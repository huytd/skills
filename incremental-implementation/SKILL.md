---
name: incremental-implementation
description: Implements a plan, spec, or design doc by breaking it into small digestible chunks — working with the user to clarify, implement, and verify each one before moving on. Visualizes every change so the user stays fully informed and understands what was built and why. Use this skill whenever a user wants to implement a plan and you want to make sure they understand everything being built. Trigger on phrases like "implement the plan", "let's build this step by step", "execute this plan", "start implementing", "work through the design doc", or when picking up a plan from docs/plans/. Also use proactively when a task involves building multiple files or modules from a written spec. This skill exists specifically to prevent vibe-coding — where AI does a large batch of work the user ends up not understanding.
---

# Incremental Implementation

**Asking questions:** At every checkpoint, use the `AskUserQuestion` tool (available in Claude Code, Codex, and compatible clients) to present structured choices rather than just asking in plain text. This keeps the user in a tight feedback loop without waiting for a new turn. If `AskUserQuestion` is not available in your environment, fall back to asking in plain text.

The goal is to keep the user informed and in control at every step. Large implementations go wrong not because the code is bad, but because the user doesn't understand what was built or why. This skill counters that by breaking work into verifiable units, explaining intent before coding, and showing a clear "what just changed and why" after each chunk.

## Step 1: Read and Internalize the Plan

Read the full plan before doing anything else. If it isn't shared, ask the user to provide it (a file path, a paste, or a description is fine).

As you read, identify:
- The major components, modules, or layers to be built
- Dependencies between them (what must exist before what)
- Natural "seams" — places where something will visibly work after the chunk is done

## Step 2: Propose a Chunk Breakdown

Before writing a single line of code, show the user how you plan to slice the work.

Present it clearly, for example:

```
Proposed implementation chunks:

[1] Project scaffold — folder structure, tsconfig, package.json, dev tooling
[2] Storage layer — JSON file read/write, conversation index
[3] LLM provider interface — abstract interface + first adapter (e.g. Anthropic)
[4] Agentic loop skeleton — basic chat, no tools yet
[5] Built-in tools — web_search, web_fetch, load_skill
[6] MCP client integration — connect to MCP servers, merge tool list
[7] REST API + SSE streaming — Express routes, streaming responses

Chunk 4 depends on 2 and 3. Chunks 5–6 depend on 4. Chunk 7 depends on all.
```

Use the `AskUserQuestion` tool to ask: "Does this breakdown look right? Anything you'd re-slice or re-order?" with options like "Looks good, let's start", "I'd like to re-slice", "Re-order some chunks", or "Other".

Wait for the user to confirm before starting.

## Step 3: Work Through Each Chunk

For each chunk, follow this sequence — every time, without skipping steps.

### 3a. Brief the user

In 2–4 sentences: what you're about to build, which files you'll create or modify, and what this chunk connects to. No code yet — just intent. This gives the user a chance to redirect before any work is done.

### 3b. Clarify

Use the `AskUserQuestion` tool to ask if the user has any questions or wants to adjust the approach for this chunk. Offer options like "Looks good, proceed", "I have a question", or "Adjust the approach". One short exchange is enough. If they want to dig into something, take the time — the whole point is that they stay oriented. Don't rush past this.

### 3c. Implement

Write the code. Stay focused on what the chunk describes. Resist the urge to clean up adjacent code, add extra features, or sneak in improvements that weren't part of the plan — those belong in their own chunks.

### 3d. Visualize

After implementation, show the user what was built. Choose the format that communicates the change most clearly:

**C4-style ASCII diagram** — best for structural changes (new services, layers, modules, data flow):
```
[User FE] --> [POST /messages] --> [Agent Loop] ---------> [LLM Provider]
                                        |
                                  [Tool Executor]
                                  /      |      \
                         [web_search] [web_fetch] [load_skill]
```
Keep diagrams tight (under 20 lines). Mark new elements with **(NEW)** and modified elements with **(CHANGED)**.

**Concise flow walkthrough** — best for logic-heavy changes:
> "When a message arrives: (1) load history from JSON → (2) build system prompt with skill frontmatter → (3) call LLM with tool list → (4) if tool_use returned, execute and loop → (5) stream final response via SSE"

**File summary** — best for scaffolding or setup chunks:
- `src/agent/loop.ts` — drives multi-turn tool calls, streams tokens
- `src/providers/base.ts` — the interface all LLM adapters must implement
- `config.json` — API keys, default model, MCP server definitions

Pick one format per chunk — whichever makes the change most obvious. Don't combine all three.

### 3e. Verify

Use the `AskUserQuestion` tool to ask: "Does this look right before we move on to [next chunk name]?" with options like "Yes, move on", "I have a question", "Fix something first", or "Other".

Give the user a real chance to inspect, question, or push back. Only move forward when they confirm. If they spot an issue, fix it now — not in a later chunk.

---

## Chunking Heuristics

A well-sized chunk:
- Covers a single layer or abstraction (e.g., "the storage layer", "the provider interface")
- Produces something observable after it's done (a test passes, a route responds, a file is readable)
- Touches 1–4 files

A chunk is probably too big if:
- Its description needs "and also" more than twice
- It creates more than 5 new files at once
- You'd struggle to explain it clearly in 3 sentences

A chunk is too small if:
- It's a single type definition or one-liner
- It produces nothing observable on its own

When in doubt, err toward smaller chunks — the user can always say "combine these two."

---

## Handling Surprises Mid-Chunk

If you discover something unexpected mid-implementation — a missing dependency, a design conflict, something the plan didn't account for — pause and surface it rather than quietly working around it. Explain what you found and why it matters, then use the `AskUserQuestion` tool to ask how the user wants to handle it (e.g. "Update the plan", "Work around it", "Skip for now", "Other").

Same for plan errors: if something in the plan doesn't make sense or conflicts with something already built, flag it before implementing. It's far easier to change direction at the start of a chunk than to untangle it at the end.

---

## Wrapping Up

After all chunks are verified, give a brief final summary:
- What was built across the whole implementation
- How the major pieces connect to each other
- What to do next (how to run it, what's missing, what's a natural next phase)

This leaves the user with a clear mental model of the complete system, not just the last chunk they approved.

---

## Staying Grounded

The sequential verification loop is the point of this skill. Don't skip ahead to chunk 5 before chunk 3 is verified, even if it seems like it would save time. The user's understanding is worth more than speed.
