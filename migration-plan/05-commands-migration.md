# Step 5: Commands Migration

**Priority**: Medium  
**Estimated Time**: 4-6 hours  
**Prerequisites**: Steps 1-4 complete  
**Status**: 📝 Ready to Execute

---

## Objective

Adapt Claude Code slash commands (`/tdd`, `/plan`, etc.) to GitHub Copilot workflows. Since Copilot doesn't have universal slash commands, we'll use a **hybrid approach**: custom agents + prompt files + documentation.

---

## The Challenge

### What We Had (Claude Code)
```
User: /tdd create a login function
```
- Single command invokes specialized agent
- Pre-configured workflow
- Universal syntax across IDE

### What Copilot Offers
- **Custom agents** (callable in IDEs)
- **Prompt files** (reusable instruction snippets)
- **No universal slash command system**

---

## Source Files Analysis

### Current Commands Inventory

| Command | Purpose | Invokes Agent | Complexity | Priority |
|---------|---------|---------------|------------|----------|
| `/tdd` | TDD workflow | tdd-guide | Medium | ⭐⭐⭐ Critical |
| `/plan` | Planning | planner | Medium | ⭐⭐⭐ Critical |
| `/code-review` | Quality review | code-reviewer | Low | ⭐⭐ High |
| `/e2e` | E2E tests | e2e-runner | Medium | ⭐⭐ High |
| `/build-fix` | Fix builds | build-error-resolver | Medium | ⭐⭐ High |
| `/refactor-clean` | Cleanup | refactor-cleaner | Low | ⭐ Medium |
| `/update-docs` | Sync docs | doc-updater | Low | ⭐ Medium |
| `/test-coverage` | Coverage | tdd-guide | Low | ⭐ Medium |
| `/update-codemaps` | Refresh maps | doc-updater | Low | ⭐ Low |
| `/learn` | Learning | N/A | Low | ⭐ Low |

**Total**: 10 commands

---

## Migration Strategy

### Approach: Three-Tier System

**Tier 1: Custom Agent Invocation** (Primary)
- Use custom agents for complex workflows
- Example: `@tdd-guide create login function`

**Tier 2: Prompt Files** (Fallback)
- Store reusable prompts in `.github/prompts/`
- Copy-paste or reference as needed
- Example: `.github/prompts/tdd-workflow.md`

**Tier 3: Documentation** (Reference)
- Document workflow in README/CONTRIBUTING
- Provide step-by-step instructions
- Example: "How to start TDD workflow"

---

## Command-by-Command Migration

### 1. /tdd Command (CRITICAL)

**Source**: `/commands/tdd.md`

**Claude Invocation**: `/tdd I need a function to calculate market liquidity`

**Copilot Approach**:

**Primary**: Custom Agent
```
@tdd-guide I need a function to calculate market liquidity
```

**Fallback**: Prompt File
- **File**: `.github/prompts/tdd-workflow.md`
- **Content**:
```markdown
# TDD Workflow

Use this prompt to start a TDD workflow:

---

I need to implement [FEATURE] following TDD.

Please:
1. Define interfaces/types first
2. Write failing tests (RED)
3. Run tests and verify they fail
4. Write minimal implementation (GREEN)
5. Run tests and verify they pass
6. Refactor while keeping tests green
7. Verify 80%+ coverage

Reference the `tdd-workflow` skill for detailed patterns.

---

Replace [FEATURE] with your feature description.
```

**Documentation**: Add to CONTRIBUTING.md
```markdown
## Test-Driven Development

To implement features with TDD:
1. Invoke the TDD agent: `@tdd-guide [feature description]`
2. Or use the prompt file: `.github/prompts/tdd-workflow.md`
3. Follow the RED → GREEN → REFACTOR cycle
```

**Target Files**:
- Agent: `.github/agents/tdd-guide.agent.md` (already migrated in Step 3)
- Prompt: `.github/prompts/tdd-workflow.md`
- Docs: `CONTRIBUTING.md` section

---

### 2. /plan Command (CRITICAL)

**Source**: `/commands/plan.md`

**Claude Invocation**: `/plan implement user authentication`

**Copilot Approach**:

**Primary**: Custom Agent
```
@planner implement user authentication
```

**Fallback**: Prompt File
- **File**: `.github/prompts/planning-workflow.md`
- **Content**:
```markdown
# Planning Workflow

Use this prompt to create an implementation plan:

---

I need to implement [FEATURE].

Please create a comprehensive plan including:
1. Requirements analysis
2. Architecture review
3. Step-by-step implementation breakdown
4. Testing strategy
5. Risk assessment
6. Success criteria

Use the plan format from the planner agent.

---

Replace [FEATURE] with your feature description.
```

**Documentation**: Add to CONTRIBUTING.md

**Target Files**:
- Agent: `.github/agents/planner.agent.md` (Step 3)
- Prompt: `.github/prompts/planning-workflow.md`
- Docs: `CONTRIBUTING.md` section

---

### 3. /code-review Command (HIGH)

**Source**: `/commands/code-review.md`

**Claude Invocation**: `/code-review`

**Copilot Approach**:

**Primary**: Custom Agent
```
@code-reviewer review current changes
```

**Fallback**: Prompt File
- **File**: `.github/prompts/code-review-request.md`
- **Content**:
```markdown
# Code Review Request

Please review the current changes for:
- Code quality and maintainability
- Security vulnerabilities
- Performance issues
- Best practices adherence
- Test coverage

Provide feedback with severity levels:
- CRITICAL: Must fix before merge
- HIGH: Should fix before merge
- MEDIUM: Consider fixing
- LOW: Optional improvement

Reference the security and testing rules in AGENTS.md.
```

**GitHub Integration**:
- Use GitHub PR review features
- Configure Copilot to assist with PR reviews
- Add reviewer checklist to PR template

**Target Files**:
- Agent: `.github/agents/code-reviewer.agent.md` (Step 3)
- Prompt: `.github/prompts/code-review-request.md`
- Template: `.github/pull_request_template.md`

---

### 4. /e2e Command (HIGH)

**Source**: `/commands/e2e.md`

**Claude Invocation**: `/e2e test user can search markets`

**Copilot Approach**:

**Primary**: Custom Agent
```
@e2e-runner test user can search markets
```

**Fallback**: Prompt File
- **File**: `.github/prompts/e2e-test-generation.md`
- **Content**:
```markdown
# E2E Test Generation

Generate Playwright E2E tests for: [USER FLOW]

Requirements:
1. Use semantic selectors (data-testid, role, text)
2. Include proper waits and assertions
3. Test happy path and edge cases
4. Follow existing test structure
5. Include setup/teardown

Example:
```typescript
test('user can [action]', async ({ page }) => {
  // Test implementation
})
```

Reference the e2e-runner agent for patterns.
```

**Target Files**:
- Agent: `.github/agents/e2e-runner.agent.md` (Step 3)
- Prompt: `.github/prompts/e2e-test-generation.md`

---

### 5. /build-fix Command (HIGH)

**Source**: `/commands/build-fix.md`

**Claude Invocation**: `/build-fix`

**Copilot Approach**:

**Primary**: Custom Agent
```
@build-error-resolver fix current build errors
```

**Fallback**: Prompt File
- **File**: `.github/prompts/build-fix-workflow.md`
- **Content**:
```markdown
# Build Error Fixing Workflow

Fix the current build errors following these steps:

1. Run build command and capture full error output
2. Analyze error messages systematically
3. Identify root cause (TS errors, deps, config, etc.)
4. Apply minimal fix
5. Verify build passes
6. Run tests to ensure no regressions

Common error types:
- TypeScript errors
- Missing dependencies
- Configuration issues
- Import/export problems

Reference the build-error-resolver agent for patterns.
```

**Target Files**:
- Agent: `.github/agents/build-error-resolver.agent.md` (Step 3)
- Prompt: `.github/prompts/build-fix-workflow.md`

---

### 6. /refactor-clean Command (MEDIUM)

**Source**: `/commands/refactor-clean.md`

**Claude Invocation**: `/refactor-clean`

**Copilot Approach**:

**Primary**: Custom Agent
```
@refactor-cleaner identify and remove dead code
```

**Fallback**: Prompt File
- **File**: `.github/prompts/refactoring-cleanup.md`

**Target Files**:
- Agent: `.github/agents/refactor-cleaner.agent.md` (Step 3)
- Prompt: `.github/prompts/refactoring-cleanup.md`

---

### 7. /update-docs Command (MEDIUM)

**Source**: `/commands/update-docs.md`

**Claude Invocation**: `/update-docs`

**Copilot Approach**:

**Primary**: Custom Agent
```
@doc-updater sync documentation with code changes
```

**Fallback**: Prompt File
- **File**: `.github/prompts/documentation-sync.md`

**Target Files**:
- Agent: `.github/agents/doc-updater.agent.md` (Step 3)
- Prompt: `.github/prompts/documentation-sync.md`

---

### 8. /test-coverage Command (MEDIUM)

**Source**: `/commands/test-coverage.md`

**Claude Invocation**: `/test-coverage`

**Copilot Approach**:

**Primary**: Direct Prompt (no agent needed)
```
Analyze test coverage and identify gaps.
Run: npm run test:coverage
Review the report and suggest tests for uncovered code.
```

**Fallback**: Prompt File
- **File**: `.github/prompts/coverage-analysis.md`

**GitHub Actions Integration**:
- Add coverage reporting to CI
- Use Codecov or similar service
- Fail PR if coverage drops below 80%

**Target Files**:
- Prompt: `.github/prompts/coverage-analysis.md`
- Workflow: `.github/workflows/test-coverage.yml` (Step 6)

---

### 9. /update-codemaps Command (LOW)

**Source**: `/commands/update-codemaps.md`

**Purpose**: Refresh documentation maps (Claude-specific)

**Decision**: **Do not migrate**

**Rationale**: Claude Code-specific feature, no direct Copilot equivalent

**Alternative**: Use GitHub wiki, README sections, or architecture diagrams

---

### 10. /learn Command (LOW)

**Source**: `/commands/learn.md`

**Purpose**: Continuous learning from sessions

**Decision**: **Do not migrate**

**Rationale**: Depends on Claude hooks and session persistence

**Alternative**: Manual knowledge capture in Issues/Discussions

---

## Implementation Checklist

### Pre-Migration
- [ ] Create `.github/prompts/` directory
- [ ] Review all command files
- [ ] Identify agent dependencies
- [ ] Plan documentation structure

### Prompt File Migration (For Each Command)
- [ ] Create prompt file in `.github/prompts/`
- [ ] Write clear, actionable prompt template
- [ ] Include placeholders for customization
- [ ] Add reference to relevant agent/skill
- [ ] Test prompt with Copilot

### Documentation Updates
- [ ] Update README.md with "Commands" section
- [ ] Update CONTRIBUTING.md with workflow instructions
- [ ] Create PR template with common workflows
- [ ] Add quick reference guide

### Testing
- [ ] Test each agent invocation
- [ ] Test each prompt file
- [ ] Verify documentation accuracy
- [ ] Gather user feedback

---

## Example Prompt File: TDD Workflow

**File**: `.github/prompts/tdd-workflow.md`

```markdown
# TDD Workflow Prompt

Use this prompt to implement features following test-driven development.

## Basic Usage

Copy and customize this prompt:

---

I need to implement [FEATURE DESCRIPTION] following TDD principles.

Please help me:

1. **Define Interfaces** (SCAFFOLD)
   - Create type definitions for inputs/outputs
   - Define function signatures
   - Add TODO comments for implementation

2. **Write Failing Tests** (RED)
   - Write comprehensive test cases
   - Include happy path, edge cases, and error scenarios
   - Run tests and verify they fail for the right reason

3. **Implement Code** (GREEN)
   - Write minimal code to make tests pass
   - Run tests and verify they pass
   - Keep implementation simple and focused

4. **Refactor** (IMPROVE)
   - Improve code quality while keeping tests green
   - Extract constants, improve naming, reduce duplication
   - Run tests after each refactoring step

5. **Verify Coverage** (VALIDATE)
   - Check test coverage: `npm run test:coverage`
   - Ensure 80%+ coverage
   - Add tests for any gaps

---

**Remember**: RED → GREEN → REFACTOR → REPEAT

## Examples

### Feature Implementation
```
I need to implement a user authentication function following TDD.
[Use the prompt above]
```

### Bug Fix
```
I need to fix a bug where users can't log in with special characters in their password, following TDD.
[First write a test that reproduces the bug, then fix it]
```

## Related Resources
- Agent: `@tdd-guide`
- Skill: `tdd-workflow`
- Documentation: See AGENTS.md for testing requirements
```

---

## Documentation Structure

### README.md Addition

```markdown
## Developer Workflows

This project uses GitHub Copilot custom agents and prompt files for common workflows.

### Quick Reference

| Workflow | Invocation | Documentation |
|----------|-----------|---------------|
| TDD Development | `@tdd-guide [feature]` | [Prompts](. github/prompts/tdd-workflow.md) |
| Feature Planning | `@planner [feature]` | [Prompts](.github/prompts/planning-workflow.md) |
| Code Review | `@code-reviewer review` | [Prompts](.github/prompts/code-review-request.md) |
| E2E Testing | `@e2e-runner [flow]` | [Prompts](.github/prompts/e2e-test-generation.md) |
| Build Fixing | `@build-error-resolver fix` | [Prompts](.github/prompts/build-fix-workflow.md) |

### Custom Agents

All custom agents are in `.github/agents/*.agent.md`.

Invoke with: `@agent-name [your request]`

### Prompt Files

Reusable prompts are in `.github/prompts/*.md`.

Use when:
- Agent invocation isn't working
- You want to customize the workflow
- You're using a surface without agent support

### Detailed Guides

See [CONTRIBUTING.md](./CONTRIBUTING.md) for detailed workflow documentation.
```

---

## Open Questions

### Critical
- [ ] **Q1**: What is the exact syntax for invoking custom agents in VS Code?
  - **Research**: VS Code Copilot documentation
  - **Verification**: Test with migrated agents
  - **Impact**: HIGH - affects primary strategy
  - **Expected**: `@agent-name` or similar

- [ ] **Q2**: Are custom agents available in GitHub.com Copilot interface?
  - **Research**: Test on GitHub.com
  - **Verification**: Open PR, try agent invocation
  - **Impact**: HIGH - affects multi-surface support

### Important
- [ ] **Q3**: Can prompt files be referenced automatically (like snippets)?
  - **Research**: Test VS Code integration
  - **Impact**: MEDIUM - affects UX
  - **Potential**: Create VS Code snippets that load prompt files

- [ ] **Q4**: Is there a Copilot CLI that supports custom agents?
  - **Research**: Check `gh copilot` capabilities
  - **Impact**: MEDIUM - affects terminal workflow

---

## Alternative Approaches

### Option A: VS Code Snippets
Create VS Code snippets that trigger agent invocations:
```json
{
  "TDD Workflow": {
    "prefix": "tdd",
    "body": "@tdd-guide $1",
    "description": "Start TDD workflow"
  }
}
```

### Option B: GitHub Actions Commands
Use issue comments or PR comments to trigger workflows:
```
/tdd feature-name
```
GitHub Action interprets and responds with agent output.

### Option C: Custom CLI Wrapper
Create a CLI tool that:
1. Reads command
2. Calls Copilot API with appropriate agent/prompt
3. Returns results

**Note**: Evaluate these if primary approach is insufficient.

---

## Success Criteria

- [ ] All high-priority commands have agent mappings
- [ ] Prompt files created for all commands
- [ ] Documentation updated (README + CONTRIBUTING)
- [ ] User guide created with examples
- [ ] Agent invocations tested
- [ ] Prompt files tested
- [ ] Open questions resolved or documented

---

## Resources

### Documentation
- [GitHub Copilot Chat](https://docs.github.com/en/copilot/using-github-copilot/asking-github-copilot-questions-in-your-ide)
- [VS Code Copilot Commands](https://code.visualstudio.com/docs/copilot/copilot-chat)
- [Copilot CLI](https://docs.github.com/en/copilot/github-copilot-in-the-cli)

### Tools
- VS Code Snippets
- Markdown templates
- Shell aliases (for CLI workflows)

---

## Next Step

Proceed to [Step 6: Hooks Replacement](./06-hooks-replacement.md)

---

*Last Updated*: 2026-01-21  
*Status*: 📝 Ready for Execution
