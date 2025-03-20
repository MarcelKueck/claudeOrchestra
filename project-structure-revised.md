# ClaudeOrchestra Project Structure Guide

This guide outlines the recommended project structure for ClaudeOrchestra, with explanations of each component to help you understand and extend the system.

## Directory Structure

```
claude-orchestra/
├── README.md               # Project documentation
├── setup.py                # Package installation
├── requirements.txt        # Dependencies
├── src/                    # Source code
│   ├── __init__.py
│   ├── main.py             # Main entry point
│   ├── cli.py              # CLI implementation
│   ├── web.py              # Web interface
│   ├── agents/             # Agent implementation
│   │   ├── __init__.py
│   │   ├── base_agent.py   # Base agent class
│   │   ├── strategic/      # Strategic agents
│   │   │   ├── __init__.py
│   │   │   ├── pm_agent.py         # Product Manager agent
│   │   │   ├── architect_agent.py  # Architect agent
│   │   │   └── project_agent.py    # Project Manager agent
│   │   ├── development/    # Development agents
│   │   │   ├── __init__.py
│   │   │   ├── developer_agent.py      # Generic Developer agent
│   │   │   ├── frontend_agent.py       # Frontend Developer agent
│   │   │   ├── backend_agent.py        # Backend Developer agent
│   │   │   ├── database_agent.py       # Database Engineer agent
│   │   │   └── devops_agent.py         # DevOps Engineer agent
│   │   ├── quality/        # Quality agents
│   │   │   ├── __init__.py
│   │   │   ├── qa_agent.py          # QA agent
│   │   │   ├── security_agent.py    # Security Engineer agent
│   │   │   └── writer_agent.py      # Technical Writer agent
│   │   └── agent_factory.py # Agent creation factory
│   ├── api/                # API clients
│   │   ├── __init__.py
│   │   ├── claude_client.py      # Claude API client
│   │   ├── claude_code_client.py # Claude Code integration
│   │   └── github_client.py      # GitHub API client
│   ├── core/               # Core functionality
│   │   ├── __init__.py
│   │   ├── knowledge_manager.py  # Knowledge management
│   │   ├── context_manager.py    # Context management
│   │   ├── workflow_engine.py    # Workflow automation
│   │   └── project_manager.py    # Project management
│   ├── utils/              # Utility functions
│   │   ├── __init__.py
│   │   ├── config.py       # Configuration management
│   │   ├── file_utils.py   # File operations
│   │   └── prompt_utils.py # Prompt handling utilities
│   └── config/             # Configuration files
│       ├── default.yml     # Default configuration
│       └── templates/      # Prompt templates
│           ├── strategic/  # Strategic agent templates
│           │   ├── pm.txt          # Product Manager template
│           │   ├── architect.txt   # Architect template
│           │   └── project.txt     # Project Manager template
│           ├── development/ # Development agent templates
│           │   ├── developer.txt   # Generic Developer template
│           │   ├── frontend.txt    # Frontend Developer template
│           │   ├── backend.txt     # Backend Developer template
│           │   ├── database.txt    # Database Engineer template
│           │   └── devops.txt      # DevOps Engineer template
│           └── quality/    # Quality agent templates
│               ├── qa.txt       # QA template
│               ├── security.txt # Security Engineer template
│               └── writer.txt   # Technical Writer template
├── templates/              # Web UI templates
│   ├── base.html           # Base template
│   ├── index.html          # Dashboard
│   ├── project_detail.html # Project details
│   └── agent_interface.html # Agent interface
├── static/                 # Static web assets
│   ├── css/
│   │   └── main.css        # Main stylesheet
│   └── js/
│       └── app.js          # JavaScript for UI
├── storage/                # Data storage (gitignored)
│   └── projects/           # Project data
│       └── {project_name}/ # Individual project folders
│           ├── config.yml  # Project configuration
│           ├── knowledge/  # Knowledge artifacts
│           │   ├── requirements.md    # Requirements document
│           │   ├── architecture.md    # System architecture
│           │   ├── api_design.md      # API specifications
│           │   ├── data_model.md      # Data model
│           │   ├── ui_design.md       # UI specifications
│           │   ├── deployment.md      # Deployment configuration
│           │   ├── security.md        # Security considerations 
│           │   ├── decisions.json     # Decision log
│           │   └── code/              # Generated code
│           │       ├── frontend/      # Frontend code
│           │       ├── backend/       # Backend code 
│           │       ├── database/      # Database scripts
│           │       └── devops/        # Infrastructure code
│           └── history/    # Agent history
│               └── {agent_type}/
│                   └── {timestamp}_{task}.json
└── tests/                  # Unit tests
    ├── __init__.py
    ├── test_agents/        # Agent tests
    ├── test_api/           # API client tests
    └── test_core/          # Core functionality tests
```

## Key Components Explained

### 1. Base Agent Framework

The `src/agents/base_agent.py` defines the common functionality for all agents:

```python
class BaseAgent:
    """Base class for all specialized agents"""
    
    def __init__(self, config, context=None):
        """Initialize agent with configuration and optional context"""
        self.config = config
        self.context = context or {}
        self.role = config.get("role", "Generic Agent")
        self.history = []
        self.logger = logging.getLogger(f"agent.{self.role}")
    
    def prepare_prompt(self, task, specific_context=None):
        """Prepare a prompt for the agent based on task and context"""
        # Base implementation - override in subclasses
        pass
    
    async def execute(self, task, specific_context=None):
        """Execute a task using the Claude API"""
        prompt = self.prepare_prompt(task, specific_context)
        
        # Call Claude API with prompt
        # Process response
        # Return structured result
        pass
    
    def export_knowledge(self):
        """Export agent's knowledge for transfer to other agents"""
        # Extract key information from history
        # Format for transfer to other agents
        pass
```

### 2. Specialized Agent Types

ClaudeOrchestra organizes agents into three categories:

#### Strategic Agents

Located in `src/agents/strategic/`, these agents focus on planning and design:

- **Product Manager Agent**: Requirements gathering and feature definition
- **Architect Agent**: System design and technical decisions
- **Project Manager Agent**: Task breakdown and coordination

Example implementation for a strategic agent:

```python
from ..base_agent import BaseAgent
from typing import Dict, Any, Optional

class ProductManagerAgent(BaseAgent):
    """Agent specialized in product management and requirements gathering"""
    
    def prepare_prompt(self, task, specific_context=None):
        """Prepare product manager specific prompt"""
        working_context = self.context.copy()
        if specific_context:
            working_context.update(specific_context)
        
        # Get project info and existing requirements
        project_summary = working_context.get("project_summary", "")
        existing_requirements = working_context.get("existing_requirements", "")
        
        # PM-specific template
        prompt = f"""
        You are a Product Manager agent specializing in software requirements analysis.
        
        PROJECT CONTEXT:
        {project_summary}
        
        CURRENT KNOWLEDGE:
        {existing_requirements}
        
        TASK:
        {task}
        
        Your response should include:
        1. Project Summary
        2. User Personas
        3. Prioritized Requirements
        4. Open Questions (if any)
        """
        
        return prompt
```

#### Development Agents

Located in `src/agents/development/`, these agents focus on implementation:

- **Frontend Developer Agent**: UI/UX implementation
- **Backend Developer Agent**: API and service implementation
- **Database Engineer Agent**: Schema design and data management
- **DevOps Engineer Agent**: Deployment and infrastructure
- **Generic Developer Agent**: Combined development capabilities for simple projects

Example implementation for a development agent:

```python
from ..base_agent import BaseAgent
from typing import Dict, Any, Optional

class FrontendDeveloperAgent(BaseAgent):
    """Agent specialized in frontend development"""
    
    def prepare_prompt(self, task, specific_context=None):
        """Prepare frontend-specific prompt"""
        working_context = self.context.copy()
        if specific_context:
            working_context.update(specific_context)
        
        # Get relevant context for frontend development
        project_summary = working_context.get("project_summary", "")
        requirements = working_context.get("requirements", "")
        architecture = working_context.get("architecture", "")
        ui_design = working_context.get("ui_design", "")
        
        # Frontend-specific template
        prompt = f"""
        You are a Frontend Developer agent specializing in UI/UX implementation.
        
        PROJECT CONTEXT:
        {project_summary}
        
        REQUIREMENTS:
        {requirements}
        
        ARCHITECTURE:
        {architecture}
        
        DESIGN GUIDELINES:
        {ui_design}
        
        TASK:
        {task}
        
        Your response should include:
        1. Component Design
        2. UI Implementation
        3. State Management
        4. API Integration
        5. Responsive Design Considerations
        """
        
        return prompt
```

#### Quality Agents

Located in `src/agents/quality/`, these agents focus on quality assurance:

- **QA Agent**: Testing and quality verification
- **Security Engineer Agent**: Security assessment and implementation
- **Technical Writer Agent**: Documentation generation

Example implementation for a quality agent:

```python
from ..base_agent import BaseAgent
from typing import Dict, Any, Optional

class SecurityEngineerAgent(BaseAgent):
    """Agent specialized in security engineering"""
    
    def prepare_prompt(self, task, specific_context=None):
        """Prepare security-specific prompt"""
        working_context = self.context.copy()
        if specific_context:
            working_context.update(specific_context)
        
        # Get relevant context for security assessment
        project_summary = working_context.get("project_summary", "")
        requirements = working_context.get("requirements", "")
        architecture = working_context.get("architecture", "")
        api_design = working_context.get("api_design", "")
        data_model = working_context.get("data_model", "")
        
        # Security-specific template
        prompt = f"""
        You are a Security Engineer agent specializing in application security.
        
        PROJECT CONTEXT:
        {project_summary}
        
        REQUIREMENTS:
        {requirements}
        
        ARCHITECTURE:
        {architecture}
        
        API DESIGN:
        {api_design}
        
        DATA MODEL:
        {data_model}
        
        TASK:
        {task}
        
        Your response should include:
        1. Security Assessment
        2. Recommended Controls
        3. Implementation Guidance
        4. Testing Approaches
        5. Compliance Considerations
        """
        
        return prompt
```

### 3. Agent Factory

`src/agents/agent_factory.py` creates the appropriate agent based on type:

```python
def create_agent(agent_type, project_name, context=None):
    """Create an agent of the specified type for a project"""
    config = load_agent_config(agent_type)
    context = context or {}
    
    # Add project info to context
    project_info = load_project_info(project_name)
    if project_info:
        context["project_info"] = project_info
    
    # Strategic agents
    if agent_type == "pm":
        from .strategic.pm_agent import ProductManagerAgent
        return ProductManagerAgent(config, context)
    elif agent_type == "architect":
        from .strategic.architect_agent import ArchitectAgent
        return ArchitectAgent(config, context)
    elif agent_type == "project":
        from .strategic.project_agent import ProjectManagerAgent
        return ProjectManagerAgent(config, context)
    
    # Development agents
    elif agent_type == "developer":
        from .development.developer_agent import DeveloperAgent
        return DeveloperAgent(config, context)
    elif agent_type == "frontend":
        from .development.frontend_agent import FrontendDeveloperAgent
        return FrontendDeveloperAgent(config, context)
    elif agent_type == "backend":
        from .development.backend_agent import BackendDeveloperAgent
        return BackendDeveloperAgent(config, context)
    elif agent_type == "db":
        from .development.database_agent import DatabaseEngineerAgent
        return DatabaseEngineerAgent(config, context)
    elif agent_type == "devops":
        from .development.devops_agent import DevOpsEngineerAgent
        return DevOpsEngineerAgent(config, context)
    
    # Quality agents
    elif agent_type == "qa":
        from .quality.qa_agent import QAAgent
        return QAAgent(config, context)
    elif agent_type == "security":
        from .quality.security_agent import SecurityEngineerAgent
        return SecurityEngineerAgent(config, context)
    elif agent_type == "writer":
        from .quality.writer_agent import TechnicalWriterAgent
        return TechnicalWriterAgent(config, context)
    
    else:
        raise ValueError(f"Unknown agent type: {agent_type}")
```

### 4. Knowledge Management

`src/core/knowledge_manager.py` handles project knowledge storage and retrieval:

```python
class KnowledgeManager:
    """Manages project knowledge and artifacts"""
    
    def __init__(self, storage_path):
        self.storage_path = storage_path
        self.logger = logging.getLogger("knowledge_manager")
    
    def init_project(self, name, description=None):
        """Initialize a new project structure"""
        project_dir = self.storage_path / "projects" / name
        project_dir.mkdir(parents=True, exist_ok=True)
        
        # Create subdirectories
        (project_dir / "knowledge").mkdir(exist_ok=True)
        (project_dir / "history").mkdir(exist_ok=True)
        
        # Create code directories
        code_dir = project_dir / "knowledge" / "code"
        code_dir.mkdir(exist_ok=True)
        (code_dir / "frontend").mkdir(exist_ok=True)
        (code_dir / "backend").mkdir(exist_ok=True)
        (code_dir / "database").mkdir(exist_ok=True)
        (code_dir / "devops").mkdir(exist_ok=True)
        
        # Create project configuration
        config = {
            "name": name,
            "description": description or "",
            "created_at": datetime.now().isoformat(),
            "complexity": "simple"  # Start with simple complexity
        }
        
        with open(project_dir / "config.yml", "w") as f:
            yaml.dump(config, f)
        
        return project_dir
    
    def save_knowledge(self, project, filename, content):
        """Save knowledge artifact to project"""
        file_path = self.storage_path / "projects" / project / "knowledge" / filename
        
        # Ensure directory exists (for nested paths)
        file_path.parent.mkdir(parents=True, exist_ok=True)
        
        with open(file_path, "w") as f:
            f.write(content)
        
        return file_path
    
    def load_knowledge(self, project, filename):
        """Load knowledge artifact from project"""
        file_path = self.storage_path / "projects" / project / "knowledge" / filename
        
        if not file_path.exists():
            return None
        
        with open(file_path, "r") as f:
            return f.read()
```

### 5. Context Management

`src/core/context_manager.py` handles context building and optimization:

```python
def build_agent_context(project, agent_type, task):
    """Build context for a specific agent and task"""
    km = get_knowledge_manager()
    
    # Load basic project info
    project_info = load_project_info(project)
    
    # Initialize context with project info and task
    context = {
        "project_info": project_info,
        "task": task
    }
    
    # Common artifacts that most agents need
    requirements = km.load_knowledge(project, "requirements.md")
    if requirements:
        context["requirements"] = requirements
    
    # Agent-specific context building
    if agent_type in ["architect", "frontend", "backend", "db", "security", "devops"]:
        # These agents need architecture information
        architecture = km.load_knowledge(project, "architecture.md")
        if architecture:
            context["architecture"] = architecture
    
    # Development-specific artifacts
    if agent_type == "frontend":
        ui_design = km.load_knowledge(project, "ui_design.md")
        if ui_design:
            context["ui_design"] = ui_design
        
        api_design = km.load_knowledge(project, "api_design.md")
        if api_design:
            context["api_design"] = api_design
    
    elif agent_type == "backend":
        api_design = km.load_knowledge(project, "api_design.md")
        if api_design:
            context["api_design"] = api_design
        
        data_model = km.load_knowledge(project, "data_model.md")
        if data_model:
            context["data_model"] = data_model
    
    elif agent_type == "db":
        data_model = km.load_knowledge(project, "data_model.md")
        if data_model:
            context["data_model"] = data_model
    
    # Quality-specific artifacts
    elif agent_type == "security":
        security = km.load_knowledge(project, "security.md")
        if security:
            context["security"] = security
        
        # Security needs to know about APIs, data, and deployment
        api_design = km.load_knowledge(project, "api_design.md")
        if api_design:
            context["api_design"] = api_design
        
        data_model = km.load_knowledge(project, "data_model.md")
        if data_model:
            context["data_model"] = data_model
        
        deployment = km.load_knowledge(project, "deployment.md")
        if deployment:
            context["deployment"] = deployment
    
    # Compress context to fit in token limits
    return compress_context(context)
```

### 6. Workflow Engine

`src/core/workflow_engine.py` manages multi-agent workflows:

```python
class WorkflowEngine:
    """Manages execution of multi-agent workflows"""
    
    def __init__(self, config_path, knowledge_manager):
        self.config_path = config_path
        self.knowledge_manager = knowledge_manager
        self.logger = logging.getLogger("workflow_engine")
        
        # Load workflow definitions
        with open(config_path, "r") as f:
            config = yaml.safe_load(f)
            self.workflows = config.get("workflows", {})
    
    async def execute_workflow(self, workflow_name, project, params=None):
        """Execute a named workflow with optional parameters"""
        if workflow_name not in self.workflows:
            raise ValueError(f"Workflow {workflow_name} not found")
        
        workflow_def = self.workflows[workflow_name]
        steps = workflow_def.get("steps", [])
        results = []
        context = {}
        
        # Initialize params
        params = params or {}
        
        # Add project name to params
        params["project_name"] = project
        
        # Execute each step in sequence
        for step in steps:
            agent_type = step["agent"]
            task_template = step["task"]
            
            # Apply parameters to task template
            task = task_template.format(**params)
            
            # Create context for this agent
            agent_context = self.build_agent_context(project, agent_type, context)
            
            # Create and execute agent
            agent = create_agent(agent_type, project, agent_context)
            result = await agent.execute(task)
            
            # Save result to knowledge store
            artifact_path = step.get("artifact")
            if artifact_path and result.get("output"):
                self.knowledge_manager.save_knowledge(
                    project, 
                    artifact_path.format(**params), 
                    result["output"]
                )
            
            # Update context for next step
            context.update(agent.export_knowledge())
            
            # Add to results
            results.append({
                "agent": agent_type,
                "task": task,
                "result": result
            })
        
        return {
            "workflow": workflow_name,
            "project": project,
            "steps": results
        }
```

### 7. CLI Interface

`src/cli.py` implements the command-line interface:

```python
import click
import asyncio
import yaml
import json
from pathlib import Path

@click.group()
def cli():
    """ClaudeOrchestra - Multi-Agent Development Framework"""
    pass

@cli.command()
@click.argument("name")
@click.argument("description", required=False)
def init(name, description=None):
    """Initialize a new project"""
    km = get_knowledge_manager()
    project_dir = km.init_project(name, description)
    click.echo(f"Initialized project '{name}' at {project_dir}")

@cli.command()
@click.argument("agent_type", type=click.Choice([
    "pm", "architect", "project", 
    "frontend", "backend", "db", "devops", "developer",
    "qa", "security", "writer"
]))
@click.argument("project")
@click.argument("task")
def agent(agent_type, project, task):
    """Run a specific agent on a task"""
    # Ensure project exists
    km = get_knowledge_manager()
    project_dir = Path(km.storage_path) / "projects" / project
    if not project_dir.exists():
        click.echo(f"Project '{project}' not found")
        return
    
    # Build context
    context = build_agent_context(project, agent_type, task)
    
    # Create and execute agent
    agent = create_agent(agent_type, project, context)
    result = asyncio.run(agent.execute(task))
    
    # Save output if successful
    if result.get("status") == "success" and result.get("output"):
        # Determine appropriate artifact name based on agent type
        if agent_type == "pm":
            artifact = "requirements.md"
        elif agent_type == "architect":
            artifact = "architecture.md"
        elif agent_type == "frontend":
            artifact = "ui_design.md"
        elif agent_type == "backend":
            artifact = "api_design.md"
        elif agent_type == "db":
            artifact = "data_model.md"
        elif agent_type == "security":
            artifact = "security.md"
        elif agent_type == "devops":
            artifact = "deployment.md"
        elif agent_type == "qa":
            artifact = "tests.md"
        elif agent_type == "writer":
            artifact = "documentation.md"
        else:
            artifact = f"{agent_type}_output.md"
        
        km.save_knowledge(project, artifact, result["output"])
        click.echo(f"Saved output to {artifact}")
    
    # Display summary
    click.echo(f"\nTask: {task}")
    click.echo(f"Status: {result.get('status')}")
    
    # Show excerpt of output
    output = result.get("output", "")
    if output:
        excerpt = output[:200] + "..." if len(output) > 200 else output
        click.echo(f"\nOutput excerpt:\n{excerpt}")
```

### 8. Web Interface

`src/web.py` implements a simple Flask-based web interface:

```python
from flask import Flask, render_template, request, redirect, url_for, jsonify
from pathlib import Path
import yaml
import json

# Import components
from .core.knowledge_manager import get_knowledge_manager
from .core.context_manager import build_agent_context
from .agents.agent_factory import create_agent

app = Flask(__name__, 
            template_folder="../templates",
            static_folder="../static")

@app.route("/")
def index():
    """Main dashboard page"""
    km = get_knowledge_manager()
    projects_dir = Path(km.storage_path) / "projects"
    
    # Get list of projects
    projects = []
    if projects_dir.exists():
        for project_dir in projects_dir.glob("*"):
            if project_dir.is_dir():
                config_file = project_dir / "config.yml"
                if config_file.exists():
                    with open(config_file, "r") as f:
                        config = yaml.safe_load(f)
                        projects.append({
                            "name": project_dir.name,
                            "description": config.get("description", ""),
                            "created_at": config.get("created_at", ""),
                            "complexity": config.get("complexity", "simple")
                        })
    
    return render_template("index.html", projects=projects)

@app.route("/project/<project_name>")
def project_detail(project_name):
    """Project details page"""
    km = get_knowledge_manager()
    project_dir = Path(km.storage_path) / "projects" / project_name
    
    if not project_dir.exists():
        return "Project not found", 404
    
    # Load project configuration
    config_file = project_dir / "config.yml"
    if config_file.exists():
        with open(config_file, "r") as f:
            config = yaml.safe_load(f)
    else:
        config = {"name": project_name}
    
    # Get knowledge artifacts
    knowledge_dir = project_dir / "knowledge"
    artifacts = []
    
    if knowledge_dir.exists():
        for artifact_path in knowledge_dir.glob("**/*.*"):
            if artifact_path.is_file():
                rel_path = artifact_path.relative_to(knowledge_dir)
                artifacts.append(str(rel_path))
    
    # Get agent history
    history_dir = project_dir / "history"
    history = {}
    
    if history_dir.exists():
        for agent_dir in history_dir.glob("*"):
            if agent_dir.is_dir():
                agent_type = agent_dir.name
                history[agent_type] = []
                
                for history_file in sorted(agent_dir.glob("*.json"), reverse=True)[:5]:
                    with open(history_file, "r") as f:
                        entry = json.load(f)
                        history[agent_type].append({
                            "timestamp": history_file.stem.split("_")[0],
                            "task": entry.get("task", ""),
                            "status": entry.get("status", "")
                        })
    
    return render_template("project_detail.html", 
                          project=config,
                          artifacts=artifacts,
                          history=history)

@app.route("/agent/<agent_type>/<project_name>", methods=["GET", "POST"])
def agent_interface(agent_type, project_name):
    """Interface for interacting with a specific agent"""
    km = get_knowledge_manager()
    
    if request.method == "POST":
        task = request.form.get("task")
        
        # Build context and execute agent
        context = build_agent_context(project_name, agent_type, task)
        agent = create_agent(agent_type, project_name, context)
        
        # Execute in background thread (or could use Celery for production)
        # For now, simplified synchronous execution
        result = asyncio.run(agent.execute(task))
        
        # Save result
        if result.get("status") == "success" and result.get("output"):
            if agent_type == "pm":
                artifact = "requirements.md"
            elif agent_type == "architect":
                artifact = "architecture.md"
            elif agent_type == "frontend":
                artifact = "ui_design.md"
            elif agent_type == "backend":
                artifact = "api_design.md"
            elif agent_type == "db":
                artifact = "data_model.md"
            elif agent_type == "security":
                artifact = "security.md"
            elif agent_type == "devops":
                artifact = "deployment.md"
            elif agent_type == "qa":
                artifact = "tests.md"
            elif agent_type == "writer":
                artifact = "documentation.md"
            else:
                artifact = f"{agent_type}_output.md"
            
            km.save_knowledge(project_name, artifact, result["output"])
        
        return redirect(url_for("project_detail", project_name=project_name))
    
    # For GET request, show agent interface
    project_dir = Path(km.storage_path) / "projects" / project_name
    if not project_dir.exists():
        return "Project not found", 404
    
    # Load project config
    config_file = project_dir / "config.yml"
    if config_file.exists():
        with open(config_file, "r") as f:
            config = yaml.safe_load(f)
    else:
        config = {"name": project_name}
    
    # Load agent history
    history_dir = project_dir / "history" / agent_type
    history = []
    
    if history_dir.exists():
        for history_file in sorted(history_dir.glob("*.json"), reverse=True)[:5]:
            with open(history_file, "r") as f:
                entry = json.load(f)
                history.append(entry)
    
    return render_template("agent_interface.html",
                          project=config,
                          agent_type=agent_type,
                          history=history)
```

## Extension Points

ClaudeOrchestra is designed to be easily extended. Here are the main extension points:

### 1. Adding New Agent Types

To add a new specialized agent:

1. Create a new agent class in the appropriate directory (`strategic/`, `development/`, or `quality/`)
2. Create a prompt template in `src/config/templates/`
3. Update `agent_factory.py` to support the new agent type
4. Update CLI and web interface to include the new agent

### 2. Customizing for Specific Domains

To adapt ClaudeOrchestra for a specific domain (e.g., e-commerce, healthcare):

1. Create domain-specific prompt templates with industry knowledge
2. Add domain-specific artifacts to the knowledge structure
3. Define specialized workflows for common domain tasks
4. Create domain-specific code templates and examples

### 3. Scaling for Enterprise Use

To enhance ClaudeOrchestra for enterprise use:

1. Implement more sophisticated knowledge management (vector database)
2. Add multi-user support and access controls
3. Enhance security features and compliance tracking
4. Integrate with enterprise CI/CD pipelines and tools
5. Add audit logs and governance features

## Best Practices

When developing and extending ClaudeOrchestra:

### Code Structure

1. **Keep agents focused**: Each agent should have a single, clear responsibility
2. **Use consistent prompts**: Maintain a similar structure across agent prompts
3. **Layer context appropriately**: More specialized agents need more specific context
4. **Handle errors gracefully**: Implement robust error handling and recovery

### Knowledge Management

1. **Maintain clear boundaries**: Separate different types of knowledge
2. **Version knowledge artifacts**: Track changes to knowledge over time
3. **Compress context efficiently**: Only include relevant information for each agent
4. **Build appropriate context**: Tailor context to each agent's specific needs

### User Experience

1. **Scale with project complexity**: Start simple and add complexity as needed
2. **Maintain consistent interfaces**: Keep CLI and web interfaces consistent
3. **Provide clear feedback**: Always show what agents are doing and their results
4. **Support exploration**: Make it easy to browse and search project knowledge
