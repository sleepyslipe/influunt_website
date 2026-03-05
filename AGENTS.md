# AI Agent Guidelines for VK-TG Polling Bot

This file provides instructions for AI agents (Copilot, Claude, Gemini, etc.) working on this repository.

## ⚠️ CRITICAL RULES - NO EXCEPTIONS

### 1. NEVER DEFER TASKS
**DO NOT** say "this is deferred for later" or "this requires more work, skipping". 
- If a task is given, it MUST be completed in full
- If you encounter difficulties, work through them systematically
- Partial completion is NOT acceptable
- All items in a request MUST be addressed

### 2. COMPLETE ALL ITEMS
When given a list of tasks:
- Execute ALL items, not just the easy ones
- Do not skip items because they are complex
- Each item deserves full attention and implementation
- Report honestly on what was completed

### 3. SELF-REVIEW EVERYTHING
Before considering any task complete:
- [ ] Verify the implementation works correctly
- [ ] Check for edge cases
- [ ] Run tests to confirm nothing is broken
- [ ] Review for duplicated or unused code
- [ ] Ensure documentation is updated

### 4. VERIFY ALL PREVIOUS FEEDBACK
Before declaring any task complete:
- [ ] Re-read ALL previous feedback items from earlier conversations
- [ ] Verify each item was actually implemented (not just planned)
- [ ] Test each fix by running the code/opening the UI
- [ ] Do NOT assume a fix works just because code was written — verify it
- [ ] If a fix cannot be verified locally, explicitly note it in the report

### 5. ALWAYS PUSH AFTER COMMIT
Every `git commit` MUST be followed by `git push`:
- Commits without push are invisible to others — treat unpushed commits as incomplete
- Run `git push` immediately after every successful `git commit`
- If push fails (auth, network), explicitly notify the user
- Never end a session with unpushed commits

### 6. DOCUMENTATION IS PART OF EVERY TASK
Documentation is NOT a "nice to have" — it is a **mandatory deliverable** for every code change:
- **PLANNING:** When creating a todo list, ALWAYS include a documentation update step
- **EXECUTION:** Update docs BEFORE committing, not after
- **SCOPE:** Even small bug fixes need doc updates (at minimum `technical-nuances.md` or `modules.md`)

### 7. FIX ALL ERRORS YOU FIND
When running pytest, pylint, or mypy — fix ALL errors, **including pre-existing ones**:
- "Pre-existing" is NOT an excuse to skip — someone introduced them, and ignoring them lets bugs accumulate
- A mypy type error may be a **real runtime crash** (e.g. missing import, wrong argument type)
- If 130 errors feel overwhelming, fix what you can in the files you touched + any clearly dangerous ones
- **Never dismiss errors as "not my problem"** — you saw them, you own them now

**⚠️ WHY THIS IS CRITICAL — CONSEQUENCES OF SKIPPING:**
- Agents are **stateless between sessions** — docs are the ONLY persistent memory
- Without updated docs, the next session starts blind: re-analyzing the entire codebase from scratch
- Outdated docs cause **wrong assumptions** → bugs, duplicated code, broken architecture
- Undocumented architectural decisions get lost → future agents make conflicting changes
- Missing module docs → agents create duplicate modules instead of reusing existing ones
- Stale test counts → agents don't know if tests regressed

> **Rule of thumb:** If you changed code but didn't update docs, the task is NOT complete.

### 9. SSH AGENT MUST BE RUNNING BEFORE GIT PUSH
Before any `git push`, ensure `ssh-agent` has the SSH key loaded:
- At the START of every session, run: `eval "$(ssh-agent -s)" && ssh-add ~/.ssh/id_rsa`
- If `git push` prompts for a passphrase — **stop** and load the key first
- **Never** let the user wait through 5 passphrase prompts — that is a blocked terminal
- If the key requires a passphrase interactively, warn the user IMMEDIATELY
- Add `ssh-agent` setup as the very first terminal command in every session

### 10. UPDATE COMMITS.md AFTER EVERY COMMIT
`COMMITS.md` is a detailed per-commit changelog — the **primary audit trail** for every change:
- After every `git commit`, **immediately** add an entry to `COMMITS.md` at the top (newest first)
- Each entry MUST include: commit hash, commit message, date, list of changes, affected files, test results
- Use the format already established in `COMMITS.md` (see existing entries for reference)
- This file is the user's way to review agent work — treat it as a mandatory deliverable
- Unlike `docs/CHANGELOG.md` (grouped by version), `COMMITS.md` is per-commit with full detail
- **Never skip this step** — the user relies on it to verify agent output

> **📖 Full documentation:** See `docs/README.md` for complete navigation.
> **🏗️ Architecture details:** See `docs/architecture/overview.md` and `docs/architecture/technical-nuances.md`.

## Testing Requirements

### Coverage Rules
- **New code: 100% coverage required**
- **Modified code: Add tests if missing**
- Critical paths MUST have tests
- Test both success and failure scenarios

### Test File Naming
- Test files: `tests/test_<module>.py`
- Test functions: `test_<functionality>_<scenario>()`

### Best Practices
```python
# Use table-driven tests
@pytest.mark.parametrize("input,expected", [
    ("case1", "result1"),
    ("case2", "result2"),
])
def test_function(input, expected):
    assert function(input) == expected
```

## Code Quality Standards

### Modularity
- **Maximum 500 lines per file** - split larger files
- Each module has single responsibility
- Avoid circular imports
- Use `__init__.py` to expose public API

### Self-Review Checklist
- [ ] All tests pass
- [ ] No linting errors
- [ ] No type errors
- [ ] Documentation updated
- [ ] No duplicated code
- [ ] No unused imports/variables
- [ ] All edge cases handled
- [ ] New tests added for new code

### Error Handling
- Use specific exceptions, not bare `except:`
- Log errors with context
- Provide user-friendly messages
- Never silently swallow exceptions

## Key Subsystems

## Documentation Requirements

### When Adding Features
1. Update `docs/user-guide/commands.md` for new commands
2. Update `docs/features/` for new features
3. Update schemas.py comments for new config options
4. Keep all docs in sync with code

### Style Guide
- Use lowercase filenames (e.g., `stats-collection.md`)
- Consistent markdown formatting
- Include examples for complex features

## Task Planning Requirements

When creating a plan (todo list) for any task, it MUST include:
1. **Read relevant docs** — understand current state before changing anything
2. **Implementation steps** — the actual code changes
3. **Tests** — write/update tests for changed code
4. **Quality checks** — pytest, pylint, mypy
5. **Documentation updates** — update ALL relevant docs (see rule 6)
6. **Commit and push** — with descriptive message

Skipping step 5 means the task is incomplete. Period.

## Remember

As an AI agent, you are expected to:
1. **Complete ALL tasks** - no deferrals, no shortcuts
2. **Test thoroughly** - run tests, verify manually
3. **Document changes** - update relevant docs (MANDATORY, not optional)
4. **Review carefully** - check for issues before committing
5. **Be professional** - quality over speed

If something seems difficult, that is NOT a reason to skip it. Work through the complexity systematically.

If you skip documentation, you are sabotaging your own future sessions. The next time you work on this project, you WILL pay the price in wasted time and wrong assumptions.
