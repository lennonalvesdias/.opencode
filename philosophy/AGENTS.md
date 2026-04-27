## Behavioral Guidelines - ALWAYS APPLY

These principles apply to every task, before any tool is used or code is written:

### Think Before Coding
- State assumptions explicitly. If uncertain, **ask** — don't guess silently.
- If multiple interpretations exist, present them. Don't pick one without saying so.
- If something is unclear, **stop and name what's confusing** before proceeding.

### Simplicity First
- Minimum code that solves the problem. Nothing speculative.
- No abstractions for single-use code. No features beyond what was asked.
- If 200 lines could be 50, rewrite it.

### Surgical Changes
- Touch only what you must. Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Remove imports/variables/functions that YOUR changes made unused — but not pre-existing dead code.
- **The test:** every changed line must trace directly to the user's request.

### Goal-Driven Execution
- Define success criteria before implementing. Loop until verified.
- For multi-step tasks, state a brief plan with verification checkpoints.
- Weak criteria ("make it work") block you; strong criteria let you loop independently.

---

## Code Philosophy - MANDATORY

Before writing or modifying any code, you MUST:

1. **Select the relevant philosophy** based on your task:
   - Working on UI/frontend? → Load **`frontend-philosophy`** (The 5 Pillars of Intentional UI)
   - Working on backend/logic? → Load **`code-philosophy`** (The 5 Laws of Elegant Defense)
   - Working on both? → Load both

2. **Load the skill** using the `skill` tool BEFORE implementation

3. **Verify your implementation** against the philosophy checklist BEFORE completing

4. **Refactor if needed** - if code violates any principle, fix it before proceeding

This is NOT optional. These philosophies define how code must be written in this project.
