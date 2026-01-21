# Step 3: Agents Migration

**Priority**: Critical  
**Estimated Time**: 6-8 hours  
**Prerequisites**: Steps 1-2 complete  
**Status**: 📝 Ready to Execute

---

## Objective

Migrate all 9 Claude Code agents from `/agents/*.md` to GitHub Copilot custom agents in `.github/agents/*.agent.md`. This is a **critical** migration that enables specialized delegation workflows.

---

## Source Files Analysis

### Current Agents Inventory

| Agent | Purpose | Tools | Model | Lines |
|-------|---------|-------|-------|-------|
| `planner.md` | Feature planning | Read, Grep, Glob | opus | ~120 |
| `architect.md` | System design | Read, Grep, Glob | opus | ~100 |
| `tdd-guide.md` | TDD enforcement | All | opus | ~150 |
| `code-reviewer.md` | Quality review | Read, Grep, Glob, Bash | opus | ~130 |
| `security-reviewer.md` | Security analysis | Read, Grep, Glob | opus | ~140 |
| `build-error-resolver.md` | Build fixing | All | sonnet | ~110 |
| `e2e-runner.md` | E2E testing | All | opus | ~120 |
| `refactor-cleaner.md` | Code cleanup | All | sonnet | ~100 |
| `doc-updater.md` | Docs sync | Read, Write, Glob | sonnet | ~90 |

**Total**: 9 agents, ~1,160 lines

---

## GitHub Copilot Custom Agent Format

### Official Specification

According to [GitHub Docs - Custom Agents](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents):

```markdown
---
description: Brief description of the agent's purpose
tools:
  - bash
  - grep
  - view
model: gpt-4o  # Optional
target: vs_code  # Optional: vs_code, github, cli
---

# Agent Name

Main instructions and behavioral guidelines...

## Role

Description of agent's role...

## Process

Step-by-step process...
```

**Key Differences from Claude**:
- No `name` field (derived from filename)
- `description` instead of `description + name`
- `tools` is YAML array, not comma-separated
- `model` may support different values
- `target` specifies where agent is available

---

## Migration Strategy

### Step-by-Step Process

1. Create `.github/agents/` directory
2. For each agent:
   - Convert YAML frontmatter to Copilot format
   - Update tool references
   - Remove Claude-specific references
   - Rename `{name}.md` → `{name}.agent.md`
3. Test each agent individually
4. Document any limitations

---

## Agent-by-Agent Migration

### 1. Planner Agent

**Source**: `/agents/planner.md`

**Claude Frontmatter**:
```yaml
---
name: planner
description: Expert planning specialist for complex features and refactoring. Use PROACTIVELY when users request feature implementation, architectural changes, or complex refactoring. Automatically activated for planning tasks.
tools: Read, Grep, Glob
model: opus
---
```

**Copilot Frontmatter**:
```yaml
---
description: Expert planning specialist for complex features and refactoring. Creates comprehensive implementation plans with step-by-step breakdowns, dependency analysis, and risk assessment.
tools:
  - view
  - grep
  - glob
---
```

**Content Changes**:
- Keep planning process intact
- Update tool names: `Read` → `view`
- Remove model specification (may not be supported)
- Keep plan format and best practices

**Target**: `.github/agents/planner.agent.md`

---

### 2. Architect Agent

**Source**: `/agents/architect.md`

**Copilot Frontmatter**:
```yaml
---
description: System architecture and design decision specialist. Analyzes codebases, suggests architectural patterns, and makes technology stack decisions.
tools:
  - view
  - grep
  - glob
---
```

**Content Changes**:
- Keep architectural decision process
- Update examples to be platform-agnostic
- Remove Claude-specific model optimization advice

**Target**: `.github/agents/architect.agent.md`

---

### 3. TDD Guide Agent

**Source**: `/agents/tdd-guide.md`

**Copilot Frontmatter**:
```yaml
---
description: Test-driven development guide. Enforces TDD workflow - writes tests FIRST, then implements code to pass tests. Ensures 80%+ coverage.
tools:
  - view
  - grep
  - glob
  - bash
  - edit
  - create
---
```

**Content Changes**:
- Keep TDD cycle (RED → GREEN → REFACTOR)
- Link to `tdd-workflow` skill
- Emphasize test-first approach
- Keep coverage requirements

**Target**: `.github/agents/tdd-guide.agent.md`

---

### 4. Code Reviewer Agent

**Source**: `/agents/code-reviewer.md`

**Copilot Frontmatter**:
```yaml
---
description: Senior code reviewer specializing in quality, security, and maintainability. Reviews code for bugs, security vulnerabilities, performance issues, and adherence to best practices.
tools:
  - view
  - grep
  - glob
  - bash
---
```

**Content Changes**:
- Keep review checklist intact
- Reference security rules from AGENTS.md
- Add GitHub-specific PR review workflow
- Keep severity levels (CRITICAL, HIGH, MEDIUM, LOW)

**Target**: `.github/agents/code-reviewer.agent.md`

---

### 5. Security Reviewer Agent

**Source**: `/agents/security-reviewer.md`

**Copilot Frontmatter**:
```yaml
---
description: Security specialist for vulnerability analysis. Performs deep security audits focusing on OWASP Top 10, secret exposure, injection attacks, and authentication flaws.
tools:
  - view
  - grep
  - glob
---
```

**Content Changes**:
- Keep OWASP checklist
- Reference security rules from AGENTS.md
- Link to `security-review` skill
- Add GitHub security features (Dependabot, CodeQL)

**Target**: `.github/agents/security-reviewer.agent.md`

---

### 6. Build Error Resolver Agent

**Source**: `/agents/build-error-resolver.md`

**Copilot Frontmatter**:
```yaml
---
description: Build and compilation error specialist. Diagnoses and fixes TypeScript, linting, dependency, and configuration errors systematically.
tools:
  - view
  - grep
  - glob
  - bash
  - edit
---
```

**Content Changes**:
- Keep error diagnosis process
- Update dependency commands (npm, pnpm, yarn, bun)
- Keep step-by-step debugging approach

**Target**: `.github/agents/build-error-resolver.agent.md`

---

### 7. E2E Runner Agent

**Source**: `/agents/e2e-runner.md`

**Copilot Frontmatter**:
```yaml
---
description: End-to-end testing specialist using Playwright. Generates comprehensive E2E tests for critical user flows with proper selectors and assertions.
tools:
  - view
  - grep
  - glob
  - bash
  - edit
  - create
---
```

**Content Changes**:
- Keep Playwright patterns
- Keep selector best practices
- Update test organization structure
- Keep debugging strategies

**Target**: `.github/agents/e2e-runner.agent.md`

---

### 8. Refactor Cleaner Agent

**Source**: `/agents/refactor-cleaner.md`

**Copilot Frontmatter**:
```yaml
---
description: Code refactoring and cleanup specialist. Identifies and removes dead code, unused dependencies, and implements refactoring patterns safely.
tools:
  - view
  - grep
  - glob
  - bash
  - edit
---
```

**Content Changes**:
- Keep refactoring safety checklist
- Keep dead code detection strategy
- Maintain test-driven refactoring approach

**Target**: `.github/agents/refactor-cleaner.agent.md`

---

### 9. Doc Updater Agent

**Source**: `/agents/doc-updater.md`

**Copilot Frontmatter**:
```yaml
---
description: Documentation synchronization specialist. Keeps README, API docs, and code comments in sync with implementation changes.
tools:
  - view
  - grep
  - glob
  - edit
---
```

**Content Changes**:
- Keep documentation structure
- Keep sync checklist
- Update markdown linting advice

**Target**: `.github/agents/doc-updater.agent.md`

---

## Tool Mapping

### Claude Tool Names → Copilot Tool Names

| Claude | Copilot | Notes |
|--------|---------|-------|
| `Read` | `view` | File reading |
| `Grep` | `grep` | Content search |
| `Glob` | `glob` | File pattern matching |
| `Bash` | `bash` | Shell commands |
| `Edit` | `edit` | File editing |
| `Write` | `create` | File creation |
| "All" | List specific tools | Be explicit |

**Important**: Copilot may have different tool names or capabilities. Verify by testing.

---

## Open Questions (CRITICAL)

### Must Resolve Before Migration

- [ ] **Q1**: What is the exact list of available tools in Copilot custom agents?
  - **Research**: Check GitHub Docs, test with minimal agent
  - **Impact**: HIGH - affects tool specifications
  - **Verification**: Create test agent, ask Copilot what tools it has
  - **Source**: [GitHub Docs - Custom Agents](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents)

- [ ] **Q2**: Can custom agents specify model preference (gpt-4o, gpt-4, etc.)?
  - **Research**: Check YAML schema in docs
  - **Impact**: MEDIUM - affects agent performance
  - **Verification**: Test with `model:` field
  - **Note**: If not supported, accept default model

- [ ] **Q3**: How do we invoke custom agents? Syntax?
  - **Research**: Check VS Code Copilot, GitHub interface
  - **Impact**: HIGH - affects user workflow
  - **Verification**: Test invocation methods
  - **Expected**: `@agent-name` or similar

- [ ] **Q4**: Can agents reference skills or other agents?
  - **Research**: Test cross-references
  - **Impact**: MEDIUM - affects delegation chains
  - **Verification**: Create agent that references skill

- [ ] **Q5**: What is the `target` field? Required or optional?
  - **Research**: Check docs and schema
  - **Impact**: LOW - affects agent availability
  - **Verification**: Test with/without target
  - **Values**: `vs_code`, `github`, `cli` (unconfirmed)

---

## Implementation Checklist

### Pre-Migration
- [ ] Create `.github/agents/` directory
- [ ] Verify GitHub Copilot access/permissions
- [ ] Review all 9 agent files
- [ ] Resolve critical open questions (Q1-Q5)

### Migration (For Each Agent)
- [ ] Convert YAML frontmatter
- [ ] Update tool references
- [ ] Remove Claude-specific content
- [ ] Rename to `*.agent.md`
- [ ] Move to `.github/agents/`
- [ ] Test agent individually
- [ ] Document any issues

### Post-Migration
- [ ] Test all 9 agents
- [ ] Document invocation syntax
- [ ] Update AGENTS.md with agent references
- [ ] Create quick reference guide
- [ ] Update README.md

---

## Testing Each Agent

### Test Template

For each agent:

1. **Test Invocation**: Can you call the agent?
   ```
   # In VS Code Copilot Chat
   @planner create a user authentication system
   ```

2. **Test Behavior**: Does it follow instructions?
   - Verify output matches expected behavior
   - Check if it uses specified tools
   - Confirm it follows process from agent file

3. **Test Tool Usage**: Can it access tools?
   - Watch for tool invocations
   - Verify tool results are accurate
   - Check for permission/access issues

4. **Test Edge Cases**:
   - Large codebases
   - Missing dependencies
   - Complex scenarios

### Success Criteria Per Agent
- [ ] Agent is invocable
- [ ] Agent follows its defined role
- [ ] Agent uses appropriate tools
- [ ] Agent produces quality output
- [ ] No errors or warnings

---

## Known Limitations

### Copilot vs. Claude Differences

1. **Model Selection**: May not support `model:` field
   - **Workaround**: Accept default model
   - **Impact**: Performance may vary

2. **Tool Surface**: May have different tools available
   - **Workaround**: Use available tools, adjust expectations
   - **Impact**: Some capabilities may be limited

3. **Context Window**: May differ from Claude
   - **Workaround**: Keep agent instructions concise
   - **Impact**: May need to simplify complex agents

4. **Invocation Method**: Syntax differs from Claude `/agent-name`
   - **Workaround**: Learn Copilot invocation syntax
   - **Impact**: User training needed

---

## Rollback Plan

If custom agents don't work as expected:

**Option A**: Inline into AGENTS.md
- Copy agent instructions into AGENTS.md sections
- Use as general guidelines instead of callable agents

**Option B**: Use as Prompt Files
- Store as `.github/prompts/*.md`
- Copy-paste into Copilot chat as needed

**Option C**: Revert to Claude
- Keep original `/agents/*.md` for Claude users
- Document dual-support strategy

---

## Example: Planner Agent (Complete)

```markdown
---
description: Expert planning specialist for complex features and refactoring. Creates comprehensive implementation plans with step-by-step breakdowns, dependency analysis, and risk assessment.
tools:
  - view
  - grep
  - glob
---

# Planner Agent

You are an expert planning specialist focused on creating comprehensive, actionable implementation plans.

## Your Role

- Analyze requirements and create detailed implementation plans
- Break down complex features into manageable steps
- Identify dependencies and potential risks
- Suggest optimal implementation order
- Consider edge cases and error scenarios

## Planning Process

### 1. Requirements Analysis
- Understand the feature request completely
- Ask clarifying questions if needed
- Identify success criteria
- List assumptions and constraints

### 2. Architecture Review
- Analyze existing codebase structure
- Identify affected components
- Review similar implementations
- Consider reusable patterns

### 3. Step Breakdown
Create detailed steps with:
- Clear, specific actions
- File paths and locations
- Dependencies between steps
- Estimated complexity
- Potential risks

### 4. Implementation Order
- Prioritize by dependencies
- Group related changes
- Minimize context switching
- Enable incremental testing

## Plan Format

```markdown
# Implementation Plan: [Feature Name]

## Overview
[2-3 sentence summary]

## Requirements
- [Requirement 1]
- [Requirement 2]

## Architecture Changes
- [Change 1: file path and description]
- [Change 2: file path and description]

## Implementation Steps

### Phase 1: [Phase Name]
1. **[Step Name]** (File: path/to/file.ts)
   - Action: Specific action to take
   - Why: Reason for this step
   - Dependencies: None / Requires step X
   - Risk: Low/Medium/High

### Phase 2: [Phase Name]
...

## Testing Strategy
- Unit tests: [files to test]
- Integration tests: [flows to test]
- E2E tests: [user journeys to test]

## Risks & Mitigations
- **Risk**: [Description]
  - Mitigation: [How to address]

## Success Criteria
- [ ] Criterion 1
- [ ] Criterion 2
```

## Best Practices

1. **Be Specific**: Use exact file paths, function names, variable names
2. **Consider Edge Cases**: Think about error scenarios, null values, empty states
3. **Minimize Changes**: Prefer extending existing code over rewriting
4. **Maintain Patterns**: Follow existing project conventions
5. **Enable Testing**: Structure changes to be easily testable
6. **Think Incrementally**: Each step should be verifiable
7. **Document Decisions**: Explain why, not just what

## Red Flags to Check

- Large functions (>50 lines)
- Deep nesting (>4 levels)
- Duplicated code
- Missing error handling
- Hardcoded values
- Missing tests
- Performance bottlenecks

**Remember**: A great plan is specific, actionable, and considers both the happy path and edge cases.
```

---

## Success Criteria

- [ ] All 9 agents migrated to `.github/agents/*.agent.md`
- [ ] YAML frontmatter converted correctly
- [ ] Tool references updated
- [ ] Claude-specific content removed
- [ ] All agents tested and working
- [ ] Invocation syntax documented
- [ ] Open questions resolved or documented
- [ ] README updated with agent usage

---

## Resources

### Documentation
- [GitHub Docs - Creating Custom Agents](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents)
- [GitHub Copilot Agent Overview](https://docs.github.com/en/copilot/using-github-copilot/using-github-copilot-agents)
- [VS Code Copilot Extensions](https://code.visualstudio.com/docs/copilot/copilot-extensibility-overview)

### Tools
- VS Code with Copilot extension
- GitHub Copilot CLI (if applicable)
- YAML validator

---

## Next Step

Proceed to [Step 4: Skills Migration](./04-skills-migration.md)

---

*Last Updated*: 2026-01-21  
*Status*: 📝 Ready for Execution
