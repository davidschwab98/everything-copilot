# Step 6: Hooks Replacement

**Priority**: Medium  
**Estimated Time**: 8-12 hours  
**Prerequisites**: Steps 1-5 complete  
**Status**: 📝 Ready to Execute

---

## Objective

Replace Claude Code hooks with GitHub Actions, pre-commit hooks, and branch protection rules. This is the **most challenging** migration as Copilot has no equivalent event-driven hook system.

---

## The Challenge

### What We Had (Claude Code)
Hooks triggered on tool events:
- **PreToolUse**: Before Bash, Edit, Write
- **PostToolUse**: After Bash, Edit
- **Stop**: Before session ends
- **SessionStart**: On new session
- **PreCompact**: Before context compaction

### What Copilot Offers
**Nothing equivalent** - No tool-event hooks

### Replacement Strategy
1. **GitHub Actions** - CI/CD enforcement
2. **Pre-commit hooks** - Local enforcement
3. **Branch protection** - PR requirements
4. **IDE integrations** - Linters, formatters

---

## Source Analysis

### Current Hooks Inventory (from hooks.json)

#### PreToolUse Hooks
1. **Block dev servers outside tmux**
   - Matcher: Dev server commands
   - Action: Block execution, suggest tmux
   - **Impact**: Developer experience

2. **Warn about long-running commands**
   - Matcher: npm install, cargo build, etc.
   - Action: Suggest tmux
   - **Impact**: Developer experience

3. **Pause before git push**
   - Matcher: `git push`
   - Action: Open editor, confirm push
   - **Impact**: Git workflow safety

4. **Block unnecessary .md files**
   - Matcher: Creating .md files (except README, CLAUDE, AGENTS, CONTRIBUTING)
   - Action: Block creation
   - **Impact**: Documentation organization

5. **Suggest context compaction**
   - Matcher: Edit or Write
   - Action: Run compaction check script
   - **Impact**: Context management (Claude-specific)

#### PostToolUse Hooks
1. **Auto-format with Prettier**
   - Matcher: Edited JS/TS files
   - Action: Run `prettier --write`
   - **Impact**: Code quality

2. **TypeScript check**
   - Matcher: Edited TS files
   - Action: Run `tsc --noEmit`
   - **Impact**: Code quality

3. **Warn about console.log**
   - Matcher: Edited JS/TS files
   - Action: Grep for console.log, warn
   - **Impact**: Code quality

4. **PR creation tracking**
   - Matcher: `gh pr create`
   - Action: Log PR URL, suggest review
   - **Impact**: Git workflow

#### Stop Hooks
1. **Final console.log audit**
   - Matcher: All
   - Action: Check modified files for console.log
   - **Impact**: Code quality

2. **Session persistence**
   - Matcher: All
   - Action: Save session state
   - **Impact**: Claude-specific

3. **Session evaluation**
   - Matcher: All
   - Action: Extract patterns for learning
   - **Impact**: Claude-specific

#### SessionStart / PreCompact Hooks
1. **Memory persistence**
   - Action: Load/save context
   - **Impact**: Claude-specific

---

## Migration Strategy by Hook Type

### Category 1: Code Quality (High Priority) ✅ MIGRATE

**Hooks**:
- Auto-format with Prettier
- TypeScript check
- Warn about console.log
- Final console.log audit

**Replacement**: GitHub Actions + Pre-commit

#### Solution A: GitHub Actions (CI)

**File**: `.github/workflows/code-quality.yml`

```yaml
name: Code Quality

on:
  pull_request:
    branches: [main, develop]
  push:
    branches: [main, develop]

jobs:
  lint-and-format:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Check Prettier formatting
        run: npx prettier --check "**/*.{js,ts,jsx,tsx,json,css,md}"
      
      - name: Run ESLint
        run: npx eslint "**/*.{js,ts,jsx,tsx}"
      
      - name: TypeScript check
        run: npx tsc --noEmit
      
      - name: Check for console.log
        run: |
          if git diff origin/main...HEAD -- '*.js' '*.ts' '*.jsx' '*.tsx' | grep -i 'console\.log'; then
            echo "❌ Error: console.log statements found in changes"
            exit 1
          else
            echo "✅ No console.log statements found"
          fi
```

**Branch Protection**:
- Require "Code Quality" check to pass
- Prevent merging if checks fail

#### Solution B: Pre-commit Hooks (Local)

**File**: `.husky/pre-commit`

```bash
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

# Run Prettier
npm run format

# Run ESLint
npm run lint

# Run TypeScript check
npm run type-check

# Check for console.log
if git diff --cached --name-only | grep -E '\.(ts|tsx|js|jsx)$' | xargs grep -n 'console\.log' 2>/dev/null; then
  echo "❌ Error: Remove console.log statements before committing"
  exit 1
fi

echo "✅ Pre-commit checks passed"
```

**Setup**:
```bash
npm install --save-dev husky lint-staged
npx husky install
npx husky add .husky/pre-commit "npm test"
```

**File**: `.lintstagedrc.js`

```javascript
module.exports = {
  '*.{js,ts,jsx,tsx}': [
    'prettier --write',
    'eslint --fix',
    'git add'
  ],
  '*.{json,css,md}': [
    'prettier --write',
    'git add'
  ]
}
```

---

### Category 2: Git Workflow (Medium Priority) ⚠️ PARTIAL MIGRATE

**Hooks**:
- Pause before git push
- PR creation tracking

**Replacement**: Git aliases, shell functions, documentation

#### Solution: Git Aliases

**File**: Add to `.gitconfig` or document in README

```bash
# Add to ~/.gitconfig or project .git/config
[alias]
    pushsafe = "!f() { \
        echo 'Review changes before pushing:'; \
        git diff HEAD origin/$(git branch --show-current); \
        read -p 'Continue with push? (y/n) ' -n 1 -r; \
        echo; \
        if [[ $REPLY =~ ^[Yy]$ ]]; then \
            git push \"$@\"; \
        fi; \
    }; f"
```

**Usage**: `git pushsafe` instead of `git push`

**Documentation**: Add to CONTRIBUTING.md

---

### Category 3: Developer Experience (Low Priority) ❌ DO NOT MIGRATE

**Hooks**:
- Block dev servers outside tmux
- Warn about long-running commands
- Suggest context compaction

**Rationale**: Copilot-specific or overly prescriptive

**Alternative**: Add to CONTRIBUTING.md as best practices

---

### Category 4: Claude-Specific (DO NOT MIGRATE) ❌

**Hooks**:
- Session persistence
- Session evaluation
- Memory persistence
- Context compaction

**Rationale**: No equivalent in Copilot

---

### Category 5: File Organization (Low Priority) ⚠️ OPTIONAL

**Hook**: Block unnecessary .md files

**Replacement**: GitHub Actions PR comment

**File**: `.github/workflows/pr-checks.yml`

```yaml
name: PR Checks

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  check-files:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Check for unnecessary markdown files
        run: |
          # Get added files in PR
          ADDED_MD=$(git diff --name-only --diff-filter=A origin/${{ github.base_ref }}...HEAD | grep '\.md$' || true)
          
          UNNECESSARY=""
          for file in $ADDED_MD; do
            if [[ ! "$file" =~ (README|AGENTS|CONTRIBUTING|LICENSE)\.md$ ]] && [[ ! "$file" =~ ^docs/ ]]; then
              UNNECESSARY="$UNNECESSARY\n- $file"
            fi
          done
          
          if [ -n "$UNNECESSARY" ]; then
            echo "⚠️ Warning: Unnecessary markdown files added:"
            echo -e "$UNNECESSARY"
            echo "Consider consolidating into README.md or docs/"
            # Don't fail, just warn
          fi
```

---

## Implementation Plan

### Phase 1: Essential Code Quality (Week 1)

**Priority**: CRITICAL

1. **Create GitHub Actions Workflow**
   - [ ] Create `.github/workflows/code-quality.yml`
   - [ ] Add Prettier check
   - [ ] Add ESLint check
   - [ ] Add TypeScript check
   - [ ] Add console.log check
   - [ ] Test workflow

2. **Setup Pre-commit Hooks**
   - [ ] Install husky + lint-staged
   - [ ] Create `.husky/pre-commit`
   - [ ] Configure `.lintstagedrc.js`
   - [ ] Test locally

3. **Configure Branch Protection**
   - [ ] Enable branch protection on `main`
   - [ ] Require "Code Quality" check
   - [ ] Require PR reviews
   - [ ] Prevent force push

**Validation**: Create test PR, verify checks run

---

### Phase 2: Extended Checks (Week 2)

**Priority**: HIGH

1. **Test Coverage Check**
   - [ ] Create `.github/workflows/test-coverage.yml`
   - [ ] Add coverage reporting
   - [ ] Set 80% threshold
   - [ ] Integrate with Codecov/Coveralls

2. **Security Scanning**
   - [ ] Enable Dependabot
   - [ ] Enable CodeQL
   - [ ] Add secret scanning
   - [ ] Configure security policies

**Validation**: Test with intentionally failing code

---

### Phase 3: Optional Enhancements (Week 3)

**Priority**: MEDIUM

1. **PR Checks**
   - [ ] Create `.github/workflows/pr-checks.yml`
   - [ ] Add file organization checks
   - [ ] Add commit message validation
   - [ ] Add changelog updates

2. **Git Workflow Helpers**
   - [ ] Document git aliases
   - [ ] Create helper scripts
   - [ ] Update CONTRIBUTING.md

**Validation**: Test full workflow end-to-end

---

## Detailed Workflow Files

### 1. Code Quality Workflow (Complete)

**File**: `.github/workflows/code-quality.yml`

```yaml
name: Code Quality

on:
  pull_request:
    branches: [main, develop]
  push:
    branches: [main, develop]

jobs:
  quality:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Full history for git diff
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Check formatting with Prettier
        run: |
          echo "Checking code formatting..."
          npm run format:check || (
            echo "❌ Code is not formatted correctly"
            echo "Run: npm run format"
            exit 1
          )
      
      - name: Lint with ESLint
        run: |
          echo "Running ESLint..."
          npm run lint || (
            echo "❌ Linting errors found"
            echo "Run: npm run lint:fix"
            exit 1
          )
      
      - name: Type check with TypeScript
        run: |
          echo "Running TypeScript compiler..."
          npm run type-check || (
            echo "❌ Type errors found"
            echo "Fix TypeScript errors before committing"
            exit 1
          )
      
      - name: Check for console.log statements
        run: |
          echo "Checking for console.log statements..."
          
          # Get changed files
          if [ "${{ github.event_name }}" == "pull_request" ]; then
            CHANGED_FILES=$(git diff --name-only ${{ github.event.pull_request.base.sha }}...${{ github.sha }})
          else
            CHANGED_FILES=$(git diff --name-only HEAD^..HEAD)
          fi
          
          # Check for console.log in changed JS/TS files
          CONSOLE_LOGS=$(echo "$CHANGED_FILES" | grep -E '\.(ts|tsx|js|jsx)$' | xargs grep -n 'console\.log' 2>/dev/null || true)
          
          if [ -n "$CONSOLE_LOGS" ]; then
            echo "❌ console.log statements found:"
            echo "$CONSOLE_LOGS"
            echo ""
            echo "Remove console.log statements before merging"
            exit 1
          else
            echo "✅ No console.log statements found"
          fi
      
      - name: Summary
        if: success()
        run: |
          echo "✅ All code quality checks passed!"
          echo "- Formatting: ✓"
          echo "- Linting: ✓"
          echo "- Type checking: ✓"
          echo "- No console.log: ✓"
```

**Required package.json scripts**:
```json
{
  "scripts": {
    "format": "prettier --write '**/*.{js,ts,jsx,tsx,json,css,md}'",
    "format:check": "prettier --check '**/*.{js,ts,jsx,tsx,json,css,md}'",
    "lint": "eslint '**/*.{js,ts,jsx,tsx}'",
    "lint:fix": "eslint '**/*.{js,ts,jsx,tsx}' --fix",
    "type-check": "tsc --noEmit"
  }
}
```

---

### 2. Test Coverage Workflow

**File**: `.github/workflows/test-coverage.yml`

```yaml
name: Test Coverage

on:
  pull_request:
    branches: [main, develop]
  push:
    branches: [main, develop]

jobs:
  coverage:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests with coverage
        run: npm run test:coverage
      
      - name: Check coverage threshold
        run: |
          COVERAGE=$(cat coverage/coverage-summary.json | jq '.total.lines.pct')
          echo "Current coverage: $COVERAGE%"
          
          if (( $(echo "$COVERAGE < 80" | bc -l) )); then
            echo "❌ Coverage is below 80% threshold"
            exit 1
          else
            echo "✅ Coverage meets 80% threshold"
          fi
      
      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/coverage-final.json
          fail_ci_if_error: true
```

---

### 3. Security Scanning Workflow

**File**: `.github/workflows/security.yml`

```yaml
name: Security

on:
  pull_request:
    branches: [main, develop]
  push:
    branches: [main, develop]
  schedule:
    - cron: '0 0 * * 0'  # Weekly

jobs:
  security:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Run npm audit
        run: npm audit --audit-level=high
      
      - name: Run Snyk security scan
        uses: snyk/actions/node@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        with:
          args: --severity-threshold=high
```

**Enable Dependabot**:

**File**: `.github/dependabot.yml`

```yaml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    open-pull-requests-limit: 10
    labels:
      - "dependencies"
      - "automated"
```

**Enable CodeQL**:

**File**: `.github/workflows/codeql.yml`

```yaml
name: CodeQL

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]
  schedule:
    - cron: '0 0 * * 1'  # Weekly

jobs:
  analyze:
    runs-on: ubuntu-latest
    
    permissions:
      actions: read
      contents: read
      security-events: write
    
    strategy:
      matrix:
        language: ['javascript', 'typescript']
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Initialize CodeQL
        uses: github/codeql-action/init@v2
        with:
          languages: ${{ matrix.language }}
      
      - name: Autobuild
        uses: github/codeql-action/autobuild@v2
      
      - name: Perform CodeQL Analysis
        uses: github/codeql-action/analyze@v2
```

---

## Pre-commit Hook Setup

### Installation

```bash
# Install dependencies
npm install --save-dev husky lint-staged @typescript-eslint/parser @typescript-eslint/eslint-plugin prettier eslint

# Initialize husky
npx husky install
npm pkg set scripts.prepare="husky install"

# Add pre-commit hook
npx husky add .husky/pre-commit "npx lint-staged"
```

### Configuration

**File**: `.lintstagedrc.js`

```javascript
module.exports = {
  // JavaScript/TypeScript files
  '*.{js,jsx,ts,tsx}': [
    'prettier --write',
    'eslint --fix',
    // Check for console.log
    (filenames) => {
      const files = filenames.join(' ')
      const result = require('child_process').execSync(
        `grep -n 'console\\.log' ${files} || true`,
        { encoding: 'utf-8' }
      )
      if (result.trim()) {
        throw new Error(`console.log found:\n${result}\nRemove before committing.`)
      }
      return []
    },
  ],
  
  // JSON/CSS/Markdown
  '*.{json,css,md}': 'prettier --write',
  
  // TypeScript type check
  '*.{ts,tsx}': () => 'tsc --noEmit',
}
```

---

## Branch Protection Configuration

### GitHub Repository Settings

Navigate to: **Settings → Branches → Branch protection rules → Add rule**

**Branch name pattern**: `main`

**Enable**:
- [x] Require a pull request before merging
  - [x] Require approvals (1)
  - [x] Dismiss stale reviews
- [x] Require status checks to pass before merging
  - Required checks:
    - ✓ Code Quality
    - ✓ Test Coverage
    - ✓ Security
- [x] Require conversation resolution before merging
- [x] Require linear history
- [x] Do not allow bypassing the above settings

**Optional**:
- [x] Require signed commits
- [x] Require deployments to succeed

---

## Open Questions

### Critical
- [ ] **Q1**: What CI/CD provider is the project using?
  - **Options**: GitHub Actions, CircleCI, Travis, Jenkins
  - **Impact**: HIGH - affects workflow syntax
  - **Verification**: Check existing `.github/workflows/` or CI config

- [ ] **Q2**: Are there existing GitHub Actions workflows?
  - **Impact**: MEDIUM - affects integration strategy
  - **Verification**: Check `.github/workflows/` directory

### Important
- [ ] **Q3**: What package manager: npm, yarn, pnpm, or bun?
  - **Impact**: MEDIUM - affects commands and caching
  - **Verification**: Check `package-lock.json`, `yarn.lock`, etc.

- [ ] **Q4**: Is Dependabot/CodeQL already enabled?
  - **Impact**: LOW - affects configuration
  - **Verification**: Check repository settings

---

## Validation Checklist

### Code Quality Checks
- [ ] Prettier formatting enforced
- [ ] ESLint rules passing
- [ ] TypeScript compiles without errors
- [ ] No console.log in changed files
- [ ] Pre-commit hook runs locally
- [ ] GitHub Action runs on PR
- [ ] Branch protection prevents merge on failure

### Test Coverage
- [ ] Coverage calculated correctly
- [ ] 80% threshold enforced
- [ ] Coverage report uploaded
- [ ] Coverage visible in PR

### Security
- [ ] Dependabot enabled and running
- [ ] CodeQL enabled and running
- [ ] Secret scanning enabled
- [ ] Security alerts configured

---

## Success Criteria

- [ ] All code quality checks migrated to GitHub Actions
- [ ] Pre-commit hooks configured and tested
- [ ] Branch protection rules enabled
- [ ] Test coverage enforcement active
- [ ] Security scanning enabled
- [ ] Documentation updated (CONTRIBUTING.md)
- [ ] Team trained on new workflow
- [ ] Validation passed on test PRs

---

## Resources

### Documentation
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Husky Documentation](https://typicode.github.io/husky/)
- [lint-staged Documentation](https://github.com/okonet/lint-staged)
- [Branch Protection Rules](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches)
- [Dependabot](https://docs.github.com/en/code-security/dependabot)
- [CodeQL](https://docs.github.com/en/code-security/code-scanning/automatically-scanning-your-code-for-vulnerabilities-and-errors/about-code-scanning-with-codeql)

### Tools
- GitHub Actions
- Husky
- lint-staged
- Prettier
- ESLint
- Codecov/Coveralls

---

## Next Step

Proceed to [Step 7: MCP Configuration](./07-mcp-configuration.md)

---

*Last Updated*: 2026-01-21  
*Status*: 📝 Ready for Execution
