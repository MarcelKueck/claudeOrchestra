# ClaudeOrchestra Knowledge Management Guide

This guide explains how ClaudeOrchestra manages knowledge across agents and throughout your project lifecycle.

## Knowledge Structure

ClaudeOrchestra uses a simple, file-based knowledge structure designed for personal projects:

```
storage/
  projects/
    {project_name}/
      config.yml                 # Project configuration
      knowledge/
        requirements.md          # Requirements document
        architecture.md          # System architecture
        api_design.md            # API specifications
        data_model.md            # Data model definitions
        decisions.json           # Key decisions log
        code/                    # Generated code
          {component_name}.py    # Source code files
      history/
        {agent_type}/            # Agent interaction history
          {timestamp}_{task}.json  # Record of agent execution
```

## Key Knowledge Artifacts

### 1. Requirements Document (requirements.md)

Contains structured requirements gathered by the Product Manager agent:

```markdown
# Project Requirements: {project_name}

## Overview
{project_description}

## User Personas
- {persona_1}: {description}
- {persona_2}: {description}

## User Stories
1. As a {user_type}, I want {action} so that {benefit}
2. As a {user_type}, I want {action} so that {benefit}

## Functional Requirements
### Must Have
- {requirement_1}
- {requirement_2}

### Should Have
- {requirement_3}
- {requirement_4}

### Could Have
- {requirement_5}
- {requirement_6}

## Non-Functional Requirements
- {non_functional_requirement_1}
- {non_functional_requirement_2}

## Open Questions
- {question_1}
- {question_2}
```

### 2. Architecture Document (architecture.md)

Contains system architecture designed by the Architect agent:

```markdown
# System Architecture: {project_name}

## Overview
{architecture_description}

## Components
1. {component_1}
   - Purpose: {purpose}
   - Responsibilities: {responsibilities}
   - Interfaces: {interfaces}

2. {component_2}
   - Purpose: {purpose}
   - Responsibilities: {responsibilities}
   - Interfaces: {interfaces}

## Data Model
{data_model_description}

## API Design
{api_design_overview}

## Technology Choices
- Frontend: {technology} - {rationale}
- Backend: {technology} - {rationale}
- Database: {technology} - {rationale}
```

### 3. Decisions Log (decisions.json)

Tracks key decisions made during the project:

```json
{
  "decisions": [
    {
      "id": "decision-001",
      "date": "2023-04-15T10:30:00Z",
      "agent": "architect",
      "topic": "Database Selection",
      "decision": "Use PostgreSQL",
      "rationale": "Need for relational data model and ACID transactions",
      "alternatives": ["MongoDB", "MySQL"],
      "context": "Data includes many relationships that benefit from a relational structure"
    },
    {
      "id": "decision-002",
      "date": "2023-04-16T14:20:00Z",
      "agent": "developer",
      "topic": "Authentication Method",
      "decision": "Use JWT with refresh tokens",
      "rationale": "Provides stateless authentication with security benefits",
      "alternatives": ["Session-based auth", "OAuth only"],
      "context": "Application needs to support both web and mobile clients"
    }
  ]
}
```

## Context Management

### 1. Context Building

When an agent is invoked, ClaudeOrchestra builds relevant context:

```python
def build_agent_context(project: str, agent_type: str, task: str) -> Dict[str, Any]:
    """Build context for a specific agent and task"""
    # Load basic project info
    project_info = load_project_info(project)
    
    # Load relevant knowledge artifacts based on agent type
    if agent_type == "pm":
        # Product Manager primarily needs requirements
        context = {
            "project": project_info,
            "requirements": load_artifact(project, "requirements.md"),
            "decisions": load_relevant_decisions(project, task)
        }
    elif agent_type == "architect":
        # Architect needs requirements and existing architecture
        context = {
            "project": project_info,
            "requirements": load_artifact(project, "requirements.md"),
            "architecture": load_artifact(project, "architecture.md"),
            "decisions": load_relevant_decisions(project, task)
        }
    elif agent_type == "developer":
        # Developer needs architecture, API design, and data model
        context = {
            "project": project_info,
            "requirements": load_artifact(project, "requirements.md"),
            "architecture": load_artifact(project, "architecture.md"),
            "api_design": load_artifact(project, "api_design.md"),
            "data_model": load_artifact(project, "data_model.md"),
            "decisions": load_relevant_decisions(project, task)
        }
    
    # Add task-specific context
    add_task_specific_context(context, project, agent_type, task)
    
    return context
```

### 2. Context Compression

For efficient Claude API usage, context is compressed:

```python
def compress_context(context: Dict[str, Any], max_tokens: int = 8000) -> Dict[str, Any]:
    """Compress context to fit within token limits"""
    # Create a compressed copy
    compressed = context.copy()
    
    # Strategy 1: Truncate history to most recent items
    if "history" in compressed and isinstance(compressed["history"], list):
        compressed["history"] = compressed["history"][-5:]
    
    # Strategy 2: Include only relevant sections of documents
    for key in ["requirements", "architecture", "api_design", "data_model"]:
        if key in compressed and isinstance(compressed[key], str) and len(compressed[key]) > 1000:
            # Extract only the sections relevant to the current task
            compressed[key] = extract_relevant_sections(compressed[key], compressed.get("task", ""))
    
    # Strategy 3: Summarize verbose content
    for key in compressed:
        if isinstance(compressed[key], str) and len(compressed[key]) > 2000:
            # Summarize long text content
            compressed[key] = summarize_content(compressed[key])
    
    return compressed

### 3. Knowledge Transfer

When transferring context between agents, ClaudeOrchestra uses a standardized format:

```python
def prepare_context_transfer(from_agent: str, to_agent: str, project: str, task: str) -> Dict[str, Any]:
    """Prepare context transfer between agents"""
    # Get the from agent's knowledge and output
    from_agent_knowledge = load_agent_knowledge(project, from_agent)
    latest_output = load_latest_output(project, from_agent)
    
    # Create the transfer context
    transfer = {
        "from_agent": from_agent,
        "to_agent": to_agent,
        "project": load_project_info(project),
        "task": task,
        "key_knowledge": extract_key_knowledge(from_agent_knowledge, latest_output),
        "current_status": summarize_status(latest_output),
        "next_steps": extract_next_steps(latest_output),
        "questions": extract_questions(latest_output)
    }
    
    return transfer
```

## Knowledge Management Best Practices

### 1. Maintaining Knowledge Consistency

To ensure consistent knowledge across agents:

1. **Use standardized formats** for all knowledge artifacts
2. **Update shared knowledge** after each agent interaction
3. **Validate knowledge** periodically with each agent
4. **Resolve conflicts** when agents provide contradictory information

### 2. Efficient Context Building

For efficient context management:

1. **Include only relevant knowledge** for each task
2. **Prioritize recent information** over older context
3. **Summarize lengthy documents** rather than including full text
4. **Track key concepts** across the entire project

### 3. Knowledge Persistence

To maintain knowledge over time:

1. **Save all important outputs** as knowledge artifacts
2. **Update existing artifacts** rather than creating duplicates
3. **Maintain decision logs** to track rationale
4. **Version knowledge artifacts** to track changes

## Practical Implementation

### 1. Loading Knowledge

```python
def load_knowledge_artifact(project: str, artifact_name: str) -> Optional[str]:
    """Load a knowledge artifact from the project"""
    file_path = Path(f"./storage/projects/{project}/knowledge/{artifact_name}")
    
    if not file_path.exists():
        return None
        
    with open(file_path, "r") as f:
        content = f.read()
        
    return content
```

### 2. Saving Knowledge

```python
def save_knowledge_artifact(project: str, artifact_name: str, content: str) -> None:
    """Save a knowledge artifact to the project"""
    project_dir = Path(f"./storage/projects/{project}/knowledge")
    project_dir.mkdir(parents=True, exist_ok=True)
    
    file_path = project_dir / artifact_name
    
    with open(file_path, "w") as f:
        f.write(content)
```

### 3. Extracting Information

```python
def extract_key_points(agent_output: str) -> List[str]:
    """Extract key points from agent output"""
    # This is a simple example - in practice, you might use 
    # Claude to extract the key points more intelligently
    
    lines = agent_output.split("\n")
    key_points = []
    
    for line in lines:
        # Look for bullet points, numbered lists, or section headers
        if line.strip().startswith(("- ", "* ", "#", "##")) or \
           (line.strip() and len(line.strip()) <= 100 and ":" in line):
            key_points.append(line.strip())
    
    return key_points
```

### 4. Knowledge Search

```python
def search_knowledge(project: str, query: str) -> List[Dict[str, Any]]:
    """Search project knowledge for relevant information"""
    results = []
    knowledge_dir = Path(f"./storage/projects/{project}/knowledge")
    
    for file_path in knowledge_dir.glob("*.md"):
        with open(file_path, "r") as f:
            content = f.read()
            
        # Simple text search - in practice, use vector search
        if query.lower() in content.lower():
            results.append({
                "file": file_path.name,
                "title": extract_title(content),
                "snippet": extract_snippet(content, query)
            })
    
    return results
```

## Example Usage

### 1. Project Initialization

```python
# Initialize knowledge structure
knowledge_manager.init_project("task-manager", "A simple task management application")

# Create initial requirements
pm_agent = create_agent("pm", "task-manager")
requirements = pm_agent.execute("Define core requirements for a task management app")

# Save requirements to knowledge store
knowledge_manager.save_knowledge("task-manager", "requirements.md", requirements["output"])
```

### 2. Agent Handoff

```python
# Load requirements for architect
requirements = knowledge_manager.load_knowledge("task-manager", "requirements.md")

# Prepare context transfer
transfer = knowledge_manager.prepare_context_transfer(
    from_agent="pm",
    to_agent="architect",
    project="task-manager",
    task="Design system architecture"
)

# Create architect agent with transfer context
architect_agent = create_agent("architect", "task-manager", transfer)

# Execute architecture design
architecture = architect_agent.execute("Design system architecture based on requirements")

# Save architecture to knowledge store
knowledge_manager.save_knowledge("task-manager", "architecture.md", architecture["output"])
```

By following these knowledge management practices, ClaudeOrchestra can maintain coherent project understanding across multiple specialized agents, enabling them to collaborate effectively on your personal projects.