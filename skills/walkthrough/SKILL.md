---
name: walkthrough
description: Walk through git changes step by step in a multi-turn conversational way. Use this skill whenever the user says /walkthrough, or asks to "walk me through the changes", "explain this diff", "walk through this PR", "explain what changed in this branch", or wants to understand code changes one file at a time. Also trigger when a user shows a git diff and wants a guided explanation.
---

# Walkthrough Skill

Walk through code changes in a guided, conversational tour — one file (or logical group) at a time. Think of yourself as a senior engineer doing a live code review with a teammate.

## Step 1: Gather the diff

Determine what to walk through based on how the skill was invoked:

- **No arguments or "current branch"**: Run `git diff $(git merge-base HEAD origin/master 2>/dev/null || git merge-base HEAD origin/main 2>/dev/null)...HEAD` to get all changes on the current branch vs master/main. Also check `git diff HEAD` for any unstaged/staged changes.
- **Specific file(s) mentioned**: Diff just those files: `git diff HEAD -- <file>` or `git diff <branch> -- <file>`
- **PR number given**: Use `gh pr diff <number>` to get the full PR diff
- **Raw diff already in context**: Use it directly — don't re-fetch

If there are both staged and unstaged changes, include both. If the diff is empty, say so and stop.

## Step 2: Build the tour plan

Before explaining anything, parse the diff and build a mental map:

1. List all changed files
2. Group logically related files together (e.g. a service + its test, a type + its usage)
3. Determine a good traversal order — usually: core logic first, then tests, then types/interfaces, then config/misc
4. Write a **brief table of contents** to show the user upfront

Present the tour plan like this:

```
Here's what changed — I'll walk through each one:

1. `apps/python_api/services/memory/memory_tools.py` — core logic change
2. `apps/python_api/type_defs/base_agent.py` — how memory injection works
3. `apps/python_api/tests/test_memory_tools.py` — updated tests

Let's start with #1.
```

Then immediately begin the first file — don't wait for the user to say "go".

## Step 3: Explain each change

For each file (or logical group), follow this structure:

### Header
State the file and what kind of change it is (e.g. "new file", "refactor", "bug fix", "added feature").

### Before
Describe what the old code did — its approach, its assumptions, what it was trying to achieve. If there was no previous version (new file), skip this. Use a code snippet from the diff's `-` lines when it helps.

### After
Describe what the new code does — the new approach, what's different, what's better (or different tradeoffs). Use a code snippet from the diff's `+` lines when it helps.

### Why it matters
Explain the motivation. What problem does this solve? What was wrong or limiting about the old approach? What does the new approach enable? This is the most important part — don't skip it.

### Key patterns or tradeoffs (when relevant)
Call out anything worth noting:
- Non-obvious design decisions
- Edge cases the change handles (or doesn't)
- Performance or correctness implications
- Things to watch out for

## Step 4: Hand off and continue

After each file, close with a natural handoff:

> Ready to move on to `<next file>`? Or any questions about this one?

**If the user asks a question**: Answer it directly and in context — reference the specific lines, the before/after, the rationale. Then offer to continue.

**If the user says "next" / "continue" / "yeah"**: Move to the next item on the tour.

**If you've covered all files**: Wrap up with a brief summary of the overall theme of the changes (1-3 sentences), and mention anything cross-cutting (e.g. "the common thread here is moving from static string interpolation to dynamic callables").

## Visualizations

Use diagrams to make the conceptual shift visible — a good diagram is often worth more than a paragraph. Ask yourself: "would a picture make this clearer?" If yes, draw it.

Always use **ASCII/text diagrams** for all visualizations — never Mermaid. ASCII diagrams render universally and stay readable in any terminal or viewer.

Use ASCII diagrams for:
- **Flow changes** — control flow, async chains, request pipelines
- **Architecture changes** — how components relate or communicate
- **State machines** — lifecycle transitions
- **Sequence diagrams** — call sequences between functions/agents/services
- **Data structure transformations** — showing the shape of an object before and after
- **Side-by-side comparisons** — two short code paths next to each other

### Examples

**Flow change** (like the `inject_memory` refactor):
```
Before:
  agent.instruction = string
        |
        v
  ADK interpolates {state} placeholders

After:
  agent.instruction = async fn
        |
        v
  fn reads ctx.state live
        |
        v
  returns rendered string
```

**Data shape change**:
```
Before: state["memories"] = "- I prefer aisle seats\n- SFO"   (raw string)
After:  state["memories"] = {"seat_pref": "aisle", ...}        (native dict)
```

**Sequence change**:
```
Before:  caller --> tool() --> returns string
After:   caller --> tool() --> sanitize() --> store dict --> returns string
```

**Simple rename or extraction** → no diagram needed, just code snippets

### Rules
- Every file explanation should have **at least one visual** (diagram, annotated snippet, or side-by-side comparison) unless the change is truly trivial (e.g. a typo fix or single-line config change)
- Never use Mermaid — always use ASCII/text diagrams
- Keep diagrams focused — show only what changed, not the entire system
- Use actual function/class names from the diff in diagram labels

## Style guidelines

- Write like a senior engineer explaining to an engaged peer — not a doc generator
- Be concrete: reference actual function names, variable names, and line-level details from the diff
- Lead with the **why**, not just the what
- Use `## Before` / `## After` headers for visual clarity
- Keep code snippets short and focused — show just the relevant lines, not entire functions
- If a change is simple and obvious, say so briefly rather than over-explaining
- If a change is subtle or tricky, slow down and explain the nuance
- Don't summarize the diff mechanically ("line 42 was changed from X to Y") — explain the conceptual shift
