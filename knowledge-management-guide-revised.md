# ClaudeOrchestra Knowledge Management Guide

This guide explains how ClaudeOrchestra manages knowledge across specialized agents and throughout your project lifecycle.

## Knowledge Structure

ClaudeOrchestra uses a file-based knowledge structure designed to scale with project complexity:

```
storage/
  projects/
    {project_name}/
      config.yml                 # Project configuration
      knowledge/
        requirements.md          # Requirements document
        architecture.md          # System architecture
        api_design.md            # API specifications
        data_model.md            # Database schema
        ui_design.md             # UI design and components
        deployment.md            # Deployment configuration
        security.md              # Security considerations
        decisions.json           # Key decisions log
        code/                    # Generated code
          frontend/              # Frontend components
          backend/               # Backend services
          database/              # Database scripts
          devops/                # Infrastructure code
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

### 3. Specialized Development Documents

Each specialized development agent maintains its own knowledge artifacts:

#### Frontend Design (ui_design.md)
```markdown
# UI Design: {project_name}

## Design System
- Colors: {color_palette}
- Typography: {font_choices}
- Components: {component_library}

## Page Structure
1. {page_1}
   - Layout: {layout_description}
   - Components: {component_list}
   - User Flow: {flow_description}

2. {page_2}
   - Layout: {layout_description}
   - Components: {component_list}
   - User Flow: {flow_description}

## State Management
{state_management_approach}

## Responsive Design
{responsive_design_strategy}
```

#### API Design (api_design.md)
```markdown
# API Design: {project_name}

## Endpoints
1. `{http_method} {endpoint_path}`
   - Purpose: {purpose}
   - Request Parameters: {parameters}
   - Response Format: {response}
   - Status Codes: {status_codes}
   - Authentication: {auth_requirements}

2. `{http_method} {endpoint_path}`
   - Purpose: {purpose}
   - Request Parameters: {parameters}
   - Response Format: {response}
   - Status Codes: {status_codes}
   - Authentication: {auth_requirements}

## Authentication
{authentication_approach}

## Error Handling
{error_handling_strategy}

## Rate Limiting
{rate_limiting_policy}
```

#### Database Schema (data_model.md)
```markdown
# Database Schema: {project_name}

## Entities
1. {entity_1}
   - Fields:
     - {field_1}: {type} - {description}
     - {field_2}: {type} - {description}
   - Indexes: {indexes}
   - Constraints: {constraints}

2. {entity_2}
   - Fields:
     - {field_1}: {type} - {description}
     - {field_2}: {type} - {description}
   - Indexes: {indexes}
   - Constraints: {constraints}

## Relationships
- {entity_1} -> {entity_2}: {relationship_type}
- {entity_2} -> {entity_3}: {relationship_type}

## Migration Strategy
{migration_approach}

## Query Patterns
{common_query_patterns}
```

### 4. Decisions Log (decisions.json)

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
      "agent": "security",
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

### 1. Agent-Specific Context Building

Each specialized agent receives context tailored to their role:

```python
def build_agent_context(project: str, agent_type: str, task: str) -> Dict[str, Any]:
    """Build context for a specific agent and task"""
    # Load basic project info
    project_info = load_project_info(project)
    
    # Common artifacts for all agents
    context = {
        "project": project_info,
        "task": task
    }
    
    # Add requirements for all agents
    requirements = load_artifact(project, "requirements.md")
    if requirements:
        context["requirements"] = requirements
    
    # Agent-specific context enrichment
    if agent_type == "pm":
        # Product Manager focuses on requirements and user needs
        context["existing_requirements"] = requirements
    
    elif agent_type == "architect":
        # Architect needs requirements and existing architecture
        context["architecture"] = load_artifact(project, "architecture.md")
        context["decisions"] = load_artifact(project, "decisions.json")
    
    elif agent_type == "frontend":
        # Frontend Developer needs UI design, architecture, and API
        context["architecture"] = load_artifact(project, "architecture.md")
        context["ui_design"] = load_artifact(project, "ui_design.md")
        context["api_design"] = load_artifact(project, "api_design.md")
    
    elif agent_type == "backend":
        # Backend Developer needs API design, architecture, and data model
        context["architecture"] = load_artifact(project, "architecture.md")
        context["api_design"] = load_artifact(project, "api_design.md")
        context["data_model"] = load_artifact(project, "data_model.md")
    
    elif agent_type == "db":
        # Database Engineer needs data model and architecture
        context["architecture"] = load_artifact(project, "architecture.md")
        context["data_model"] = load_artifact(project, "data_model.md")
    
    elif agent_type == "security":
        # Security Engineer needs architecture, API, and data model
        context["architecture"] = load_artifact(project, "architecture.md")
        context["api_design"] = load_artifact(project, "api_design.md")
        context["data_model"] = load_artifact(project, "data_model.md")
        context["security"] = load_artifact(project, "security.md")
    
    # Compress context to fit token limits
    return compress_context(context)
```

### 2. Context Compression

For efficient Claude API usage, context is compressed based on relevance:

```python
def compress_context(context: Dict[str, Any], max_tokens: int = 8000) -> Dict[str, Any]:
    """Compress context to fit within token limits"""
    # Create a compressed copy
    compressed = context.copy()
    
    # Strategy 1: Truncate history to most recent items
    if "history" in compressed and isinstance(compressed["history"], list):
        compressed["history"] = compressed["history"][-5:]
    
    # Strategy 2: Include only relevant sections of documents
    for key in ["requirements", "architecture", "api_design", "data_model", "ui_design"]:
        if key in compressed and isinstance(compressed[key], str) and len(compressed[key]) > 1000:
            # Extract only the sections relevant to the current task
            compressed[key] = extract_relevant_sections(compressed[key], compressed.get("task", ""))
    
    # Strategy 3: Summarize verbose content
    for key in compressed:
        if isinstance(compressed[key], str) and len(compressed[key]) > 2000:
            # Summarize long text content
            compressed[key] = summarize_content(compressed[key])
    
    return compressed
```

### 3. Knowledge Transfer Between Specialized Agents

When transferring context between agents, ClaudeOrchestra uses a standardized format:

```python
def prepare_context_transfer(from_agent: str, to_agent: str, project: str, task: str) -> Dict[str, Any]:
    """Prepare context transfer between agents"""
    # Get the from agent's knowledge and output
    from_agent_knowledge = load_agent_knowledge(project, from_agent)
    latest_output = load_latest_output(project, from_agent)
    
    # Create the transfer context based on agent specializations
    transfer = {
        "from_agent": from_agent,
        "to_agent": to_agent,
        "project": load_project_info(project),
        "task": task,
        "key_knowledge": extract_key_knowledge(from_agent_knowledge, latest_output, to_agent),
        "current_status": summarize_status(latest_output),
        "next_steps": extract_next_steps(latest_output),
        "questions": extract_questions(latest_output)
    }
    
    # Add specialized knowledge for specific agent pairs
    if from_agent == "architect" and to_agent == "frontend":
        # Architect to Frontend Developer needs UI-relevant architecture
        transfer["ui_components"] = extract_ui_components(from_agent_knowledge)
    
    elif from_agent == "architect" and to_agent == "backend":
        # Architect to Backend Developer needs API-relevant architecture
        transfer["api_components"] = extract_api_components(from_agent_knowledge)
    
    elif from_agent == "architect" and to_agent == "db":
        # Architect to Database Engineer needs data model
        transfer["data_entities"] = extract_data_entities(from_agent_knowledge)
    
    return transfer
```

## Scaling Knowledge Management

ClaudeOrchestra's knowledge management scales with project complexity:

### 1. Simple Projects

For simple personal projects:
- Minimal knowledge artifacts (requirements, basic implementation)
- Simple file-based storage
- Limited agent specialization (PM, Developer, QA)

### 2. Standard Projects

For professional applications:
- More detailed knowledge artifacts
- Specialized development artifacts (frontend, backend)
- Simple knowledge relationships
- Core specialized agents (PM, Architect, Frontend, Backend, QA)

### 3. Advanced Projects

For complex applications:
- Comprehensive knowledge artifacts
- Specialized domain knowledge
- Component relationship tracking
- Full development team (all specialized agents)

### 4. Enterprise Projects

For enterprise-grade systems:
- Extensive knowledge base with compliance tracking
- Knowledge graph with semantic relationships
- Advanced context compression and retrieval
- Security and compliance artifacts
- Complete agent ecosystem with specialized expertise

## Knowledge Management Best Practices

### 1. Maintaining Knowledge Consistency

To ensure consistent knowledge across specialized agents:

1. **Use standardized formats** for all knowledge artifacts
2. **Update shared knowledge** after each agent interaction
3. **Validate knowledge** periodically with each agent
4. **Resolve conflicts** when agents provide contradictory information

### 2. Efficient Context Building

For efficient context management:

1. **Include only relevant knowledge** for each specialized agent
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
    
    # Handle nested paths for specialized artifacts
    file_path = project_dir / artifact_name
    file_path.parent.mkdir(parents=True, exist_ok=True)
    
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
    
    for file_path in knowledge_dir.glob("**/*.md"):
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

### 2. Specialized Agent Handoff

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

# Now prepare transfer to specialized development agents
db_transfer = knowledge_manager.prepare_context_transfer(
    from_agent="architect",
    to_agent="db",
    project="task-manager",
    task="Design database schema"
)

backend_transfer = knowledge_manager.prepare_context_transfer(
    from_agent="architect",
    to_agent="backend",
    project="task-manager",
    task="Design API endpoints"
)

frontend_transfer = knowledge_manager.prepare_context_transfer(
    from_agent="architect",
    to_agent="frontend",
    project="task-manager",
    task="Design UI components"
)

# Create specialized development agents
db_agent = create_agent("db", "task-manager", db_transfer)
backend_agent = create_agent("backend", "task-manager", backend_transfer)
frontend_agent = create_agent("frontend", "task-manager", frontend_transfer)

# Execute specialized development tasks
db_schema = db_agent.execute("Design database schema based on architecture")
api_design = backend_agent.execute("Design API endpoints based on architecture")
ui_design = frontend_agent.execute("Design UI components based on architecture")

# Save specialized knowledge
knowledge_manager.save_knowledge("task-manager", "data_model.md", db_schema["output"])
knowledge_manager.save_knowledge("task-manager", "api_design.md", api_design["output"])
knowledge_manager.save_knowledge("task-manager", "ui_design.md", ui_design["output"])
```

## Scaling Knowledge Management for Complex Projects

As your projects grow in complexity, consider these enhancements:

### 1. Vector Database Integration

For larger projects, implement vector embeddings for semantic search:

```python
import numpy as np
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity

class VectorKnowledgeStore:
    """Knowledge store with vector search capabilities"""
    
    def __init__(self):
        self.documents = []
        self.vectorizer = TfidfVectorizer()
        self.vectors = None
        
    def add_document(self, content: str, metadata: Dict[str, Any]):
        """Add a document to the knowledge store"""
        self.documents.append({
            "content": content,
            "metadata": metadata
        })
        # Invalidate existing vectors
        self.vectors = None
        
    def build_vectors(self):
        """Build vector representations of documents"""
        if not self.documents:
            return
            
        contents = [doc["content"] for doc in self.documents]
        self.vectors = self.vectorizer.fit_transform(contents)
        
    def search(self, query: str, top_k: int = 5) -> List[Dict[str, Any]]:
        """Search for documents similar to query"""
        if not self.vectors:
            self.build_vectors()
            
        if not self.vectors:
            return []
            
        query_vec = self.vectorizer.transform([query])
        similarities = cosine_similarity(query_vec, self.vectors).flatten()
        
        # Get top-k results
        top_indices = np.argsort(similarities)[-top_k:][::-1]
        
        results = []
        for idx in top_indices:
            results.append({
                "content": self.documents[idx]["content"],
                "metadata": self.documents[idx]["metadata"],
                "similarity": similarities[idx]
            })
            
        return results
```

### 2. Knowledge Graph for Component Relationships

For enterprise projects, track relationships between components:

```python
class KnowledgeGraph:
    """Graph structure for tracking relationships between knowledge artifacts"""
    
    def __init__(self):
        self.nodes = {}  # id -> node data
        self.edges = {}  # (source_id, target_id) -> edge data
        
    def add_node(self, node_id: str, node_type: str, data: Dict[str, Any]):
        """Add a node to the graph"""
        self.nodes[node_id] = {
            "type": node_type,
            "data": data
        }
        
    def add_edge(self, source_id: str, target_id: str, edge_type: str, data: Dict[str, Any] = None):
        """Add an edge between nodes"""
        if source_id not in self.nodes or target_id not in self.nodes:
            raise ValueError(f"Source or target node not found: {source_id} -> {target_id}")
            
        self.edges[(source_id, target_id)] = {
            "type": edge_type,
            "data": data or {}
        }
        
    def get_neighbors(self, node_id: str) -> List[Dict[str, Any]]:
        """Get all nodes connected to a given node"""
        if node_id not in self.nodes:
            return []
            
        neighbors = []
        for (source, target), edge_data in self.edges.items():
            if source == node_id:
                neighbors.append({
                    "node": self.nodes[target],
                    "node_id": target,
                    "edge": edge_data,
                    "direction": "outgoing"
                })
            elif target == node_id:
                neighbors.append({
                    "node": self.nodes[source],
                    "node_id": source,
                    "edge": edge_data,
                    "direction": "incoming"
                })
                
        return neighbors
        
    def find_paths(self, start_id: str, end_id: str, max_depth: int = 5) -> List[List[str]]:
        """Find all paths between two nodes up to a maximum depth"""
        if start_id not in self.nodes or end_id not in self.nodes:
            return []
            
        def dfs(current_id, path, depth):
            if depth > max_depth:
                return []
                
            if current_id == end_id:
                return [path + [current_id]]
                
            paths = []
            for neighbor in self.get_neighbors(current_id):
                neighbor_id = neighbor["node_id"]
                if neighbor_id not in path:  # Avoid cycles
                    new_paths = dfs(neighbor_id, path + [current_id], depth + 1)
                    paths.extend(new_paths)
                    
            return paths
            
        return dfs(start_id, [], 0)
```

### 3. Hierarchical Knowledge Summarization

For large knowledge bases, implement hierarchical summarization:

```python
async def create_hierarchical_summary(content: str, max_levels: int = 3) -> Dict[str, Any]:
    """Create a hierarchical summary of content"""
    # This would use Claude to generate summaries at different levels
    # Level 1: One-paragraph summary
    # Level 2: Section-by-section summary
    # Level 3: Detailed summary with key points
    
    async with ClaudeClient() as client:
        # Generate level 1 summary (most condensed)
        l1_prompt = f"""
        Please provide a one-paragraph summary (3-4 sentences) of the following content:
        
        {content[:2000]}  # Truncate for prompt size limits
        """
        l1_response = await client.generate_response(l1_prompt)
        l1_summary = extract_summary_from_response(l1_response)
        
        # Generate level 2 summary (section highlights)
        l2_prompt = f"""
        Please provide a section-by-section summary of the following content.
        For each main section, write 1-2 sentences highlighting the key points.
        
        {content[:4000]}  # Truncate for prompt size limits
        """
        l2_response = await client.generate_response(l2_prompt)
        l2_summary = extract_summary_from_response(l2_response)
        
        # Generate level 3 summary (more detailed)
        if max_levels >= 3:
            l3_prompt = f"""
            Please provide a detailed summary of the following content.
            Include key points, important details, and significant conclusions.
            Format as bullet points under each section heading.
            
            {content[:6000]}  # Truncate for prompt size limits
            """
            l3_response = await client.generate_response(l3_prompt)
            l3_summary = extract_summary_from_response(l3_response)
        else:
            l3_summary = None
        
        return {
            "level_1": l1_summary,
            "level_2": l2_summary,
            "level_3": l3_summary,
            "full_content": content
        }
```

By following these knowledge management practices, ClaudeOrchestra can maintain coherent project understanding across multiple specialized agents, enabling effective collaboration across projects of all sizes - from simple personal tools to complex enterprise applications.