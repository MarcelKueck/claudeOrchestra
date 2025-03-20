# ClaudeOrchestra CLI Reference Guide

This guide provides detailed information on using the ClaudeOrchestra command-line interface (CLI) for your personal development workflow.

## Installation

After setting up the ClaudeOrchestra project, you can install the CLI tool:

```bash
# From the project directory
pip install -e .
```

This makes the `co` command available in your environment.

## Environment Configuration

ClaudeOrchestra requires the following environment variables:

```bash
# Required
export CLAUDE_API_KEY="your-claude-api-key"

# Optional
export GITHUB_TOKEN="your-github-token"  # For GitHub integration
export CO_STORAGE_PATH="/path/to/storage"  # Custom storage location (default: ./storage)
```

You can add these to your `.bashrc` or `.zshrc` file for persistence.

## Basic Commands

### Project Management

#### Initialize a new project

```bash
co init PROJECT_NAME [DESCRIPTION]
```

Creates a new project with the specified name and optional description.

**Examples:**
```bash
co init task-manager "A simple task management application"
co init personal-blog
```

#### List projects

```bash
co list
```

Shows all projects in your ClaudeOrchestra storage.

#### Show project details

```bash
co show PROJECT_NAME
```

Displays details about a specific project, including available knowledge artifacts.

**Example:**
```bash
co show task-manager
```

### Working with Agents

#### Run an agent on a task

```bash
co agent AGENT_TYPE PROJECT_NAME TASK
```

Executes a specific agent on a project with a given task.

**Available agent types:**

Strategic Agents:
- `pm` - Product Manager
- `architect` - System Architect
- `project` - Project Manager

Development Agents:
- `frontend` - Frontend Developer
- `backend` - Backend Developer
- `db` - Database Engineer
- `devops` - DevOps Engineer

Quality Agents:
- `qa` - QA Specialist
- `security` - Security Engineer
- `writer` - Technical Writer

Generic Agent (for simple projects):
- `developer` - General Developer (combines frontend/backend/db capabilities)

**Examples:**
```bash
co agent pm task-manager "Define user stories for task management"
co agent architect task-manager "Design system architecture"
co agent frontend task-manager "Design login interface"
co agent backend task-manager "Implement user authentication API"
co agent db task-manager "Design database schema for users"
co agent security task-manager "Review authentication implementation"
```

#### View agent history

```bash
co history AGENT_TYPE PROJECT_NAME [--limit N]
```

Shows the interaction history for a specific agent on a project.

**Example:**
```bash
co history backend task-manager --limit 5
```

### Knowledge Management

#### View knowledge artifact

```bash
co knowledge PROJECT_NAME ARTIFACT_NAME
```

Displays the content of a knowledge artifact.

**Example:**
```bash
co knowledge task-manager requirements.md
co knowledge task-manager api_design.md
co knowledge task-manager code/backend/auth.js
```

#### List knowledge artifacts

```bash
co knowledge PROJECT_NAME --list
```

Lists all knowledge artifacts for a project.

**Example:**
```bash
co knowledge task-manager --list
```

#### Search knowledge

```bash
co search PROJECT_NAME QUERY
```

Searches project knowledge for a specific query.

**Example:**
```bash
co search task-manager "authentication"
```

### Workflow Automation

#### Run a predefined workflow

```bash
co workflow WORKFLOW_NAME PROJECT_NAME [--params KEY=VALUE]
```

Executes a predefined sequence of agent tasks.

**Built-in workflows:**
- `init` - Initial project setup (PM → Architect)
- `feature` - Complete feature development (PM → Architect → Development Agents → QA → Writer)
- `api` - API development workflow (Architect → Backend → DB → QA)
- `frontend` - Frontend component workflow (Architect → Frontend → QA)
- `security` - Security review workflow (Security → Backend → Frontend → DevOps)
- `refactor` - Code refactoring (Developer → QA)

**Project Complexity Based Workflows:**
- `simple` - For simple projects (PM → Developer → QA)
- `standard` - For standard projects (PM → Architect → Frontend → Backend → QA → Writer)
- `advanced` - For advanced projects (All agents except Security)
- `enterprise` - For enterprise projects (All specialized agents)

**Example:**
```bash
co workflow init task-manager
co workflow feature task-manager --params feature_name="user authentication"
```

#### Define a custom workflow

```bash
co workflow define WORKFLOW_NAME --file WORKFLOW_FILE
```

Defines a custom workflow from a YAML file.

**Example workflow file (feature.yml):**
```yaml
name: feature
description: "Develop a new feature"
steps:
  - agent: pm
    task: "Define requirements for {feature_name}"
    artifact: "features/{feature_name}_requirements.md"
  - agent: architect
    task: "Design architecture for {feature_name}"
    artifact: "features/{feature_name}_architecture.md"
  - agent: frontend
    task: "Design UI for {feature_name}"
    artifact: "features/{feature_name}_ui.md"
  - agent: backend
    task: "Design API for {feature_name}"
    artifact: "features/{feature_name}_api.md"
  - agent: db
    task: "Design database changes for {feature_name}"
    artifact: "features/{feature_name}_db.md"
  - agent: backend
    task: "Implement API for {feature_name}"
    artifact: "code/backend/{feature_name}.js"
  - agent: frontend
    task: "Implement UI for {feature_name}"
    artifact: "code/frontend/{feature_name}.jsx"
  - agent: db
    task: "Implement migrations for {feature_name}"
    artifact: "code/database/{feature_name}_migration.sql"
  - agent: qa
    task: "Create tests for {feature_name}"
    artifact: "tests/{feature_name}_tests.md"
  - agent: security
    task: "Review security for {feature_name}"
    artifact: "security/{feature_name}_review.md"
  - agent: writer
    task: "Document {feature_name}"
    artifact: "docs/{feature_name}.md"
```

**Example usage:**
```bash
co workflow define custom-feature --file feature.yml
co workflow custom-feature task-manager --params feature_name="user authentication"
```

### Code Generation

#### Generate code with Claude Code

```bash
co code PROJECT_NAME [--component COMPONENT] [--output FILE] "PROMPT"
```

Generates code using Claude Code CLI with project context.

**Example:**
```bash
co code task-manager --component auth --output code/backend/auth.js "Implement user authentication using JWT"
```

#### Generate code for specific agent domain

```bash
co code-specialized AGENT_TYPE PROJECT_NAME [--output FILE] "PROMPT"
```

Generates code using a specialized agent's knowledge context.

**Example:**
```bash
co code-specialized frontend task-manager --output code/frontend/login.jsx "Create login form component"
co code-specialized backend task-manager --output code/backend/auth.js "Implement authentication middleware"
co code-specialized db task-manager --output code/database/user_schema.sql "Create user table schema"
```

### GitHub Integration

#### Initialize GitHub repository

```bash
co github init PROJECT_NAME [--private]
```

Creates a GitHub repository for the project.

**Example:**
```bash
co github init task-manager --private
```

#### Commit code to GitHub

```bash
co github commit PROJECT_NAME --message "COMMIT_MESSAGE"
```

Commits and pushes changes to the GitHub repository.

**Example:**
```bash
co github commit task-manager --message "Implement user authentication"
```

### Web Interface

#### Start the web interface

```bash
co web [--port PORT]
```

Starts the web interface for ClaudeOrchestra.

**Example:**
```bash
co web --port 8080
```

## Advanced Usage

### Project Complexity Management

#### Detect project complexity

```bash
co analyze PROJECT_NAME
```

Analyzes the project and determines its complexity level.

**Example:**
```bash
co analyze task-manager
```

This will output something like:
```
Project complexity: advanced
Recommended agents: pm, architect, project, frontend, backend, db, qa, writer
```

#### Set project complexity

```bash
co config PROJECT_NAME set complexity LEVEL
```

Manually sets the project complexity level.

**Example:**
```bash
co config task-manager set complexity enterprise
```

### Agent Prompt Customization

You can customize agent prompts by creating a prompt template file:

```bash
co agent config AGENT_TYPE --template FILE
```

**Example template file (pm_template.txt):**
```
You are a Product Manager agent specializing in software requirements gathering.

PROJECT CONTEXT:
{project_summary}

TASK:
{task}

Your goal is to create clear, actionable requirements that address user needs.

APPROACH:
1. Understand the core problem being solved
2. Identify key user personas
3. Create user stories in the format: "As a [user], I want [action] so that [benefit]"
4. Prioritize features using the MoSCoW method

Your response should include:
1. Project Overview
2. User Personas
3. User Stories
4. Functional Requirements (Must/Should/Could/Won't have)
5. Non-Functional Requirements
6. Open Questions (if any)
```

**Usage:**
```bash
co agent config pm --template pm_template.txt
co agent pm task-manager "Define requirements" --use-template
```

### Context Management

#### Manually add context

```bash
co context add PROJECT_NAME --file FILE --name NAME
```

Adds a context file to the project's knowledge base.

**Example:**
```bash
co context add task-manager --file external_requirements.md --name additional_requirements.md
```

#### View current context

```bash
co context show PROJECT_NAME [AGENT_TYPE] [--task TASK]
```

Shows the context that would be provided to an agent for a specific task.

**Example:**
```bash
co context show task-manager backend --task "Implement login form"
```

### Project Configuration

#### Edit project configuration

```bash
co config PROJECT_NAME edit
```

Opens the project configuration file in your default editor.

**Example:**
```bash
co config task-manager edit
```

#### Set a configuration value

```bash
co config PROJECT_NAME set KEY VALUE
```

Sets a specific configuration value for a project.

**Example:**
```bash
co config task-manager set technology_stack "Node.js,Express,React,MongoDB"
```

## Specialized Development Commands

### Frontend Development

```bash
co frontend PROJECT_NAME COMPONENT [--framework FRAMEWORK]
```

Generate a frontend component with the Frontend Developer agent.

**Example:**
```bash
co frontend task-manager UserProfile --framework React
```

### Backend Development

```bash
co backend PROJECT_NAME ENDPOINT [--method METHOD]
```

Generate a backend API endpoint with the Backend Developer agent.

**Example:**
```bash
co backend task-manager /api/users --method POST
```

### Database Development

```bash
co database PROJECT_NAME ENTITY [--type TYPE]
```

Generate database schema for an entity with the Database Engineer agent.

**Example:**
```bash
co database task-manager User --type schema
```

### DevOps Commands

```bash
co devops PROJECT_NAME [--type TYPE]
```

Generate DevOps configuration with the DevOps Engineer agent.

**Example:**
```bash
co devops task-manager --type docker
```

## Security Commands

```bash
co security PROJECT_NAME [--scope SCOPE]
```

Run a security review with the Security Engineer agent.

**Example:**
```bash
co security task-manager --scope authentication
```

## Tips for Effective Use

1. **Match agent specialization to project complexity**:
   - For simple projects, use generic agents (pm, developer, qa)
   - For complex projects, use specialized agents (frontend, backend, db, etc.)

2. **Start with strategic agents**:
   - Always begin with the Product Manager agent for requirements
   - Use the Architect agent for technical design before implementation
   - Consider the Project Manager agent for breaking down complex work

3. **Maintain knowledge continuity**:
   - Use workflows for consistent knowledge transfer between agents
   - Review knowledge artifacts after each agent interaction
   - Use search to find relevant existing knowledge

4. **Leverage specialized workflows**:
   - Use predefined workflows for common development patterns
   - Create custom workflows for your specific project needs
   - Use complexity-based workflows for appropriate agent selection

5. **Customize agent prompts**:
   - Tailor agent prompts to your specific domain and preferences
   - Add domain-specific guidance to agent templates
   - Refine prompts based on the quality of agent outputs

6. **Combine with manual editing**:
   - Use the generated content as a starting point
   - Refine critical artifacts manually when needed
   - Commit your changes to maintain version history

7. **Scale appropriately**:
   - Start simple and add complexity as needed
   - Use the analyze command to determine appropriate scaling
   - Adjust agent specialization based on project needs

8. **Integrate with your tools**:
   - Use GitHub integration for version control
   - Connect with your existing development environment
   - Incorporate with your preferred project management tools
