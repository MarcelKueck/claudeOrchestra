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
│   │   ├── pm_agent.py     # Product Manager agent
│   │   ├── architect_agent.py # Architect agent
│   │   ├── developer_agent.py # Developer agent
│   │   ├── qa_agent.py     # QA agent
│   │   ├── writer_agent.py # Technical Writer agent
│   │   └── agent_factory.py # Agent creation factory
│   ├── api/                # API clients
│   │   ├── __init__.py
│   │   ├── claude_client.py # Claude API client
│   │   ├── claude_code_client.py # Claude Code integration
│   │   └── github_client.py # GitHub API client
│   ├── core/               # Core functionality
│   │   ├── __init__.py
│   │   ├── knowledge_manager.py # Knowledge management
│   │   ├── context_builder.py # Context building
│   │   ├── workflow_engine.py # Workflow automation
│   │   └── project_manager.py # Project management
│   ├── utils/              # Utility functions
│   │   ├── __init__.py
│   │   ├── config.py       # Configuration management
│   │   ├── file_utils.py   # File operations
│   │   └── prompt_utils.py # Prompt handling utilities
│   └── config/             # Configuration files
│       ├── default.yml     # Default configuration
│       └── templates/      # Prompt templates
│           ├── pm.txt      # Product Manager template
│           ├── architect.txt # Architect template
│           ├── developer.txt # Developer template
│           ├── qa.txt      # QA template
│           └── writer.txt  # Technical Writer template
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
│           │   ├── requirements.md
│           │   ├── architecture.md
│           │   └── code/   # Generated code
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

### 1. Agents

The `src/agents/` directory contains the specialized agent implementations. Each agent type has its own subclass that extends the base agent.

#### Base Agent

`base_agent.py` defines the common functionality for all agents:

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

#### Agent Factory

`agent_factory.py` provides a factory function for creating appropriate agent instances:

```python
def create_agent(agent_type, project_name, context=None):
    """Create an agent of the specified type for a project"""
    config = load_agent_config(agent_type)
    
    if agent_type == "pm":
        return ProductManagerAgent(config, context)
    elif agent_type == "architect":
        return ArchitectAgent(config, context)
    elif agent_type == "developer":
        return DeveloperAgent(config, context)
    elif agent_type == "qa":
        return QAAgent(config, context)
    elif agent_type == "writer":
        return TechnicalWriterAgent(config, context)
    else:
        raise ValueError(f"Unknown agent type: {agent_type}")
```

### 2. Core Functionality

The `src/core/` directory contains the central functionality of ClaudeOrchestra.

#### Knowledge Manager

`knowledge_manager.py` handles project knowledge storage and retrieval:

```python
class KnowledgeManager:
    """Manages project knowledge and artifacts"""
    
    def __init__(self, storage_path):
        self.storage_path = storage_path
        self.logger = logging.getLogger("knowledge_manager")
    
    def init_project(self, name, description=None):
        """Initialize a new project structure"""
        # Create project directories
        # Initialize configuration file
        # Return project path
        pass
    
    def save_knowledge(self, project, filename, content):
        """Save knowledge artifact to project"""
        # Save content to project knowledge directory
        # Update metadata if needed
        pass
    
    def load_knowledge(self, project, filename):
        """Load knowledge artifact from project"""
        # Read content from project knowledge directory
        # Return file content
        pass
    
    def save_agent_history(self, project, agent_type, task, data):
        """Save agent interaction history"""
        # Save interaction data to history directory
        # Update metadata
        pass
```

#### Context Builder

`context_builder.py` handles context creation for agent interactions:

```python
def build_agent_context(project, agent_type, task):
    """Build context for a specific agent and task"""
    # Load project configuration
    # Load relevant knowledge artifacts
    # Filter and format for the specific agent
    # Return context dictionary
    pass

def compress_context(context, max_tokens=8000):
    """Compress context to fit within token limits"""
    # Apply compression strategies
    # Prioritize most relevant information
    # Return compressed context
    pass
```

#### Workflow Engine

`workflow_engine.py` manages multi-agent workflows:

```python
class WorkflowEngine:
    """Manages execution of multi-agent workflows"""
    
    def __init__(self, project_name):
        self.project_name = project_name
        self.knowledge_manager = KnowledgeManager(get_storage_path())
        self.logger = logging.getLogger("workflow_engine")
    
    async def execute_workflow(self, workflow_name, params=None):
        """Execute a named workflow with optional parameters"""
        # Load workflow definition
        # Execute steps in sequence
        # Handle context transfer between agents
        # Return workflow results
        pass
    
    async def execute_workflow_step(self, step, context):
        """Execute a single workflow step"""
        # Create appropriate agent
        # Execute task
        # Process output
        # Return results for next step
        pass
```

### 3. API Clients

The `src/api/` directory contains clients for external services.

#### Claude API Client

`claude_client.py` handles communication with Claude API:

```python
class ClaudeClient:
    """Client for interacting with Claude API"""
    
    def __init__(self, api_key=None, model="claude-3-7-sonnet-20240229"):
        self.api_key = api_key or os.environ.get("CLAUDE_API_KEY")
        if not self.api_key:
            raise ValueError("Claude API key not provided and not found in environment")
        
        self.model = model
        self.base_url = "https://api.anthropic.com/v1"
        self.client = httpx.AsyncClient(headers={...})
    
    async def generate_response(self, prompt, system=None, temperature=0.7, max_tokens=4096):
        """Generate a response from Claude"""
        # Prepare request payload
        # Send request to Claude API
        # Process and return response
        pass
```

#### Claude Code Client

`claude_code_client.py` provides integration with Claude Code CLI:

```python
class ClaudeCodeClient:
    """Client for interacting with Claude Code CLI"""
    
    def __init__(self, working_dir=None):
        self.working_dir = working_dir or Path.cwd()
        # Check if Claude Code is installed
    
    async def generate_code(self, prompt, language=None, output_file=None, timeout=300):
        """Generate code using Claude Code"""
        # Prepare command
        # Execute Claude Code CLI
        # Process results
        # Return generated code
        pass
```

### 4. CLI Interface

`src/cli.py` implements the command-line interface:

```python
import click
import asyncio
from pathlib import Path

from .core.knowledge_manager import KnowledgeManager
from .agents.agent_factory import create_agent
# Other imports

@click.group()
def cli():
    """ClaudeOrchestra - Personal Multi-Agent Development Framework"""
    pass

@cli.command()
@click.argument("name")
@click.argument("description", required=False)
def init(name, description=None):
    """Initialize a new project"""
    km = KnowledgeManager(get_storage_path())
    project_dir = km.init_project(name, description)
    click.echo(f"Initialized project '{name}' at {project_dir}")

@cli.command()
@click.argument("agent_type", type=click.Choice(["pm", "architect", "developer", "qa", "writer"]))
@click.argument("project")
@click.argument("task")
def agent(agent_type, project, task):
    """Run a specific agent on a task"""
    # Create agent
    # Execute task
    # Save output
    # Print result summary
    pass

# Other CLI commands...

def main():
    cli()

if __name__ == "__main__":
    main()
```

### 5. Web Interface

`src/web.py` implements a simple Flask-based web interface:

```python
from flask import Flask, render_template, request, redirect, url_for
from pathlib import Path
import yaml
import json

# Import core components

app = Flask(__name__, 
            template_folder=str(Path(__file__).parent.parent / "templates"),
            static_folder=str(Path(__file__).parent.parent / "static"))

@app.route("/")
def index():
    """Main dashboard page"""
    # Get list of projects
    # Return dashboard template
    pass

@app.route("/project/<project_name>")
def project_detail(project_name):
    """Project details page"""
    # Load project data
    # Return project detail template
    pass

@app.route("/agent/<agent_type>/<project_name>", methods=["GET", "POST"])
def agent_interface(agent_type, project_name):
    """Interface for interacting with a specific agent"""
    # Handle form submission
    # Execute agent task
    # Display agent interface
    pass

# Other routes...

def start_web(host="127.0.0.1", port=5000, debug=False):
    """Start the web interface"""
    app.run(host=host, port=port, debug=debug)

if __name__ == "__main__":
    start_web(debug=True)
```

## Extension Points

ClaudeOrchestra is designed to be easily extended. Here are the main extension points:

### 1. Adding New Agent Types

To add a new agent type:

1. Create a new agent class in `src/agents/` that extends `BaseAgent`
2. Add prompt template in `src/config/templates/`
3. Update `agent_factory.py` to support the new agent type
4. Update CLI and web interface to include the new agent type

### 2. Adding New Knowledge Artifacts

To add support for new knowledge artifact types:

1. Update `KnowledgeManager` to handle the new artifact type
2. Extend `context_builder.py` to include the new artifacts in context
3. Update templates to display the new artifact types

### 3. Creating Custom Workflows

To create custom workflows:

1. Define workflow in YAML format
2. Load workflow definition in `WorkflowEngine`
3. Add CLI command and web interface for the new workflow

### 4. Enhancing Context Management

To improve context handling:

1. Extend `compress_context()` with more sophisticated compression techniques
2. Implement semantic search for context retrieval
3. Add better context visualization in web interface

## Configuration Files

### Default Configuration

`src/config/default.yml` contains default settings:

```yaml
# ClaudeOrchestra Default Configuration

# API Configuration
api:
  claude:
    api_key_env: "CLAUDE_API_KEY"
    model: "claude-3-7-sonnet-20240229"
  github:
    token_env: "GITHUB_TOKEN"

# Agent Configuration
agents:
  defaults:
    temperature: 0.7
    max_tokens: 4096
  
  product_manager:
    role: "Product Manager"
    template: "pm.txt"
    temperature: 0.7
  
  architect:
    role: "System Architect"
    template: "architect.txt"
    temperature: 0.7
  
  developer:
    role: "Developer"
    template: "developer.txt"
    temperature: 0.5
  
  qa:
    role: "QA Specialist"
    template: "qa.txt"
    temperature: 0.3
  
  writer:
    role: "Technical Writer"
    template: "writer.txt"
    temperature: 0.7

# Storage Configuration
storage:
  base_path: "./storage"

# Workflow Configuration
workflows:
  init:
    description: "Initialize a new project"
    steps:
      - agent: "pm"
        task: "Define core requirements for {project_name}"
      - agent: "architect"
        task: "Design system architecture based on requirements"
  
  feature:
    description: "Develop a complete feature"
    steps:
      - agent: "pm"
        task: "Define requirements for {feature_name}"
      - agent: "architect"
        task: "Design architecture for {feature_name}"
      - agent: "developer"
        task: "Implement {feature_name}"
      - agent: "qa"
        task: "Create tests for {feature_name}"
      - agent: "writer"
        task: "Document {feature_name}"
```

### Project Configuration

Each project has its own configuration file (`storage/projects/{project_name}/config.yml`):

```yaml
# Project Configuration
name: "task-manager"
description: "A simple task management application"
created_at: "2023-04-15T10:30:00Z"
last_modified: "2023-04-16T14:20:00Z"

# Project metadata
version: "0.1.0"
status: "in-progress"

# Technology stack
technologies:
  frontend: "React"
  backend: "Flask"
  database: "SQLite"

# GitHub integration
github:
  repository: "username/task-manager"
  branch: "main"

# Custom settings
settings:
  feature_flags:
    enable_authentication: true
    enable_sharing: false
```

## Development Workflow

Here's the recommended workflow for developing and extending ClaudeOrchestra:

### 1. Initial Setup

1. Clone the repository
2. Set up virtual environment and install dependencies
3. Configure environment variables
4. Run tests to ensure everything is working

### 2. Adding Features

When adding new features:

1. Create a new branch for the feature
2. Update or add necessary components
3. Add tests for the new functionality
4. Update documentation
5. Create a pull request

### 3. Testing

ClaudeOrchestra includes a test suite in the `tests/` directory:

```bash
# Run all tests
pytest

# Run specific test category
pytest tests/test_agents/

# Run with coverage
pytest --cov=src
```

### 4. Documentation

Documentation is maintained in several places:

1. `README.md` - Overview and getting started
2. Docstrings in code - API documentation
3. `docs/` directory - Detailed guides and tutorials

## Best Practices

When developing ClaudeOrchestra, follow these best practices:

### Code Structure

1. **Keep modules focused**: Each module should have a single responsibility
2. **Use clear names**: Functions and classes should have descriptive names
3. **Add docstrings**: Document all public functions and classes
4. **Use type hints**: Add Python type hints to improve code quality

### Agent Development

1. **Clear prompts**: Ensure agent prompts are clear and focused
2. **Consistent output**: Agents should produce consistent, structured output
3. **Error handling**: Handle API errors and edge cases gracefully
4. **Context efficiency**: Optimize context for token usage

### Knowledge Management

1. **Structured artifacts**: Use consistent formats for knowledge artifacts
2. **Version control**: Track changes to knowledge artifacts
3. **Clear boundaries**: Maintain separation between different types of knowledge
4. **Efficient retrieval**: Implement effective knowledge search and retrieval

## Troubleshooting

Common issues and solutions:

### API Connection Issues

```
Error: Unable to connect to Claude API
```

Solutions:
- Check CLAUDE_API_KEY environment variable
- Verify internet connection
- Check Claude API status

### Missing Dependencies

```
ImportError: No module named 'xxx'
```

Solution:
- Run `pip install -r requirements.txt`
- Check virtual environment activation

### Context Window Exceeded

```
Error: Context window limit exceeded
```

Solutions:
- Reduce context size with better filtering
- Break task into smaller components
- Use context compression techniques

### Memory Issues

```
MemoryError during large project processing
```

Solutions:
- Implement pagination for large projects
- Use lazy loading for knowledge artifacts
- Optimize data structures for memory usage
