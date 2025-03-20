# ClaudeOrchestra Implementation Plan

This simplified implementation plan focuses on creating a personal development tool with minimal complexity while maintaining core functionality.

## Phase 1: Core Framework (Week 1)

### Day 1-2: Project Setup

1. **Create project structure**
   ```bash
   mkdir -p claude-orchestra/{src/{agents,core,utils,config,api},storage,docs}
   cd claude-orchestra
   git init
   ```

2. **Set up virtual environment**
   ```bash
   python -m venv venv
   source venv/bin/activate
   pip install httpx pyyaml click flask
   ```

3. **Create basic configuration**
   ```bash
   # src/config/config.yml
   api:
     claude:
       api_key_env: "CLAUDE_API_KEY"
       model: "claude-3-7-sonnet-20240229"
   
   storage:
     base_path: "./storage"
   
   agents:
     product_manager:
       role: "Product Manager"
     architect:
       role: "System Architect"
     developer:
       role: "Developer"
     qa:
       role: "QA Specialist"
     writer:
       role: "Technical Writer"
   ```

### Day 3-5: Agent Framework

1. **Create base agent class**
   ```python
   # src/agents/base_agent.py
   import os
   import json
   import logging
   from typing import Dict, Any, Optional
   
   class BaseAgent:
       """Base class for all specialized agents"""
       
       def __init__(self, config: Dict[str, Any], context: Optional[Dict[str, Any]] = None):
           self.config = config
           self.context = context or {}
           self.role = config.get("role", "Generic Agent")
           self.history = []
           self.logger = logging.getLogger(f"agent.{self.role}")
       
       def update_context(self, new_context: Dict[str, Any]) -> None:
           """Update agent's context with new information"""
           self.context.update(new_context)
       
       def prepare_prompt(self, task: str, specific_context: Optional[Dict[str, Any]] = None) -> str:
           """Prepare a prompt for the agent based on task and context"""
           # Override in subclasses with specialized prompts
           pass
       
       async def execute(self, task: str, specific_context: Optional[Dict[str, Any]] = None) -> Dict[str, Any]:
           """Execute a task using the Claude API"""
           prompt = self.prepare_prompt(task, specific_context)
           
           # This would call the Claude API
           # For initial implementation, just log the prompt
           self.logger.info(f"Executing task: {task}")
           
           # Record in history
           self.history.append({
               "task": task,
               "prompt": prompt,
               "result": "Placeholder result"
           })
           
           return {"status": "success", "output": "Placeholder output"}
       
       def export_knowledge(self) -> Dict[str, Any]:
           """Export agent's knowledge for transfer to other agents"""
           return {
               "role": self.role,
               "context": self.context,
               "key_points": self.extract_key_points()
           }
       
       def extract_key_points(self) -> Dict[str, Any]:
           """Extract key information from agent history"""
           # Override in subclasses
           return {}
   ```

2. **Implement specialized agents**
   - Create subclasses for each agent type
   - Implement specialized prompt templates

### Day 6-7: CLI Interface

1. **Create CLI with Click**
   ```python
   # src/cli.py
   import click
   import os
   import asyncio
   import yaml
   import json
   from pathlib import Path
   
   # Import agent factory and other components
   
   @click.group()
   def cli():
       """ClaudeOrchestra - Personal Multi-Agent Development Framework"""
       pass
   
   @cli.command()
   @click.argument("name")
   @click.argument("description", required=False)
   def init(name, description=None):
       """Initialize a new project"""
       click.echo(f"Initializing project: {name}")
       # Create project structure
   
   @cli.command()
   @click.argument("agent_type", type=click.Choice(["pm", "architect", "developer", "qa", "writer"]))
   @click.argument("project")
   @click.argument("task")
   def agent(agent_type, project, task):
       """Run a specific agent on a task"""
       click.echo(f"Running {agent_type} agent on project {project}")
       # Execute agent
   
   if __name__ == "__main__":
       cli()
   ```

## Phase 2: Knowledge Management & API Integration (Week 2)

### Day 8-10: File-Based Knowledge Store

1. **Create project directory structure**
   ```
   storage/
     projects/
       {project_name}/
         config.yml          # Project configuration
         knowledge/
           requirements.md   # Requirements document
           architecture.md   # Architecture document
           code/             # Generated code
         history/
           {agent_type}/     # Agent interaction history
   ```

2. **Implement knowledge manager**
   ```python
   # src/core/knowledge_manager.py
   from pathlib import Path
   import yaml
   import json
   import logging
   from typing import Dict, Any, Optional, List
   
   class KnowledgeManager:
       """Manages project knowledge and artifacts"""
       
       def __init__(self, storage_path: Path):
           self.storage_path = storage_path
           self.logger = logging.getLogger("knowledge_manager")
       
       def init_project(self, name: str, description: Optional[str] = None) -> Path:
           """Initialize a new project"""
           project_dir = self.storage_path / "projects" / name
           project_dir.mkdir(parents=True, exist_ok=True)
           
           # Create subdirectories
           (project_dir / "knowledge").mkdir(exist_ok=True)
           (project_dir / "history").mkdir(exist_ok=True)
           (project_dir / "knowledge" / "code").mkdir(exist_ok=True)
           
           # Create project configuration
           config = {
               "name": name,
               "description": description or "",
               "created_at": datetime.now().isoformat(),
           }
           
           with open(project_dir / "config.yml", "w") as f:
               yaml.dump(config, f)
           
           return project_dir
       
       def save_knowledge(self, project: str, filename: str, content: str) -> Path:
           """Save knowledge artifact to project"""
           project_dir = self.storage_path / "projects" / project
           if not project_dir.exists():
               raise ValueError(f"Project {project} does not exist")
           
           file_path = project_dir / "knowledge" / filename
           with open(file_path, "w") as f:
               f.write(content)
           
           return file_path
       
       def load_knowledge(self, project: str, filename: str) -> Optional[str]:
           """Load knowledge artifact from project"""
           file_path = self.storage_path / "projects" / project / "knowledge" / filename
           if not file_path.exists():
               return None
           
           with open(file_path, "r") as f:
               return f.read()
       
       def save_agent_history(self, project: str, agent_type: str, task: str, data: Dict[str, Any]) -> Path:
           """Save agent interaction history"""
           project_dir = self.storage_path / "projects" / project
           if not project_dir.exists():
               raise ValueError(f"Project {project} does not exist")
           
           history_dir = project_dir / "history" / agent_type
           history_dir.mkdir(parents=True, exist_ok=True)
           
           timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
           file_path = history_dir / f"{timestamp}_{task}.json"
           
           with open(file_path, "w") as f:
               json.dump(data, f, indent=2)
           
           return file_path
   ```

### Day 11-14: Claude API Integration

1. **Implement Claude API client**
   ```python
   # src/api/claude_client.py
   import os
   import json
   import logging
   import httpx
   from typing import Dict, Any, Optional
   
   class ClaudeClient:
       """Client for interacting with Claude API"""
       
       def __init__(self, api_key: Optional[str] = None, model: str = "claude-3-7-sonnet-20240229"):
           self.api_key = api_key or os.environ.get("CLAUDE_API_KEY")
           if not self.api_key:
               raise ValueError("Claude API key not provided and not found in environment")
           
           self.model = model
           self.base_url = "https://api.anthropic.com/v1"
           self.client = httpx.AsyncClient(
               headers={
                   "x-api-key": self.api_key,
                   "anthropic-version": "2023-06-01",
                   "content-type": "application/json"
               }
           )
       
       async def __aenter__(self):
           return self
       
       async def __aexit__(self, exc_type, exc_val, exc_tb):
           await self.client.aclose()
       
       async def generate_response(
           self, 
           prompt: str, 
           system: Optional[str] = None,
           temperature: float = 0.7,
           max_tokens: int = 4096
       ) -> Dict[str, Any]:
           """Generate a response from Claude"""
           try:
               payload = {
                   "model": self.model,
                   "max_tokens": max_tokens,
                   "temperature": temperature,
                   "messages": [
                       {"role": "user", "content": prompt}
                   ]
               }
               
               if system:
                   payload["system"] = system
               
               response = await self.client.post(
                   f"{self.base_url}/messages",
                   json=payload
               )
               
               response.raise_for_status()
               return response.json()
               
           except Exception as e:
               logging.error(f"Error calling Claude API: {str(e)}")
               raise
   ```

2. **Update agent execution to use Claude API**

## Phase 3: Simple Web Interface (Week 3)

### Day 15-18: Basic Flask App

1. **Create simple Flask application**
   ```python
   # src/web.py
   from flask import Flask, render_template, request, jsonify, redirect, url_for
   import os
   import yaml
   import json
   from pathlib import Path
   
   # Import knowledge manager and agent components
   
   app = Flask(__name__, template_folder="templates", static_folder="static")
   
   @app.route("/")
   def index():
       """Main dashboard page"""
       # Get list of projects
       projects_dir = Path("./storage/projects")
       projects = []
       if projects_dir.exists():
           projects = [d.name for d in projects_dir.iterdir() if d.is_dir()]
       
       return render_template("index.html", projects=projects)
   
   @app.route("/project/<project_name>")
   def project_detail(project_name):
       """Project details page"""
       # Load project info
       # Display project knowledge artifacts
       return render_template("project_detail.html", project=project_name)
   
   @app.route("/agent/<agent_type>/<project_name>", methods=["GET", "POST"])
   def agent_interface(agent_type, project_name):
       """Interface for interacting with a specific agent"""
       if request.method == "POST":
           # Handle agent task submission
           task = request.form.get("task")
           # Execute agent task
           return redirect(url_for("project_detail", project_name=project_name))
       
       return render_template("agent_interface.html", 
                             agent_type=agent_type, 
                             project_name=project_name)
   
   if __name__ == "__main__":
       app.run(debug=True)
   ```

2. **Create basic HTML templates**
   - index.html - Project list dashboard
   - project_detail.html - Project details page
   - agent_interface.html - Agent interaction page

### Day 19-21: Workflow Improvements

1. **Implement context building functions**
   ```python
   # src/core/context_builder.py
   from pathlib import Path
   import json
   from typing import Dict, Any, List, Optional
   
   def build_agent_context(project: str, agent_type: str, task: str) -> Dict[str, Any]:
       """Build context for a specific agent and task"""
       base_context = load_project_context(project)
       
       # Add agent-specific context
       if agent_type == "pm":
           return build_pm_context(base_context, task)
       elif agent_type == "architect":
           return build_architect_context(base_context, task)
       # Other agent types...
       
       return base_context
   
   def load_project_context(project: str) -> Dict[str, Any]:
       """Load basic project context"""
       # Load project configuration
       # Load key knowledge artifacts
       # Return consolidated context
   ```

2. **Implement knowledge transfer functions**

## Phase 4: Project Utilities & Refinement (Week 4)

### Day 22-25: GitHub Integration

1. **Create GitHub client for code storage**
   ```python
   # src/api/github_client.py
   import os
   import logging
   import httpx
   from typing import Dict, Any, Optional, List
   
   class GitHubClient:
       """Client for interacting with GitHub API"""
       
       def __init__(self, token: Optional[str] = None):
           self.token = token or os.environ.get("GITHUB_TOKEN")
           if not self.token:
               raise ValueError("GitHub token not provided and not found in environment")
           
           self.base_url = "https://api.github.com"
           self.client = httpx.AsyncClient(
               headers={
                   "Authorization": f"token {self.token}",
                   "Accept": "application/vnd.github.v3+json"
               }
           )
       
       async def create_repository(self, name: str, description: Optional[str] = None, private: bool = False) -> Dict[str, Any]:
           """Create a new GitHub repository"""
           try:
               payload = {
                   "name": name,
                   "private": private
               }
               
               if description:
                   payload["description"] = description
               
               response = await self.client.post(
                   f"{self.base_url}/user/repos",
                   json=payload
               )
               
               response.raise_for_status()
               return response.json()
               
           except Exception as e:
               logging.error(f"Error creating GitHub repository: {str(e)}")
               raise
   ```

### Day 26-28: Final Integration & Testing

1. **Integration testing**
   - Test complete workflows
   - Refine agent prompts based on output quality
   - Fix any issues in knowledge management

2. **Documentation and setup**
   - Create comprehensive README
   - Document installation process
   - Create usage examples

## Usage Guide

### Installation

```bash
# Clone the repository
git clone https://github.com/your-username/claude-orchestra.git
cd claude-orchestra

# Set up virtual environment
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# Set environment variables
export CLAUDE_API_KEY="your-api-key"
export GITHUB_TOKEN="your-github-token"  # Optional

# Install the CLI tool
pip install -e .
```

### Basic Usage

```bash
# Initialize a new project
co init my-app "A task management application"

# Use the Product Manager agent to define requirements
co agent pm my-app "Define core user stories and requirements"

# Use the Architect agent to design the system
co agent architect my-app "Design system architecture based on requirements"

# Use the Developer agent to implement features
co agent developer my-app "Implement user authentication module"

# Use the QA agent to create tests
co agent qa my-app "Create test plan for user authentication"

# Use the Technical Writer agent to document
co agent writer my-app "Create API documentation"
```

### Web Interface

```bash
# Start the web interface
co web

# Access at http://localhost:5000
```

## Next Steps for Enhancement

1. **Improved Context Management**
   - Add semantic relevance filtering
   - Implement better context compression

2. **Advanced Agent Specialization**
   - Refine agent prompts based on usage
   - Add more specialized agent types

3. **Workflow Automation**
   - Create predefined workflow sequences
   - Add dependency tracking between tasks

4. **Enhanced UI**
   - Add knowledge visualization
   - Improve project tracking features
