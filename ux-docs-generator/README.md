# UX Documentation Extractor

A Claude Code plugin that reverse-engineers your codebase into comprehensive UX documentation.

## What It Does

Analyzes your codebase and creates documentation that can power an AI product assistant:

- **UI Pages** - Every route, what users see, what actions they can take
- **API Endpoints** - What they do, when they're called, how they relate to the UI
- **Product Features** - Cross-cutting capabilities like RBAC, notifications, search

The output is designed for product experts, support agents, and solution architects—not developers.

## Installation

```bash
claude plugins add ./path/to/ux-docs-plugin
```

## Usage

```bash
/ux-docs
```

That's it. The plugin will:
1. Ask a few setup questions about your project
2. Discover all pages, APIs, and features
3. Document everything automatically
4. Generate cross-referenced indexes

If interrupted, just run `/ux-docs` again—it resumes where it left off.

## Output

Documentation is saved to `.ux-doccs/` in your project:

```
.ux-doccs/
├── pages/          # One file per UI page
├── apis/           # One file per API endpoint
├── features/       # One file per product feature
├── index.md        # Master index with cross-references
├── jargon.json     # Product terminology dictionary
└── manifest.json   # State (for resumability)
```

## RAG Support

The generated documentation is specifically designed for Retrieval-Augmented Generation (RAG). Use it to power AI assistants that can act as product experts.

### What You Get

Each documented feature, page, and API includes:

- **Structured JSON metadata** (`_feature.json`) for programmatic queries
- **Human-readable Markdown** (`overview.md`) for natural language retrieval
- **Cross-references** linking related pages, APIs, and features
- **Aliases and synonyms** for better search matching
- **Tier availability** (free/pro/enterprise) for accurate capability answers

### Example: Feature Documentation

```json
// .ux-doccs/features/rbac/_feature.json
{
  "id": "rbac",
  "name": "Role-Based Access Control",
  "aliases": ["permissions", "roles", "access control"],
  "capabilities": [
    {
      "name": "Create custom roles",
      "ui_location": "Settings > Permissions > New Role",
      "api_endpoint": "POST /api/roles",
      "tier": ["pro", "enterprise"]
    }
  ],
  "limitations": [...],
  "related_features": [...]
}
```

### RAG Integration Patterns

**Vector Database Ingestion**
```python
# Ingest both JSON (structured queries) and Markdown (semantic search)
for feature_dir in glob(".ux-doccs/features/*/"):
    metadata = json.load(open(f"{feature_dir}/_feature.json"))
    content = open(f"{feature_dir}/overview.md").read()

    # Index with metadata for filtering
    vector_db.upsert(
        content=content,
        metadata={
            "type": "feature",
            "id": metadata["id"],
            "tiers": list(metadata["tier_availability"].keys()),
            "aliases": metadata.get("aliases", [])
        }
    )
```

**Query Enhancement**
```python
# Use jargon.json to expand user queries
jargon = json.load(open(".ux-doccs/jargon.json"))

def expand_query(query):
    for term, definition in jargon.items():
        if term in query.lower():
            query += f" ({definition['aliases']})"
    return query
```

**Context Assembly**
```python
# Follow cross-references for comprehensive answers
def get_context(feature_id):
    feature = load_feature(feature_id)
    context = [feature["overview"]]

    # Include related features
    for related in feature["related_features"]:
        context.append(load_feature(related["id"])["summary"])

    # Include relevant pages and APIs
    context.extend(get_pages_for_feature(feature_id))
    return context
```

### What AI Assistants Can Answer

With the generated documentation, your AI can:

- **Explain features**: "What is RBAC and how do I set it up?"
- **Guide users**: "How do I add a contractor with limited access?"
- **Compare tiers**: "What permissions features do I get on Pro vs Enterprise?"
- **Navigate the UI**: "Where do I find the team settings?"
- **Clarify limitations**: "Can I restrict access to a specific project?"

## Philosophy

Document what users **see and do**—not how code works.
