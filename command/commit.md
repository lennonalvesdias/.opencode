---
description: Generate a conventional commit from staged or modified files
model: github-copilot/claude-haiku-4.5
---

Analyze the current changes and create a commit with a well-crafted conventional commit message.

**Scope:** $ARGUMENTS

If no arguments provided, commit all staged changes (`git diff --cached`). If nothing is staged, stage and commit all modified tracked files.
If argument is a file path or glob, stage and commit only matching files.
If argument is a commit message hint, use it to guide the message content.

---

## EXECUTION STEPS

### Step 1: Assess working tree state

```bash
git status --short
```

Determine what to commit:
- If there are staged changes → use those
- If nothing staged → stage modified tracked files matching $ARGUMENTS (or all if no args)
- If working tree is clean → report "nothing to commit" and stop

### Step 2: Collect diff for analysis

```bash
git diff --cached --stat
git diff --cached
```

### Step 3: Craft the commit message

Analyze the diff and craft a conventional commit message following these rules:
- **Format**: `type(scope): description` with optional body and footer
- **Types**: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`
- **Scope**: inferred from changed files (module, directory, or component name)
- **Subject line**: imperative mood, lowercase, no period, max 72 chars
- **Body** (if needed): explain WHY the change was made, not what changed
- **Footer** (if needed): breaking changes (`BREAKING CHANGE:`) or issue refs
- **Language**: English

If $ARGUMENTS contains a hint, incorporate it into the message.

### Step 4: Present and confirm

Show the proposed commit message to the user. Ask for confirmation before committing.

### Step 5: Execute the commit

```bash
git commit -m "<subject>" -m "<body>"
```

If the commit includes multiple logical changes that should be split, suggest splitting before committing.

### Step 6: Report

Confirm:
- Branch name
- Commit hash (short)
- Files committed (count and names)
- The final commit message
 