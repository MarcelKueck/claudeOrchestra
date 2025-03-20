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
- `pm` - Product Manager
- `architect` - System Architect
- `developer` - Developer
- `qa` - QA Specialist
- `writer` - Technical Writer

**Examples:**
```bash
co agent pm task-manager "Define user stories for task management"
co agent architect task-manager "Design system architecture"
co agent developer task-manager "Implement user authentication"
```

#### View agent history

```bash
co history AGENT_TYPE PROJECT_NAME [--limit N]
```

Shows the interaction history for a specific agent on a project.

**Example:**
```bash
co history developer task-manager --limit 5
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
co workflow WORKFLOW_NAME PROJECT_NAME
```

Executes a predefined sequence of agent tasks.

**Built-in workflows:**
- `init` - Initial project setup (PM → Architect)
- `feature` - Complete feature development (PM → Architect → Developer → QA → Writer)
- `refactor` - Code refactoring (Developer → QA)

**Example:**
```bash
co workflow init task-manager
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
  - agent: architect
    task: "Design architecture for {feature_name}"
  - agent: developer
    task: "Implement {feature_name}"
  - agent: qa
    task: "Create tests for {feature_name}"
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
co code task-manager --component auth --output auth.py "Implement user authentication using JWT"
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
co context show task-manager developer --task "Implement login form"
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
co config task-manager set technology_stack "Python,Flask,React"
```

## Tips for Effective Use

1. **Start with clear requirements**: Always begin projects with a thorough session using the Product Manager agent

2. **Maintain knowledge continuity**: After each agent interaction, review the knowledge artifacts and ensure they're up to date

3. **Use specific tasks**: Give agents clear, focused tasks rather than broad instructions

4. **Leverage workflows**: Use the predefined workflows for common development patterns

5. **Customize agent prompts**: Tailor agent prompts to your specific domain for better results

6. **Combine with manual editing**: Use the generated content as a starting point and refine manually when needed

7. **Use CLI for quick tasks**: The CLI is ideal for quick tasks, while the web interface is better for exploring complex projects

8. **Regular knowledge search**: Use the search feature to find relevant information before starting new tasks

9. **Keep context focused**: Only include relevant context for each agent to improve performance

10. **Version control integration**: Use the GitHub integration to maintain a proper version history of your project
