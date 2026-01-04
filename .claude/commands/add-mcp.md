# Add MCP Command

**IMPORTANT: This command ONLY runs when explicitly invoked via `/add-mcp`. Do NOT auto-create MCP servers. Wait for user to explicitly run `/add-mcp`.**

Goal: Create a new MCP server in the `mcp-servers/` directory following best practices.

## Required Skills

Load these skills before implementation:

1. **mcp-builder**: `.claude/skills/mcp-builder/SKILL.md` - Core MCP development guide
2. **md**: `.claude/skills/document-skills/md/SKILL.md` - Clean markdown for README
3. **token-formatter**: `.claude/skills/token-formatter/SKILL.md` - Compress documentation

## Execution Flow

### Step 1: Gather Requirements

Ask user:
1. "What service/API should this MCP server integrate with?"
2. "Python or TypeScript implementation?"
3. "What are the main operations needed?"

### Step 2: Research (from mcp-builder)

1. Fetch MCP protocol docs: `https://modelcontextprotocol.io/llms-full.txt`
2. Fetch SDK docs:
   - Python: `https://raw.githubusercontent.com/modelcontextprotocol/python-sdk/main/README.md`
   - TypeScript: `https://raw.githubusercontent.com/modelcontextprotocol/typescript-sdk/main/README.md`
3. Research target API documentation
4. Load MCP best practices: `.claude/skills/mcp-builder/reference/mcp_best_practices.md`

### Step 3: Create Directory Structure

```bash
mcp-servers/<server-name>/
├── README.md           # Documentation (use md skill)
├── server.py           # Python implementation
│   OR
├── src/
│   └── index.ts        # TypeScript implementation
├── package.json        # TypeScript only
├── tsconfig.json       # TypeScript only
└── .env.example        # Environment variables template
```

### Step 4: Implement Server

Follow mcp-builder skill phases:
1. **Phase 1**: Research & Planning
2. **Phase 2**: Implementation (use language-specific guide)
3. **Phase 3**: Review & Refine
4. **Phase 4**: Create evaluations (optional)

### Step 5: Create README

Use **md skill** for clean markdown. Include:

```markdown
# <Server Name> MCP Server

Brief description.

## Features

- Feature 1
- Feature 2

## Installation

[Setup instructions]

## Configuration

| Variable | Description | Required |
|----------|-------------|----------|
| API_KEY | API key | Yes |

## Tools

| Tool | Description |
|------|-------------|
| tool_name | What it does |

## Usage

[Examples]
```

### Step 6: Compress Documentation

Use **token-formatter skill** to compress:
- README.md (Level 1: Light - keep readable)
- Code comments (Level 2: Medium)
- Add token count footer to README

## File Templates

### Python Server Template

```python
"""
<Server Name> MCP Server
Brief description.
"""

from mcp.server.fastmcp import FastMCP
from pydantic import BaseModel, Field

mcp = FastMCP("<server-name>")

class ToolInput(BaseModel):
    """Input validation."""
    param: str = Field(..., description="Parameter description")

@mcp.tool()
async def tool_name(input: ToolInput) -> str:
    """
    Tool description.

    Args:
        input: Validated input

    Returns:
        Result description
    """
    # Implementation
    return "result"

if __name__ == "__main__":
    mcp.run()
```

### TypeScript Server Template

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { z } from "zod";

const server = new McpServer({ name: "<server-name>", version: "1.0.0" });

const ToolInputSchema = z.object({
  param: z.string().describe("Parameter description"),
}).strict();

server.tool(
  "tool_name",
  "Tool description",
  ToolInputSchema.shape,
  async ({ param }) => ({
    content: [{ type: "text", text: "result" }],
  })
);

server.run();
```

## Quality Checklist

Before completion, verify:

- [ ] Server follows mcp-builder best practices
- [ ] README is clean (md skill validated)
- [ ] Documentation is token-efficient
- [ ] .env.example includes all required vars
- [ ] Error handling is LLM-friendly
- [ ] Tool descriptions are clear and actionable

## Update README

After creating MCP server, update project `README.md`:

```markdown
## MCP Servers

| Server | Description |
|--------|-------------|
| **<new-server>** | <description> |
```

---
Integrates: mcp-builder, md, token-formatter