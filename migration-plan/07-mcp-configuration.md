# Step 7: MCP Configuration

**Priority**: Medium  
**Estimated Time**: 3-4 hours  
**Prerequisites**: Steps 1-6 complete  
**Status**: 📝 Ready to Execute

---

## Objective

Migrate Model Context Protocol (MCP) server configurations from Claude Code format to GitHub Copilot Agent format. Configure MCP tools for use with custom agents.

---

## Source Analysis

### Current MCP Servers (from mcp-configs/mcp-servers.json)

| Server | Type | Purpose | Priority |
|--------|------|---------|----------|
| `github` | Command | GitHub PRs, issues, repos | ⭐⭐⭐ Critical |
| `firecrawl` | Command | Web scraping | ⭐⭐ High |
| `supabase` | Command | Database operations | ⭐⭐ High |
| `memory` | Command | Persistent memory | ⭐⭐ High |
| `sequential-thinking` | Command | Chain-of-thought reasoning | ⭐ Medium |
| `vercel` | HTTP | Vercel deployments | ⭐⭐ High |
| `railway` | Command | Railway deployments | ⭐ Medium |
| `cloudflare-*` (4 servers) | HTTP | CF docs, workers, logs | ⭐ Medium |
| `clickhouse` | HTTP | Analytics queries | ⭐ Low |
| `context7` | Command | Live documentation | ⭐ Medium |
| `magic` | Command | Magic UI components | ⭐ Low |
| `filesystem` | Command | Filesystem operations | ⭐⭐ High |

**Total**: 15 MCP servers configured

---

## GitHub Copilot MCP Support

### Current Status (as of 2026-01)

According to [GitHub Docs - Custom Agents](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents):

```yaml
---
description: Agent description
tools:
  - bash
  - view
  - grep
mcp-servers:
  - server-name
---
```

**Key Points**:
- Custom agents can reference MCP servers
- MCP server configuration may differ from Claude format
- Not all MCP servers may be compatible
- Configuration location may vary (IDE settings vs. agent config)

---

## Migration Strategy

### Approach: Selective Migration

1. **Identify Essential Servers**: Focus on high-value, stable MCPs
2. **Test Compatibility**: Verify each server works with Copilot
3. **Configure Per-Agent**: Assign tools based on agent needs (least privilege)
4. **Document**: Clear setup instructions for each MCP

---

## Open Questions (CRITICAL - MUST RESOLVE FIRST)

### Critical Questions

- [ ] **Q1**: How are MCP servers configured in GitHub Copilot?
  - **Options**:
    - A) Per-agent in frontmatter (like `tools:`)
    - B) Global IDE/CLI configuration
    - C) Repository-level configuration file
  - **Impact**: HIGH - affects entire migration approach
  - **Verification**: Check official documentation, test with sample agent
  - **Source**: [GitHub Docs - Custom Agents](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents)

- [ ] **Q2**: Does Copilot support the same MCP protocol version as Claude?
  - **Impact**: HIGH - affects compatibility
  - **Verification**: Check MCP spec version support
  - **Source**: [MCP Specification](https://spec.modelcontextprotocol.io/)

- [ ] **Q3**: Can Copilot use npx-based MCP servers (like Claude)?
  - **Impact**: HIGH - affects 10 out of 15 servers
  - **Verification**: Test with simple MCP server
  - **Note**: Claude uses `npx -y @modelcontextprotocol/server-github`

- [ ] **Q4**: Where do API keys go? Environment variables? IDE settings?
  - **Impact**: HIGH - affects security and setup
  - **Verification**: Check documentation and test

- [ ] **Q5**: Are HTTP-based MCP servers supported?
  - **Impact**: MEDIUM - affects 6 servers
  - **Verification**: Test with Vercel or Cloudflare MCP

---

## Conditional Migration Plan

**IMPORTANT**: Complete research on Q1-Q5 before proceeding.

### Scenario A: Per-Agent Configuration (If Supported)

If `mcp-servers:` field is supported in agent frontmatter:

**Example Agent with MCP**:
```yaml
---
description: GitHub operations specialist
tools:
  - view
  - grep
  - bash
mcp-servers:
  - github
  - vercel
---
```

**Implementation**:
- Add `mcp-servers:` to each agent that needs MCP tools
- Configure MCP servers globally (IDE settings or config file)
- Reference servers by name in agents

---

### Scenario B: Global Configuration Only (More Likely)

If MCP servers are configured globally (not per-agent):

**Configuration Location**: Check these locations
- VS Code: `settings.json` → Copilot MCP configuration
- CLI: `~/.config/github-copilot/` or similar
- Repository: `.github/copilot-config.json` (speculative)

**Implementation**:
- Configure essential MCP servers globally
- Agents automatically have access to configured tools
- Document which agents use which MCP tools

---

## MCP Server Migration (Conditional)

**PREREQUISITE**: Resolve Q1-Q5 first.

### Priority 1: Essential Servers

#### 1. GitHub MCP ⭐⭐⭐

**Source Configuration**:
```json
{
  "github": {
    "command": "npx",
    "args": ["-y", "@modelcontextprotocol/server-github"],
    "env": {
      "GITHUB_PERSONAL_ACCESS_TOKEN": "YOUR_GITHUB_PAT_HERE"
    },
    "description": "GitHub operations - PRs, issues, repos"
  }
}
```

**Copilot Configuration** (format TBD after research):
```json
{
  "mcp-servers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_PAT}"
      }
    }
  }
}
```

**Required Setup**:
1. Create GitHub Personal Access Token
2. Set environment variable: `GITHUB_PAT`
3. Test: Can agent list PRs, create issues, etc.?

**Used By**: All agents (especially code-reviewer, planner)

---

#### 2. Filesystem MCP ⭐⭐

**Source Configuration**:
```json
{
  "filesystem": {
    "command": "npx",
    "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/your/projects"],
    "description": "Filesystem operations (set your path)"
  }
}
```

**Note**: May be redundant with Copilot's built-in `view` tool

**Decision**: Test if needed, may skip if built-in tools sufficient

---

#### 3. Memory MCP ⭐⭐

**Source Configuration**:
```json
{
  "memory": {
    "command": "npx",
    "args": ["-y", "@modelcontextprotocol/server-memory"],
    "description": "Persistent memory across sessions"
  }
}
```

**Copilot Equivalent**: Check if Copilot has built-in persistence

**Decision**: Test and evaluate benefit

---

### Priority 2: High-Value Servers

#### 4. Supabase MCP ⭐⭐

**Source Configuration**:
```json
{
  "supabase": {
    "command": "npx",
    "args": ["-y", "@supabase/mcp-server-supabase@latest", "--project-ref=YOUR_PROJECT_REF"],
    "description": "Supabase database operations"
  }
}
```

**Required Setup**:
1. Set Supabase project reference
2. Configure authentication
3. Test database queries

**Used By**: Agents working with backend, database operations

---

#### 5. Vercel MCP ⭐⭐

**Source Configuration**:
```json
{
  "vercel": {
    "type": "http",
    "url": "https://mcp.vercel.com",
    "description": "Vercel deployments and projects"
  }
}
```

**Type**: HTTP-based MCP

**Decision**: Test HTTP MCP support (Q5)

**Used By**: Deployment-related agents

---

#### 6. Firecrawl MCP ⭐⭐

**Source Configuration**:
```json
{
  "firecrawl": {
    "command": "npx",
    "args": ["-y", "firecrawl-mcp"],
    "env": {
      "FIRECRAWL_API_KEY": "YOUR_FIRECRAWL_KEY_HERE"
    },
    "description": "Web scraping and crawling"
  }
}
```

**Required Setup**:
1. Get Firecrawl API key
2. Set environment variable

**Used By**: Research agents, documentation scrapers

---

### Priority 3: Optional Servers

#### Railway, Cloudflare, ClickHouse, etc.

**Decision**: Migrate only if project actively uses these services

**Implementation**:
- Same pattern as above
- Test compatibility
- Document setup

---

## Recommended Minimal Setup

### Start with These 3 MCPs

1. **GitHub** - Critical for PR/issue management
2. **Supabase** (if used) - Database operations
3. **Vercel** (if used) - Deployments

**Rationale**:
- Focus on essential, high-value tools
- Avoid context window pollution
- Easy to add more later

---

## Configuration Template (Speculative)

**File**: `.github/copilot-mcp-config.json` (location TBD)

```json
{
  "$schema": "https://json.schemastore.org/copilot-mcp-config.json",
  "mcp-servers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_PAT}"
      },
      "description": "GitHub operations",
      "enabled": true
    },
    "supabase": {
      "command": "npx",
      "args": [
        "-y",
        "@supabase/mcp-server-supabase@latest",
        "--project-ref=${SUPABASE_PROJECT_REF}"
      ],
      "description": "Supabase database",
      "enabled": true
    },
    "vercel": {
      "type": "http",
      "url": "https://mcp.vercel.com",
      "description": "Vercel deployments",
      "enabled": true
    }
  },
  "security": {
    "use-environment-variables": true,
    "require-explicit-enable": true
  }
}
```

**Environment Variables** (.env.local or IDE settings):
```bash
GITHUB_PAT=ghp_xxxxxxxxxxxx
SUPABASE_PROJECT_REF=your-project-ref
VERCEL_TOKEN=your-vercel-token
```

---

## Testing MCP Integration

### Test 1: Server Connection

For each MCP server:

1. **Start Server** (if applicable)
2. **Test from Agent**: Ask agent to use MCP tool
   ```
   @planner List open PRs in this repository
   ```
3. **Verify**: Check if agent can access MCP server
4. **Log**: Record success/failure

### Test 2: Tool Usage

1. **List Available Tools**: Ask Copilot what tools it has
2. **Verify MCP Tools**: Are MCP tools visible?
3. **Use Tool**: Ask agent to perform MCP operation
4. **Validate**: Did operation succeed?

### Test 3: Error Handling

1. **Invalid Credentials**: Test with wrong API key
2. **Server Unavailable**: Test when server is down
3. **Verify**: Does agent handle errors gracefully?

---

## Implementation Checklist

### Pre-Migration
- [ ] Resolve critical questions (Q1-Q5)
- [ ] Understand Copilot MCP configuration format
- [ ] Identify which MCP servers are essential
- [ ] Gather API keys and credentials
- [ ] Set up test environment

### Migration
- [ ] Create MCP configuration file (if needed)
- [ ] Configure essential MCP servers (GitHub, etc.)
- [ ] Set up environment variables securely
- [ ] Test each MCP server individually
- [ ] Update agent frontmatter (if per-agent config)
- [ ] Document setup instructions

### Validation
- [ ] Test MCP servers with agents
- [ ] Verify tool availability
- [ ] Check error handling
- [ ] Test with different agents
- [ ] Document known issues

### Documentation
- [ ] Create MCP setup guide
- [ ] Document required API keys
- [ ] Add troubleshooting section
- [ ] Update README with MCP requirements

---

## Security Considerations

### API Key Management

**DO NOT**:
- ❌ Commit API keys to repository
- ❌ Store keys in plain text
- ❌ Share keys in documentation

**DO**:
- ✅ Use environment variables
- ✅ Use IDE secret storage
- ✅ Document how to obtain keys
- ✅ Use .gitignore for sensitive files
- ✅ Rotate keys regularly

### Recommended Approach

**File**: `.env.example`
```bash
# Copy to .env.local and fill in your values

# GitHub Personal Access Token
# Required scopes: repo, read:user
# Get from: https://github.com/settings/tokens
GITHUB_PAT=

# Supabase Project Reference
# Get from: Supabase Dashboard → Settings
SUPABASE_PROJECT_REF=

# Firecrawl API Key (optional)
# Get from: https://firecrawl.com
FIRECRAWL_API_KEY=
```

**File**: `.gitignore`
```
.env.local
.env
*.key
*.secret
```

---

## Fallback Strategy

### If MCP Integration Doesn't Work

**Option A: Manual Operations**
- Document MCP operations in README
- Provide CLI commands as alternatives
- Use GitHub CLI (`gh`) for GitHub operations

**Option B: Custom Scripts**
- Create wrapper scripts for common operations
- Call from agents using bash tool
- Store in `.github/scripts/`

**Option C: API Direct Access**
- Use curl/fetch from agents
- Document API endpoints
- Store examples in prompts

---

## Documentation Template

### MCP Setup Guide

**File**: `docs/MCP_SETUP.md`

```markdown
# MCP Server Setup Guide

This project uses Model Context Protocol (MCP) servers to enhance Copilot Agent capabilities.

## Required MCP Servers

### 1. GitHub MCP (Required)

**Purpose**: GitHub PR, issue, and repository operations

**Setup**:
1. Create Personal Access Token: https://github.com/settings/tokens
   - Required scopes: `repo`, `read:user`
2. Set environment variable:
   ```bash
   export GITHUB_PAT=ghp_xxxxxxxxxxxx
   ```
3. Verify:
   ```bash
   npx -y @modelcontextprotocol/server-github --help
   ```

### 2. Supabase MCP (If Using Supabase)

**Purpose**: Database operations

**Setup**:
1. Get project reference from Supabase Dashboard
2. Set environment variable:
   ```bash
   export SUPABASE_PROJECT_REF=your-project-ref
   ```
3. Verify: [test command]

## Troubleshooting

### Issue: MCP server not found
- Verify npx is installed: `npx --version`
- Check environment variables are set
- Try running MCP server manually

### Issue: Authentication failed
- Verify API key is correct
- Check key has required scopes
- Try regenerating key

## Security

Never commit API keys. Use `.env.local` file (gitignored).
```

---

## Success Criteria

- [ ] Critical MCP servers identified
- [ ] Configuration format understood
- [ ] Essential MCPs configured and tested
- [ ] API keys managed securely
- [ ] Agents can use MCP tools
- [ ] Setup documented
- [ ] Team trained on MCP setup

---

## Resources

### Documentation
- [MCP Specification](https://spec.modelcontextprotocol.io/)
- [GitHub Copilot Custom Agents](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents)
- [MCP Server Registry](https://github.com/modelcontextprotocol)
- [Anthropic MCP Servers](https://github.com/anthropics/anthropic-quickstarts/tree/main/mcp)

### MCP Server Packages
- [@modelcontextprotocol/server-github](https://www.npmjs.com/package/@modelcontextprotocol/server-github)
- [@modelcontextprotocol/server-memory](https://www.npmjs.com/package/@modelcontextprotocol/server-memory)
- [@supabase/mcp-server-supabase](https://www.npmjs.com/package/@supabase/mcp-server-supabase)
- [firecrawl-mcp](https://www.npmjs.com/package/firecrawl-mcp)

---

## Next Step

Proceed to [Step 8: Testing & Validation](./08-testing-validation.md)

---

*Last Updated*: 2026-01-21  
*Status*: 📝 Ready for Execution (after resolving Q1-Q5)
