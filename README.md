# skills

Some custom skills I created for Claude Code.

## Installation

### Step 1 — Add the marketplace

Inside Claude Code, run:

```
/plugin marketplace add huytd/skills
```

This registers the catalog. No plugins are installed yet.

### Step 2 — Install individual skills

Install only what you need:

```
/plugin install create-wiki@huytd-skills
/plugin install incremental-implementation@huytd-skills
/plugin install review-diff@huytd-skills
/plugin install walkthrough@huytd-skills
```

Or browse everything via `/plugin` → **Marketplaces** tab.

---

## What's inside?

### 📖 Create Wiki

Create a DeepWiki-style documentation of your codebase.

<img width="1706" height="963" alt="image" src="https://github.com/user-attachments/assets/e2b77920-6cb7-4b3b-b192-a1d334e3ae30" />

### 🪜 Incremental Implementation

Analyze a plan, break it down into smaller manageable chunks, figure out the dependency between them, implement step by step instead of building the whole thing all at once.

The point is to avoid the vibe-coding trap, where the user asks the AI to do all the work and ends up not understanding anything at all.

<img width="2672" height="1522" alt="image" src="https://github.com/user-attachments/assets/629da0c1-fe78-4918-b29a-f9a86d7e8dc6" />

<img width="1189" height="1052" alt="image" src="https://github.com/user-attachments/assets/22258ef1-6623-4efa-bed1-2f009e1cf3c8" />

<img width="1423" height="572" alt="image" src="https://github.com/user-attachments/assets/999ff9a9-0f62-4ae7-9319-9875a2d6c437" />

<img width="1410" height="1301" alt="image" src="https://github.com/user-attachments/assets/4cad7183-c2cc-411d-9a99-5428fcecf22c" />

### 🔍 Review Diff

This skill creates a markdown document containing a diagram explaining the system before and after the change, as well as a detailed walkthrough of the code change, based on the current `git diff`, to make it easier to understand what's going on. It can also be used to review any PR.

### 🚶 Walkthrough

Another skill to improve code review experience, this one runs directly in the Claude Code session. It will go through each change in the codebase, one by one, explaining what the code's behavior was before and after the change.
