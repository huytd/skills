# skills
Some custom skills I created for Claude Code

## What's inside?

### Create Wiki Skill

Create a DeepWiki-style documentation of your codebase.

<img width="1706" height="963" alt="image" src="https://github.com/user-attachments/assets/e2b77920-6cb7-4b3b-b192-a1d334e3ae30" />

### Incremental Implementation Skill

Analyze a plan, break it down into smaller manageble chunks, figure out the dependency between them, implement step by step instead of bulding the whole thing all at once.

The point is to avoid the vibe-coding trap, where the user ask the AI to do all the work and ended up not understanding anything at all.

<img width="2672" height="1522" alt="image" src="https://github.com/user-attachments/assets/629da0c1-fe78-4918-b29a-f9a86d7e8dc6" />

<img width="1189" height="1052" alt="image" src="https://github.com/user-attachments/assets/22258ef1-6623-4efa-bed1-2f009e1cf3c8" />

<img width="1423" height="572" alt="image" src="https://github.com/user-attachments/assets/999ff9a9-0f62-4ae7-9319-9875a2d6c437" />

<img width="1410" height="1301" alt="image" src="https://github.com/user-attachments/assets/4cad7183-c2cc-411d-9a99-5428fcecf22c" />

### Review Diff

This skill create a markdown document contains the diagram explains the system before and after the change, as well as the detailed walkthrough the code change, based on the current `git diff`, to make it easier to understand what's going on. It can also being used to review any PR.

### Walkthrough

Another skill to improve code review experience, this one run directly in the Claude Code session. It will go through each change in the codebase, one by one. Explain what was the code's behavior before and after the change.
