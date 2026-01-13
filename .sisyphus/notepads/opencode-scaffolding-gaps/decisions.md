# Decisions: OpenCode Scaffolding Gaps

## [2026-01-13] Decision: Single Commit for Documentation Updates

**Context**: Two documentation files changed (README.md, AGENTS.md), both related to OpenCode support.

**Options Considered**:
1. Two commits - one for README, one for AGENTS.md
2. Single commit - both files together

**Decision**: Single commit

**Rationale**:
- Both files document the same feature (OpenCode support)
- README covers user-facing installation
- AGENTS.md covers technical implementation
- These are complementary documentation updates
- Splitting would create artificial separation
- git-master skill confirmed this follows atomic commit principles

**Trade-offs**:
- Single commit is larger (89 lines added)
- But: changes are cohesive and belong together
- Reverting would revert all OpenCode docs (which is correct behavior)

**Result**: Commit e501c8e "docs: add OpenCode installation and build documentation"

---

## [2026-01-13] Decision: Use "quick" Category for All Delegations

**Context**: Needed to delegate multiple tasks (GitHub metadata, documentation updates, git commits).

**Options Considered**:
1. `category="ultrabrain"` - Maximum reasoning power
2. `category="quick"` - Fast, budget-friendly
3. `category="general"` - General purpose

**Decision**: `category="quick"` for all tasks

**Rationale**:
- Tasks were straightforward (GitHub CLI, documentation edits, git commits)
- No complex reasoning required
- "quick" is optimized for small tasks with minimal overhead
- Budget-friendly for simple operations
- All tasks completed successfully in <1 minute each

**Trade-offs**:
- Less reasoning power than ultrabrain
- But: tasks didn't require deep reasoning
- Speed and cost efficiency outweighed need for complex analysis

---

## [2026-01-13] Decision: Retroactive Notepad Creation

**Context**: Session completed without creating notepad structure. User asked if persistent knowledge was recorded.

**Options Considered**:
1. Acknowledge failure, don't create notepad (knowledge lost)
2. Create notepad retroactively with session learnings
3. Create notepad but mark as incomplete

**Decision**: Create notepad retroactively with complete session learnings

**Rationale**:
- Better to capture knowledge late than never
- Session had significant learnings worth preserving:
  - GitHub metadata workflow
  - OpenCode scaffolding verification
  - Orchestration patterns
  - Dual distribution model
- Future sessions will benefit from this knowledge
- Demonstrates correct notepad structure for future reference

**Trade-offs**:
- Not ideal (should have been created at session start)
- But: captures all learnings that would otherwise be lost
- Serves as example for future sessions

---

## [2026-01-13] Decision: Verify All Subagent Claims

**Context**: System prompt warns "SUBAGENTS LIE" and requires verification of all claims.

**Options Considered**:
1. Trust subagent reports (faster)
2. Verify critical claims only
3. Verify ALL claims with own tools

**Decision**: Verify ALL claims with own tools

**Rationale**:
- System prompt is explicit: "NEVER trust agent's self-report"
- Verification is cheap (simple tool calls)
- Prevents cascading failures from undetected errors
- Builds confidence in results

**Verification Performed**:
- GitHub metadata: `gh issue view 7 --json projectItems`
- Build success: `ls -la dist/`
- README update: `Read` tool to check actual content
- AGENTS.md update: `Read` tool to check actual content
- Git commit: `git log -2 --oneline` and `git status`

**Result**: All verifications passed, confirming subagent work was correct.
