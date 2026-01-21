# Step 2: Rules Migration

**Priority**: Critical  
**Estimated Time**: 4-6 hours  
**Prerequisites**: Step 1 complete  
**Status**: 📝 Ready to Execute

---

## Objective

Migrate all Claude Code rules from `/rules/*.md` to GitHub Copilot's instruction system (`AGENTS.md` and nested `AGENTS.md` files). This is the **highest priority** migration as it establishes the governance foundation for all agent behavior.

---

## Why Rules First?

1. **Foundation**: Rules define behavior for all subsequent components
2. **High Portability**: Direct mapping to AGENTS.md (no complex transformation)
3. **Immediate Value**: Works instantly with Copilot coding agent
4. **Low Risk**: Well-documented feature with stable support

---

## Source Files Analysis

### Current Rules Inventory

| File | Purpose | Lines | Criticality |
|------|---------|-------|-------------|
| `security.md` | Security checks, secret management | ~37 | **Critical** |
| `testing.md` | TDD requirements, 80% coverage | ~50 | **Critical** |
| `coding-style.md` | Immutability, file organization | ~60 | High |
| `git-workflow.md` | Commit format, PR process | ~45 | High |
| `agents.md` | Delegation guidelines | ~40 | High |
| `performance.md` | Model selection, context mgmt | ~35 | Medium |
| `patterns.md` | API formats, common patterns | ~55 | Medium |
| `hooks.md` | Hook documentation | ~30 | Low (deprecated) |

**Total**: 8 files, ~352 lines of rules

---

## Target Structure

### Primary: Root AGENTS.md

```markdown
# Development Guidelines

## Security (MANDATORY)

[Content from security.md]

## Testing Requirements (MANDATORY)

[Content from testing.md]

## Coding Style

[Content from coding-style.md]

## Git Workflow

[Content from git-workflow.md]

## Agent Delegation

[Content from agents.md]

## Performance Guidelines

[Content from performance.md]

## Common Patterns

[Content from patterns.md]
```

### Optional: Nested AGENTS.md for Scoping

If different rules apply to different parts of the codebase:

```
src/
├── AGENTS.md                    # Backend-specific rules
├── api/
│   └── AGENTS.md                # API-specific rules
└── frontend/
    └── AGENTS.md                # Frontend-specific rules

tests/
└── AGENTS.md                    # Test-specific rules

docs/
└── AGENTS.md                    # Documentation-specific rules
```

---

## Migration Strategy

### Approach: Merged Single File vs. Modular Files

**Option A: Single Root AGENTS.md** ✅ RECOMMENDED
- **Pros**: Simple, global rules apply everywhere
- **Cons**: Large file (can be mitigated with sections)
- **Best for**: Consistent rules across entire codebase

**Option B: Multiple Nested AGENTS.md**
- **Pros**: Scoped rules, smaller files
- **Cons**: More complex to maintain
- **Best for**: Polyglot repos or distinct subsystems

**Decision**: Start with Option A, add nesting if needed

---

## Step-by-Step Migration Process

### 1. Prepare Root AGENTS.md Structure

```bash
# Create AGENTS.md at repository root
touch AGENTS.md
```

Template structure:
```markdown
# Development Guidelines for [Project Name]

This file contains instructions for GitHub Copilot when working in this repository.

## Table of Contents
1. [Security (MANDATORY)](#security-mandatory)
2. [Testing Requirements (MANDATORY)](#testing-requirements-mandatory)
3. [Coding Style](#coding-style)
4. [Git Workflow](#git-workflow)
5. [Agent Delegation](#agent-delegation)
6. [Performance Guidelines](#performance-guidelines)
7. [Common Patterns](#common-patterns)

---

## Security (MANDATORY)
...
```

### 2. Migrate Each Rule File

#### 2.1 Security Rules (CRITICAL)

**Source**: `/rules/security.md`

**Transformation**:
- Add "MANDATORY" emphasis
- Convert checklist to enforcement language
- Add severity levels where appropriate

**Key Content**:
```markdown
## Security (MANDATORY)

### Before ANY Commit
- ❌ BLOCK: No hardcoded secrets (API keys, passwords, tokens)
- ❌ BLOCK: All user inputs must be validated
- ❌ BLOCK: SQL injection prevention (use parameterized queries)
- ❌ BLOCK: XSS prevention (sanitize HTML output)
- ✅ VERIFY: CSRF protection enabled
- ✅ VERIFY: Authentication/authorization on all protected routes
- ✅ VERIFY: Rate limiting on all public endpoints
- ✅ VERIFY: Error messages don't leak sensitive data

### Secret Management
NEVER hardcode secrets. ALWAYS use environment variables.

Example:
```typescript
// ❌ WRONG
const apiKey = "sk-proj-xxxxx"

// ✅ CORRECT
const apiKey = process.env.OPENAI_API_KEY
if (!apiKey) {
  throw new Error('OPENAI_API_KEY not configured')
}
```

### Security Response Protocol
If a security issue is found:
1. STOP immediately
2. Use the **security-reviewer** custom agent
3. Fix CRITICAL issues before continuing
4. Rotate any exposed secrets
5. Review entire codebase for similar issues
```

#### 2.2 Testing Rules (CRITICAL)

**Source**: `/rules/testing.md`

**Transformation**:
- Emphasize TDD requirement
- Clarify 80% coverage mandate
- Link to TDD skill

**Key Content**:
```markdown
## Testing Requirements (MANDATORY)

### Test-Driven Development (TDD)
ALL new features and bug fixes MUST follow TDD:
1. Write tests FIRST (they should fail)
2. Write minimal code to pass tests
3. Refactor while keeping tests green
4. Verify 80%+ coverage

### Coverage Requirements
- **Minimum 80%** coverage for all code
- **100% required** for:
  - Financial calculations
  - Authentication logic
  - Security-critical code
  - Core business logic

### Test Types
- **Unit Tests**: Individual functions, components
- **Integration Tests**: API endpoints, database operations
- **E2E Tests**: Critical user flows (use e2e-runner agent)

### Before Committing
- ✅ All tests passing
- ✅ 80%+ coverage verified
- ✅ No skipped/disabled tests
- ✅ Test execution < 30s (unit tests)

For detailed TDD workflow, reference the `tdd-workflow` skill.
```

#### 2.3 Coding Style

**Source**: `/rules/coding-style.md`

**Transformation**:
- Keep concise but actionable
- Add language-specific sections if needed

**Key Content**:
```markdown
## Coding Style

### Immutability Preferred
- Use `const` over `let`
- Prefer `.map()`, `.filter()`, `.reduce()` over loops
- Avoid mutations; return new objects

### File Organization
- Keep files under 300 lines
- One export per file (components)
- Co-locate tests next to source files
- Use index files for clean exports

### Naming Conventions
- `camelCase` for variables and functions
- `PascalCase` for classes and components
- `SCREAMING_SNAKE_CASE` for constants
- Descriptive names over abbreviations

### Comments
- Prefer self-documenting code
- Comment "why", not "what"
- Use JSDoc for public APIs
- Remove commented-out code
```

#### 2.4 Git Workflow

**Source**: `/rules/git-workflow.md`

**Transformation**:
- Integrate with GitHub-native workflows
- Reference branch protection

**Key Content**:
```markdown
## Git Workflow

### Commit Messages
Format: `<type>(<scope>): <subject>`

Types:
- `feat`: New feature
- `fix`: Bug fix
- `refactor`: Code refactoring
- `test`: Adding tests
- `docs`: Documentation
- `chore`: Maintenance

Example: `feat(auth): add OAuth2 login flow`

### Branch Strategy
- `main`: Production-ready code
- `develop`: Integration branch
- `feature/*`: New features
- `fix/*`: Bug fixes
- `hotfix/*`: Critical production fixes

### Pull Request Process
1. Create feature branch from `develop`
2. Write code with tests
3. Run linting and tests locally
4. Create PR with description
5. Wait for CI checks to pass
6. Request review from code-reviewer agent
7. Address feedback
8. Merge with squash commit

### Before Pushing
- ✅ All tests passing
- ✅ No linting errors
- ✅ No console.log statements
- ✅ Secrets removed from code
```

#### 2.5 Agent Delegation

**Source**: `/rules/agents.md`

**Transformation**:
- Update agent invocation syntax for Copilot
- Reference .github/agents/ location

**Key Content**:
```markdown
## Agent Delegation

### When to Use Custom Agents

Delegate to specialized agents for:
- **planner**: Complex feature planning, architectural decisions
- **tdd-guide**: TDD workflow enforcement
- **code-reviewer**: Quality and security review
- **security-reviewer**: Vulnerability analysis
- **build-error-resolver**: Build error fixing
- **e2e-runner**: E2E test generation
- **refactor-cleaner**: Dead code cleanup
- **doc-updater**: Documentation synchronization

### How to Invoke
Custom agents are available in `.github/agents/*.agent.md`.
Reference them by name when you need specialized help.

### Delegation Guidelines
- Use **planner** for multi-file changes
- Use **tdd-guide** for all new features
- Use **code-reviewer** before finalizing PRs
- Use **security-reviewer** for auth/payment code
- Use **architect** for system design questions
```

#### 2.6 Performance Guidelines

**Source**: `/rules/performance.md`

**Transformation**:
- Focus on actionable optimizations
- Add Copilot-specific guidance

**Key Content**:
```markdown
## Performance Guidelines

### Code Performance
- Avoid N+1 queries (use eager loading)
- Use database indexes for frequent queries
- Cache expensive computations
- Debounce user inputs
- Lazy load large components

### Context Window Management
- Keep instructions concise
- Reference skills instead of copying content
- Use agent delegation for focused tasks
- Compact context when exceeding limits

### Build Performance
- Use code splitting for large apps
- Optimize images and assets
- Tree-shake unused dependencies
- Use production builds for deployment
```

#### 2.7 Common Patterns

**Source**: `/rules/patterns.md`

**Transformation**:
- Extract reusable patterns
- Link to skills for detailed implementation

**Key Content**:
```markdown
## Common Patterns

### API Response Format
```typescript
// Success response
{
  success: true,
  data: T,
  meta?: { ... }
}

// Error response
{
  success: false,
  error: {
    code: 'ERROR_CODE',
    message: 'User-friendly message',
    details?: { ... }
  }
}
```

### Error Handling
```typescript
try {
  // Operation
} catch (error) {
  logger.error('Context', error)
  return errorResponse(error)
}
```

### React Hooks Pattern
- Use custom hooks for reusable logic
- Co-locate hooks with components
- Name hooks with `use` prefix
- Keep hooks focused and small

For more patterns, reference the `coding-standards`, `backend-patterns`, and `frontend-patterns` skills.
```

### 3. Handle hooks.md

**Source**: `/rules/hooks.md`

**Decision**: **DO NOT migrate directly**

**Rationale**: Hooks are being replaced by GitHub Actions (Step 6). The documentation will be replaced with GitHub Actions documentation.

**Action**: Create a note in AGENTS.md:
```markdown
## Automation (GitHub Actions)

This project uses GitHub Actions for automated checks:
- Code quality (linting, formatting)
- Test execution
- Security scanning
- Coverage reporting

Refer to `.github/workflows/` for automation configuration.
```

### 4. Validate AGENTS.md

**Checklist**:
- [ ] All 7 rules migrated (skip hooks.md)
- [ ] Markdown formatting correct
- [ ] Links to skills work
- [ ] Agent references updated
- [ ] Examples are accurate
- [ ] No Claude-specific references
- [ ] File size reasonable (< 1000 lines recommended)

---

## Testing the Migration

### Test 1: Basic Recognition

1. Commit AGENTS.md to repository
2. Open file in VS Code with Copilot
3. Ask Copilot: "What are the security requirements for this project?"
4. **Expected**: Copilot should reference rules from AGENTS.md

### Test 2: Enforcement

1. Ask Copilot to write code that violates a rule (e.g., "add an API key as a constant")
2. **Expected**: Copilot should refuse or warn based on security rules

### Test 3: Scoped Instructions (if using nested AGENTS.md)

1. Create nested AGENTS.md in subdirectory
2. Open file in that directory
3. Ask Copilot about rules
4. **Expected**: Both root and nested rules should apply

---

## Open Questions

### Critical
- [ ] **Q1**: What is the maximum recommended size for AGENTS.md?
  - **Research**: GitHub Docs, community best practices
  - **Verification**: Test with large file (1000+ lines)
  - **Source**: [GitHub Blog - AGENTS.md](https://github.blog/changelog/2025-08-28-copilot-coding-agent-now-supports-agents-md-custom-instructions/)

- [ ] **Q2**: How do nested AGENTS.md files merge? Is it additive or override?
  - **Research**: GitHub Docs, test in practice
  - **Impact**: Affects scoping strategy
  - **Verification**: Create test with conflicting rules

### Important
- [ ] **Q3**: Can AGENTS.md reference external files (e.g., links to skills)?
  - **Research**: Test with links
  - **Impact**: Affects rule modularity
  - **Verification**: Test with `[link](../skills/tdd-workflow/SKILL.md)`

- [ ] **Q4**: Does AGENTS.md work in GitHub Copilot on github.com or only IDEs?
  - **Research**: Test in web interface
  - **Impact**: Affects adoption strategy
  - **Verification**: Open PR with AGENTS.md, test suggestions

---

## Alternative Approach: .github/copilot-instructions.md

### If AGENTS.md doesn't work or is unsupported

GitHub also supports `.github/copilot-instructions.md` (classic pattern):

```bash
mkdir -p .github
cp AGENTS.md .github/copilot-instructions.md
```

**Note**: Some sources suggest both are supported, with AGENTS.md being newer. Test both if issues arise.

**Verification**: [GitHub Blog - Custom Instructions](https://github.blog/changelog/2024-10-23-github-copilot-now-supports-custom-instructions/)

---

## Rollback Plan

If AGENTS.md causes issues:
1. Rename to `AGENTS.md.backup`
2. Use `.github/copilot-instructions.md` instead
3. Or split into smaller `.github/instructions/*.instructions.md` files

---

## Success Criteria

- [ ] All 7 rule files reviewed and migrated
- [ ] AGENTS.md created at repository root
- [ ] Content merged and organized logically
- [ ] Markdown formatting validated
- [ ] No Claude-specific references remain
- [ ] Links to skills/agents updated
- [ ] Tested with Copilot in IDE
- [ ] Documented any open questions
- [ ] Committed to repository

---

## Example: Merged AGENTS.md Output

See appendix for full example or reference the final `AGENTS.md` file created during migration.

---

## Resources

### Documentation
- [GitHub Blog - AGENTS.md Support](https://github.blog/changelog/2025-08-28-copilot-coding-agent-now-supports-agents-md-custom-instructions/)
- [GitHub Docs - Custom Instructions](https://docs.github.com/en/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot)
- [VS Code - Copilot Instructions](https://code.visualstudio.com/docs/copilot/copilot-settings#_using-instructionsmd-files)

### Tools
- Markdown linter: `markdownlint`
- Link checker: `markdown-link-check`
- File size: `wc -l AGENTS.md`

---

## Next Step

Proceed to [Step 3: Agents Migration](./03-agents-migration.md)

---

*Last Updated*: 2026-01-21  
*Status*: 📝 Ready for Execution
