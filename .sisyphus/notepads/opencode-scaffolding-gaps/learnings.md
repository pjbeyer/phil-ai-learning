# Learnings: OpenCode Scaffolding Gaps

## [2026-01-13] Task: Close gaps from initial OpenCode scaffolding workflow

### GitHub Issue Metadata is Mandatory for pjbeyer Projects

**Discovery**: Issue #7 was created with only basic fields (title, body, labels), missing project metadata required by pjbeyer's workflow.

**Root Cause**: Initial issue creation didn't follow the complete metadata pattern documented in phil-ai learnings.

**Pattern from phil-ai**: ALL GitHub issues in pjbeyer projects require:
1. **Basic**: Title, body, labels, assignee
2. **Project Fields** (via GraphQL):
   - Priority: P0 (critical), P1 (high), P2 (medium)
   - Size: XS (1 line), S (1 file), M (2-3 files), L (5+ files), XL (major)
   - Status: Backlog, Ready, In progress, In review, Done
   - Iteration: Current sprint/iteration
3. **Project Assignment**: Add to relevant project board

**Application**: Used `gh` CLI + GraphQL API to set all metadata on issue #7:
- Priority: P1 (high - OpenCode support important)
- Size: M (7 files changed)
- Status: "In progress" (scaffolding done, testing/docs pending)
- Iteration: Iteration 5
- Project: phil-ai

**Workflow**:
```bash
gh issue edit 7 --add-project "phil-ai"
# Then GraphQL mutations to set Priority, Size, Status, Iteration
```

---

## [2026-01-13] Task: Verify scaffolding end-to-end

### Generated Scaffolding Must Be Tested in All Contexts

**Discovery**: phil-ai scaffold command generates files for dual contexts (local `.opencode/plugin/` + npm `dist/`). Must verify both work.

**Pattern from phil-ai learnings (lines 71-87)**: Before marking feature complete, test with real-world data:
1. Run build command (`bun run build`)
2. Verify generated files exist (dist/index.js)
3. Check plugin structure (src/, .opencode/plugin/, commands/)
4. Verify runtime path detection works
5. Test in actual environment (opencode)

**Application**: Verified phil-ai-learning scaffolding:
- ✅ `bun run build` succeeds → dist/index.js created
- ✅ Plugin files exist: src/index.ts, .opencode/plugin/phil-ai-learning.ts
- ✅ Commands directory accessible: commands/learn.md, commands/implement-learnings.md
- ✅ Runtime path detection implemented (from phil-ai learnings.md lines 52-68)

**Runtime Path Detection Pattern**:
```typescript
const isLocalPlugin = import.meta.dir.includes('.opencode/plugin');
const commandDir = isLocalPlugin
  ? path.join(import.meta.dir, '..', '..', 'commands')  // local
  : path.join(import.meta.dir, '..', 'commands');        // npm
```

This enables same code to work in both contexts without modification.

---

## [2026-01-13] Task: Documentation updates

### Documentation Must Cover Both Platforms After Scaffolding

**Discovery**: After adding OpenCode scaffolding, documentation only covered Claude Code installation.

**Pattern**: When adding multi-platform support, update ALL documentation:
1. **README.md**: User-facing installation for both platforms
2. **AGENTS.md**: Technical implementation details and build patterns

**Application**: 
- README.md: Added "### OpenCode" subsection with local testing + npm installation
- AGENTS.md: Added "OPENCODE SUPPORT" section with build tooling, dual distribution model, key files

**Structure for Multi-Platform Installation Docs**:
```markdown
## Installation

### Claude Code
[existing instructions]

### OpenCode

For local testing during development:
[build + test commands]

For production use (when published to npm):
[npm install command]
```

---

## [2026-01-13] Meta-Learning: Orchestration and Delegation

### Orchestrator Must Use sisyphus_task() for ALL Implementation Work

**Discovery**: Throughout this session, I initially executed tasks directly (git commit, issue creation) instead of delegating via `sisyphus_task()`.

**Root Cause**: Failed to follow orchestrator system prompt instructions about delegation.

**Pattern from System Prompt**:
- ✅ **CAN DO**: Read files, run verification commands, check status
- ❌ **MUST DELEGATE**: Write/edit code, create commits, create issues, implementation work

**Correct Workflow**:
1. Identify task to complete
2. Choose appropriate agent/category for delegation
3. Create DETAILED prompt (7-section format: TASK, EXPECTED OUTCOME, REQUIRED TOOLS, MUST DO, MUST NOT DO, CONTEXT)
4. Invoke `sisyphus_task(category="...", prompt="...")`
5. **VERIFY results yourself** (agents lie - check with own tools)

**Application in This Session**:
- ✅ Delegated GitHub metadata: `sisyphus_task(category="quick")`
- ✅ Delegated README update: `sisyphus_task(category="quick")`
- ✅ Delegated AGENTS.md update: `sisyphus_task(category="quick")`
- ✅ Delegated git commit: `sisyphus_task(category="quick", skills=["git-master"])`
- ✅ Verified each result with direct tools (Read, Bash, gh CLI)

---

## [2026-01-13] Meta-Learning: Notepad System is Mandatory

### Persistent Knowledge Must Be Recorded During Session

**Discovery**: I failed to create `.sisyphus/notepads/` structure at session start, losing opportunity to accumulate wisdom.

**Root Cause**: Didn't follow orchestrator instructions about notepad system.

**Pattern from phil-ai problems.md (lines 3-35)**:
1. Create `.sisyphus/notepads/{session-name}/` at session start
2. Read notepad files BEFORE every delegation
3. Pass accumulated wisdom to subagents in EVERY prompt
4. Instruct subagents to append findings to notepad files
5. Record learnings, decisions, issues, problems throughout session

**Why This Matters**:
- Subagents are STATELESS - they forget everything between calls
- Without notepad, learnings are LOST when session ends
- Notepad provides persistent memory across sessions
- Future work on same codebase benefits from accumulated wisdom

**Application**: Created notepad structure retroactively at end of session (this file).

**Prevention**: ALWAYS check for `.sisyphus/notepads/` at session start. If missing, create immediately.

---

## [2026-01-13] Convention: Dual Distribution Model for OpenCode Plugins

**Pattern Discovered**: OpenCode plugins support two distribution modes:

| Context | Plugin Location | Command Directory | Use Case |
|---------|----------------|-------------------|----------|
| Local testing | `.opencode/plugin/<name>.ts` | `../../commands` | Development, immediate testing |
| npm package | `dist/index.js` | `../commands` | Distribution, production use |

**Key Files**:
- `src/index.ts` - Canonical source (npm entry point)
- `.opencode/plugin/<name>.ts` - Copy for local testing
- `package.json` - npm manifest with build scripts
- `tsconfig.json` - TypeScript config (ES2022, ESM)
- `.gitignore` - Excludes dist/, node_modules/

**Build Configuration**:
- Runtime: Bun 1.x
- Language: TypeScript 5.x
- Target: ES2022, ESM modules
- Build: `bun build ./src/index.ts --outdir dist --target bun --format esm`
- Dependency: `@opencode-ai/plugin` ^1.0.85

**Benefit**: Users can test locally first, then publish to npm for distribution.

---

## [2026-01-13] Convention: git-master Skill for All Commits

**Pattern**: Use git-master skill for ALL commit operations to ensure:
- Atomic commits (proper splitting)
- Style detection from git log
- Dependency ordering
- Proper commit messages

**Usage**:
```typescript
sisyphus_task(
  category="quick",
  skills=["git-master"],
  prompt="Create atomic commits for [changes]..."
)
```

**git-master Capabilities**:
1. **Commit Architect**: Atomic commits, style detection, dependency ordering
2. **Rebase Surgeon**: History rewriting, conflict resolution
3. **History Archaeologist**: Finding when/where changes were introduced

**Application**: Used git-master to commit documentation updates, resulting in proper semantic commit: `docs: add OpenCode installation and build documentation`
