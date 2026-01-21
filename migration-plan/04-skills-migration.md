# Step 4: Skills Migration

**Priority**: High  
**Estimated Time**: 6-8 hours  
**Prerequisites**: Steps 1-3 complete  
**Status**: 📝 Ready to Execute

---

## Objective

Migrate Claude Code skills from `/skills/**` to GitHub Copilot Agent Skills in `.github/skills/*/SKILL.md`. Skills are workflow definitions and domain knowledge that agents and developers reference on-demand.

---

## Important Context: Preview Feature

⚠️ **Agent Skills are in PREVIEW** according to [VS Code Docs](https://code.visualstudio.com/docs/copilot/customization/agent-skills).

**Implications**:
- Feature may change
- Not all Copilot surfaces may support it
- Fallback strategy required
- Monitor GitHub Changelog for updates

**Mitigation Strategy**:
1. Migrate high-value skills first
2. Keep critical content in AGENTS.md as fallback
3. Test thoroughly before relying on skills
4. Document both skill and AGENTS.md approaches

---

## Source Files Analysis

### Current Skills Inventory

| Skill | Type | Purpose | Structure | Priority |
|-------|------|---------|-----------|----------|
| `coding-standards.md` | Simple | Language best practices | Markdown | High |
| `backend-patterns.md` | Simple | API, DB, caching | Markdown | High |
| `frontend-patterns.md` | Simple | React, Next.js | Markdown | High |
| `tdd-workflow/SKILL.md` | Structured | TDD methodology | SKILL format | **Critical** |
| `security-review/SKILL.md` | Structured | Security checklist | SKILL format | **Critical** |
| `project-guidelines-example.md` | Simple | Example project skill | Markdown | Medium |
| `clickhouse-io.md` | Simple | ClickHouse analytics | Markdown | Low |
| `continuous-learning/` | Complex | Session learning | Scripts + config | Low |
| `strategic-compact/` | Complex | Context management | Scripts | Low |

**Total**: 9 skills (5 simple, 2 structured, 2 complex)

---

## GitHub Copilot Agent Skills Format

### Official Specification

According to [VS Code Docs - Agent Skills](https://code.visualstudio.com/docs/copilot/customization/agent-skills):

```markdown
---
name: skill-name
description: Brief description of what this skill provides
---

# Skill Title

Detailed content and instructions...

## When to Use

When to activate this skill...

## Process/Guidelines

Step-by-step process or guidelines...

## Examples

Concrete examples...
```

**Directory Structure**:
```
.github/skills/
├── skill-name/
│   ├── SKILL.md           # Main skill definition
│   ├── resources/         # Optional: supporting files
│   │   ├── checklist.md
│   │   └── templates/
│   └── scripts/           # Optional: helper scripts
```

**Key Features**:
- Skills are **on-demand** (loaded when relevant)
- Can include resources and scripts
- Organized in subdirectories
- YAML frontmatter with name and description

---

## Migration Strategy

### Phased Approach

**Phase 1: Critical Skills** (Week 1)
- tdd-workflow
- security-review

**Phase 2: High-Value Skills** (Week 1-2)
- coding-standards
- backend-patterns
- frontend-patterns

**Phase 3: Nice-to-Have Skills** (Week 2-3)
- project-guidelines
- clickhouse-io

**Phase 4: Complex Skills** (Week 3+)
- Evaluate continuous-learning
- Evaluate strategic-compact
- May require alternative implementation

---

## Skill-by-Skill Migration

### 1. TDD Workflow Skill (CRITICAL)

**Source**: `/skills/tdd-workflow/SKILL.md`

**Already structured correctly!** This is a model for migration.

**Target**: `.github/skills/tdd-workflow/SKILL.md`

**Changes Needed**:
- Verify YAML frontmatter format
- Ensure examples use Copilot-compatible syntax
- Check for Claude-specific tool references

**Additional Resources**:
- Create `resources/test-patterns.md` with common test patterns
- Create `resources/coverage-guide.md` with coverage strategies

**Priority**: ⭐⭐⭐ CRITICAL (most referenced skill)

---

### 2. Security Review Skill (CRITICAL)

**Source**: `/skills/security-review/SKILL.md`

**Already structured correctly!**

**Target**: `.github/skills/security-review/SKILL.md`

**Changes Needed**:
- Add GitHub-specific security tools (Dependabot, CodeQL)
- Update secret scanning advice
- Link to GitHub security features

**Additional Resources**:
- Create `resources/owasp-checklist.md`
- Create `resources/security-tools.md` (GitHub-specific)
- Create `templates/security-review.md` (template for reviews)

**Priority**: ⭐⭐⭐ CRITICAL (security is mandatory)

---

### 3. Coding Standards Skill

**Source**: `/skills/coding-standards.md`

**Current Format**: Simple markdown

**Target**: `.github/skills/coding-standards/SKILL.md`

**Conversion**:
```yaml
---
name: coding-standards
description: Language-agnostic coding standards and best practices. Use for code style, naming conventions, file organization, and general code quality guidelines.
---

# Coding Standards

[Content from coding-standards.md]

## When to Use
- Writing new code
- Reviewing code
- Refactoring existing code
- Setting up new projects

## Languages Covered
- TypeScript/JavaScript
- Python
- Go
- General principles

[Rest of content]
```

**Additional Resources**:
- Create `resources/typescript-standards.md`
- Create `resources/python-standards.md`
- Create `resources/file-organization.md`

**Priority**: ⭐⭐ HIGH

---

### 4. Backend Patterns Skill

**Source**: `/skills/backend-patterns.md`

**Current Format**: Simple markdown

**Target**: `.github/skills/backend-patterns/SKILL.md`

**Conversion**:
```yaml
---
name: backend-patterns
description: Backend architecture patterns for APIs, databases, caching, authentication, and error handling. Use for designing backend systems.
---

# Backend Patterns

## When to Use
- Designing APIs
- Implementing authentication
- Setting up databases
- Configuring caching
- Error handling strategies

[Content organized by pattern type]
```

**Additional Resources**:
- Create `resources/api-design.md`
- Create `resources/database-patterns.md`
- Create `resources/caching-strategies.md`
- Create `templates/api-endpoint-template.ts`

**Priority**: ⭐⭐ HIGH

---

### 5. Frontend Patterns Skill

**Source**: `/skills/frontend-patterns.md`

**Current Format**: Simple markdown

**Target**: `.github/skills/frontend-patterns/SKILL.md`

**Conversion**:
```yaml
---
name: frontend-patterns
description: Frontend patterns for React, Next.js, state management, and UI components. Use for building modern web applications.
---

# Frontend Patterns

## When to Use
- Building React components
- Setting up Next.js apps
- Managing state
- Implementing UI patterns
- Performance optimization

[Content organized by framework]
```

**Additional Resources**:
- Create `resources/react-patterns.md`
- Create `resources/nextjs-patterns.md`
- Create `resources/state-management.md`
- Create `templates/component-template.tsx`

**Priority**: ⭐⭐ HIGH

---

### 6. Project Guidelines Example

**Source**: `/skills/project-guidelines-example.md`

**Current Format**: Simple markdown (example)

**Target**: `.github/skills/project-guidelines/SKILL.md`

**Purpose**: Template for creating project-specific skills

**Conversion**:
```yaml
---
name: project-guidelines
description: Project-specific conventions and patterns. Customize this for each project's unique requirements.
---

# Project Guidelines Template

This is a template for creating project-specific skills.

## How to Use This Template
1. Copy this skill directory
2. Rename to your project name
3. Fill in project-specific conventions
4. Update examples with actual code from your project

## Sections to Customize
- Tech stack
- Directory structure
- Naming conventions
- API patterns
- Database schema
- Deployment process

[Example sections]
```

**Priority**: ⭐ MEDIUM

---

### 7. ClickHouse IO Skill

**Source**: `/skills/clickhouse-io.md`

**Current Format**: Simple markdown (niche technology)

**Target**: `.github/skills/clickhouse-io/SKILL.md`

**Decision**: Migrate only if project uses ClickHouse

**Priority**: ⭐ LOW (niche)

---

### 8. Continuous Learning Skill

**Source**: `/skills/continuous-learning/` (complex, with scripts)

**Current Format**: Directory with `config.json` and scripts

**Analysis**: This is a meta-skill for extracting patterns from sessions.

**Decision**: **Do not migrate directly**

**Rationale**:
- Relies on Claude Code hooks and session persistence
- No equivalent in Copilot Agent Skills
- Implementation-dependent

**Alternative**:
- Document the learning process in AGENTS.md
- Use GitHub Discussions/Issues for pattern tracking
- Manual pattern extraction and documentation

**Priority**: ❌ NOT MIGRATED (no equivalent)

---

### 9. Strategic Compact Skill

**Source**: `/skills/strategic-compact/` (complex, context management)

**Current Format**: Scripts for context compaction

**Analysis**: Claude-specific context window management

**Decision**: **Do not migrate directly**

**Rationale**:
- Specific to Claude's context compaction
- Copilot has different context management
- Hook-dependent

**Alternative**:
- Add context management guidance to AGENTS.md
- Keep instructions concise by default
- Use agent delegation for focused tasks

**Priority**: ❌ NOT MIGRATED (Claude-specific)

---

## Implementation Checklist

### Pre-Migration
- [ ] Create `.github/skills/` directory
- [ ] Review VS Code Agent Skills documentation
- [ ] Verify Agent Skills support in your IDE
- [ ] Enable Agent Skills in VS Code settings (if gated)

### Migration (For Each Skill)
- [ ] Create skill subdirectory: `.github/skills/{skill-name}/`
- [ ] Create `SKILL.md` with YAML frontmatter
- [ ] Convert content to skill format
- [ ] Add "When to Use" section
- [ ] Create `resources/` directory if needed
- [ ] Add supporting files (checklists, templates)
- [ ] Test skill activation
- [ ] Document any issues

### Post-Migration
- [ ] Test all skills
- [ ] Document skill invocation syntax
- [ ] Update AGENTS.md with skill references
- [ ] Create skill usage guide
- [ ] Add fallback content to AGENTS.md (for critical skills)

---

## Testing Skills

### Test 1: Skill Activation

1. Open VS Code with Copilot
2. Work on code related to skill topic
3. Check if skill is suggested/loaded
4. **Expected**: Skill appears in context

### Test 2: Skill Content

1. Explicitly reference skill (if possible)
2. Ask Copilot questions related to skill
3. **Expected**: Answers reference skill content

### Test 3: Fallback

1. Disable Agent Skills (or use unsupported surface)
2. Check if AGENTS.md provides coverage
3. **Expected**: Critical content still available

---

## Open Questions

### Critical
- [ ] **Q1**: How do you explicitly invoke a skill?
  - **Research**: VS Code Copilot documentation
  - **Verification**: Test different invocation methods
  - **Impact**: HIGH - affects usability
  - **Source**: [VS Code Docs - Agent Skills](https://code.visualstudio.com/docs/copilot/customization/agent-skills)

- [ ] **Q2**: Are skills automatically loaded based on context?
  - **Research**: Check skill loading behavior
  - **Verification**: Monitor when skills appear
  - **Impact**: HIGH - affects design approach

- [ ] **Q3**: Is Agent Skills feature available in all Copilot surfaces?
  - **Research**: Test in VS Code, GitHub.com, CLI
  - **Verification**: Test skill availability
  - **Impact**: HIGH - affects rollout strategy

### Important
- [ ] **Q4**: What is the maximum skill size?
  - **Research**: Test with large skills
  - **Impact**: MEDIUM - affects content strategy

- [ ] **Q5**: Can skills include code snippets and templates?
  - **Research**: Test with templates
  - **Impact**: MEDIUM - affects resource files

- [ ] **Q6**: Can skills reference other skills or agents?
  - **Research**: Test cross-references
  - **Impact**: MEDIUM - affects modularization

---

## Fallback Strategy

### If Agent Skills Don't Work

**Option A: Inline into AGENTS.md**
- Copy critical skill content into AGENTS.md sections
- Keep as instructions rather than separate skills
- Lose on-demand loading, gain universal support

**Option B: Hybrid Approach** ✅ RECOMMENDED
- Keep critical content in AGENTS.md
- Use Agent Skills for detailed, on-demand content
- Skills augment AGENTS.md, not replace it

**Option C: Prompt Files**
- Store skills as `.md` files in `.github/prompts/`
- Manually reference when needed
- Good for complex, occasionally-used skills

---

## Example: TDD Workflow Skill (Complete)

```markdown
---
name: tdd-workflow
description: Test-driven development workflow with comprehensive testing patterns. Use when writing new features, fixing bugs, or refactoring code. Enforces 80%+ coverage.
---

# Test-Driven Development Workflow

This skill ensures all code development follows TDD principles with comprehensive test coverage.

## When to Activate

- Writing new features or functionality
- Fixing bugs or issues
- Refactoring existing code
- Adding API endpoints
- Creating new components

## Core Principles

### 1. Tests BEFORE Code
ALWAYS write tests first, then implement code to make tests pass.

### 2. Coverage Requirements
- Minimum 80% coverage (unit + integration + E2E)
- 100% for critical code (auth, payments, business logic)
- All edge cases covered
- Error scenarios tested

### 3. Test Types

#### Unit Tests
- Individual functions and utilities
- Component logic
- Pure functions

#### Integration Tests
- API endpoints
- Database operations
- External service calls

#### E2E Tests
- Critical user flows
- Complete workflows
- Browser automation

## TDD Workflow Steps

### Step 1: Write User Journeys
```
As a [role], I want to [action], so that [benefit]
```

### Step 2: Generate Test Cases
Write comprehensive test cases for each journey

### Step 3: Run Tests (They Should Fail)
Verify tests fail before implementation

### Step 4: Implement Code
Write minimal code to make tests pass

### Step 5: Run Tests Again
Verify tests now pass

### Step 6: Refactor
Improve code quality while keeping tests green

### Step 7: Verify Coverage
```bash
npm run test:coverage
```

## Testing Patterns

[Include test patterns from original skill]

## Best Practices

1. **Write Tests First** - Always TDD
2. **One Assert Per Test** - Focus on single behavior
3. **Descriptive Test Names** - Explain what's tested
4. **Arrange-Act-Assert** - Clear test structure
5. **Mock External Dependencies** - Isolate unit tests

## Resources

See `resources/` directory for:
- Test patterns by framework
- Coverage strategies
- Mocking examples
- E2E test templates

---

**Remember**: Tests are not optional. They are the safety net that enables confident refactoring and rapid development.
```

**Supporting Files**:
- `.github/skills/tdd-workflow/resources/test-patterns.md`
- `.github/skills/tdd-workflow/resources/coverage-guide.md`
- `.github/skills/tdd-workflow/templates/unit-test-template.ts`

---

## Success Criteria

- [ ] Critical skills migrated (TDD, security)
- [ ] High-value skills migrated (coding standards, patterns)
- [ ] All skills tested and working
- [ ] Supporting resources created
- [ ] Fallback content in AGENTS.md
- [ ] Skills documented in README
- [ ] Open questions resolved or documented

---

## Resources

### Documentation
- [VS Code Docs - Agent Skills](https://code.visualstudio.com/docs/copilot/customization/agent-skills)
- [GitHub Copilot Customization](https://docs.github.com/en/copilot/customizing-copilot)
- [Agent Skills Best Practices](https://code.visualstudio.com/docs/copilot/copilot-settings)

### Tools
- VS Code with Copilot extension
- YAML validator
- Markdown linter

---

## Next Step

Proceed to [Step 5: Commands Migration](./05-commands-migration.md)

---

*Last Updated*: 2026-01-21  
*Status*: 📝 Ready for Execution
