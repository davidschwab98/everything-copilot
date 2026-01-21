# Master Migration Plan: Claude Code → GitHub Copilot Agent

## Executive Summary

This master plan provides a comprehensive, step-by-step approach to migrate the `everything-claude-code` configuration suite to work with **GitHub Copilot Agent**. The migration preserves the original functionality while adapting to Copilot's architecture and capabilities.

**Status**: Ready for implementation  
**Estimated Duration**: 2-3 weeks for full migration  
**Risk Level**: Medium (Hooks and Commands require workarounds)

---

## Migration Overview

### What We're Migrating FROM (Claude Code)
- **9 Agents**: Specialized subagents for delegation
- **8 Rules**: Always-follow guidelines
- **10 Commands**: Slash commands for quick execution
- **Skills**: Workflow definitions and domain knowledge
- **Hooks**: Trigger-based automations
- **MCP Configs**: Model Context Protocol server configurations
- **Examples & Plugins**: Templates and bundles

### What We're Migrating TO (GitHub Copilot Agent)
- **Custom Agents** → `.github/agents/*.agent.md`
- **AGENTS.md** → Root and nested instruction files
- **Agent Skills** → `.github/skills/*/SKILL.md`
- **GitHub Actions** → CI/CD enforcement (replacing hooks)
- **MCP Server Configuration** → Updated format in agent profiles
- **Prompt Files** → Reusable instruction snippets

---

## Migration Steps (Ordered by Priority)

| Step | Component | Priority | Portability | Effort | Status |
|------|-----------|----------|-------------|--------|--------|
| 1 | [Overview & Assessment](./01-overview-and-assessment.md) | High | N/A | Low | ✅ Ready |
| 2 | [Rules Migration](./02-rules-migration.md) | **Critical** | High | Low | 📝 Plan |
| 3 | [Agents Migration](./03-agents-migration.md) | **Critical** | High | Medium | 📝 Plan |
| 4 | [Skills Migration](./04-skills-migration.md) | High | Medium-High | Medium | 📝 Plan |
| 5 | [Commands Migration](./05-commands-migration.md) | Medium | Medium | High | 📝 Plan |
| 6 | [Hooks Replacement](./06-hooks-replacement.md) | Medium | Low-Medium | High | 📝 Plan |
| 7 | [MCP Configuration](./07-mcp-configuration.md) | Medium | Medium-High | Low | 📝 Plan |
| 8 | [Testing & Validation](./08-testing-validation.md) | High | N/A | Medium | 📝 Plan |
| 9 | [Final Checklist](./09-final-checklist.md) | Critical | N/A | Low | 📝 Plan |

---

## Key Design Decisions

### ✅ Direct Ports (High Confidence)
1. **Rules → AGENTS.md**: Rules become global and scoped instructions
2. **Agents → Custom Agents**: Direct 1:1 mapping with YAML frontmatter
3. **Skills → Agent Skills**: Strong conceptual match (preview feature)
4. **Examples → Templates**: Documentation and starter files

### ⚠️ Workarounds Required (Medium Confidence)
5. **Commands → Prompt Files + Agents**: No native slash command support
6. **MCP Configs → Agent Tool Configuration**: Format differs but supported

### ❌ Major Changes (Replacement Strategy)
7. **Hooks → GitHub Actions + CI**: No equivalent event system in Copilot

---

## Migration Philosophy

### Core Principles
1. **Preserve Intent**: Maintain the original purpose of each component
2. **Minimal Breaking Changes**: Keep disruption to workflows minimal
3. **Official Documentation First**: Verify all configurations against official sources
4. **Incremental Adoption**: Enable gradual rollout
5. **Backwards Compatible**: Support both systems during transition

### Quality Gates
- ✅ All migrations must reference official documentation
- ✅ All open questions must be clearly marked
- ✅ All workarounds must include rationale
- ✅ All breaking changes must be documented
- ✅ All configurations must be testable

---

## Success Criteria

### Must Have (P0)
- [ ] All 9 agents converted to `.github/agents/*.agent.md`
- [ ] All 8 rules converted to `AGENTS.md` + nested AGENTS.md
- [ ] Primary skills (TDD, security) converted to Agent Skills
- [ ] MCP servers configured in new format
- [ ] GitHub Actions replace critical hooks
- [ ] Documentation updated with migration guide

### Should Have (P1)
- [ ] All skills converted to `.github/skills/`
- [ ] Command workflows documented as prompt files
- [ ] Examples converted to templates
- [ ] Test validation suite created
- [ ] Rollback plan documented

### Nice to Have (P2)
- [ ] Automated conversion scripts
- [ ] Side-by-side comparison guide
- [ ] Video walkthrough
- [ ] Community contribution guide

---

## Risk Assessment

### High Risk Items
1. **Hooks Replacement**: No direct equivalent in Copilot
   - **Mitigation**: Use GitHub Actions + branch protection
   - **Fallback**: Document manual checks

2. **Commands UX**: Different user experience from slash commands
   - **Mitigation**: Create prompt file library + documentation
   - **Fallback**: Use custom agents as command proxies

### Medium Risk Items
3. **Skills Preview Status**: Agent Skills are in preview
   - **Mitigation**: Test thoroughly, have fallback to AGENTS.md
   - **Status**: Monitor GitHub Changelog for updates

4. **MCP Server Compatibility**: Format differences may cause issues
   - **Mitigation**: Test each server individually
   - **Fallback**: Document incompatible servers

### Low Risk Items
5. **Rules & Agents**: High portability, well-documented
6. **Examples**: Pure documentation, no technical risk

---

## Timeline & Phases

### Phase 1: Foundation (Week 1)
- Day 1-2: Complete Steps 1-2 (Overview, Rules)
- Day 3-4: Complete Step 3 (Agents)
- Day 5: Review and test Phase 1

**Milestone**: Basic instructions and agents working

### Phase 2: Enhancement (Week 2)
- Day 1-2: Complete Step 4 (Skills)
- Day 3-4: Complete Step 5 (Commands)
- Day 5: Complete Step 7 (MCP Configuration)

**Milestone**: Full feature parity (except hooks)

### Phase 3: Enforcement (Week 3)
- Day 1-3: Complete Step 6 (Hooks Replacement)
- Day 4: Complete Step 8 (Testing)
- Day 5: Complete Step 9 (Final Checklist)

**Milestone**: Production-ready migration

---

## Open Questions & Research Needed

### Critical Questions
1. **Q**: Can Copilot custom agents reference MCP tools by name?
   - **Source**: [GitHub Docs - Custom Agents](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents)
   - **Status**: ⏳ To be verified in Step 3

2. **Q**: What is the exact syntax for nested AGENTS.md scoping?
   - **Source**: [GitHub Blog - AGENTS.md Support](https://github.blog/changelog/2025-08-28-copilot-coding-agent-now-supports-agents-md-custom-instructions/)
   - **Status**: ⏳ To be verified in Step 2

3. **Q**: Are Agent Skills available in all Copilot surfaces (GitHub, VS Code, etc.)?
   - **Source**: [VS Code Docs - Agent Skills](https://code.visualstudio.com/docs/copilot/customization/agent-skills)
   - **Status**: ⏳ To be verified in Step 4

### Important Questions
4. **Q**: Can custom agents specify model preference (opus, sonnet, haiku)?
   - **Source**: GitHub Docs (custom agents spec)
   - **Status**: ⏳ To be verified in Step 3

5. **Q**: How do we handle hook equivalents for formatting/linting?
   - **Source**: GitHub Actions, pre-commit hooks
   - **Status**: ⏳ To be researched in Step 6

---

## Resources & References

### Official Documentation
- [Creating Custom Agents - GitHub Docs](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-custom-agents)
- [AGENTS.md Support - GitHub Changelog](https://github.blog/changelog/2025-08-28-copilot-coding-agent-now-supports-agents-md-custom-instructions/)
- [Agent Skills - VS Code Docs](https://code.visualstudio.com/docs/copilot/customization/agent-skills)
- [GitHub Actions - Workflow Syntax](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)
- [MCP Specification](https://spec.modelcontextprotocol.io/)

### Community Resources
- [DeepWiki - everything-claude-code](https://deepwiki.com/affaan-m/everything-claude-code)
- [Original Repository](https://github.com/affaan-m/everything-claude-code)

### Migration Artifacts
- This repository: Migration plans and converted configurations
- Branch: `main` (after merge)
- Documentation: Each step file in `migration-plan/`

---

## Next Steps

1. **Read** → Start with [Step 1: Overview & Assessment](./01-overview-and-assessment.md)
2. **Execute** → Follow steps 2-9 in order
3. **Validate** → Test each step before proceeding
4. **Document** → Record any deviations or issues
5. **Iterate** → Refine based on testing feedback

---

## Contributing to This Plan

If you discover issues, improvements, or answers to open questions:
1. Document findings in the relevant step file
2. Update this master plan with cross-references
3. Mark questions as ✅ Resolved or ❌ Blocked
4. Share learnings with the community

---

**Status Legend**:
- ✅ Complete
- 📝 Plan Ready
- ⏳ In Progress
- ❌ Blocked
- 🔍 Research Needed

---

*Last Updated*: 2026-01-21  
*Version*: 1.0.0  
*Maintainer*: Migration Team
