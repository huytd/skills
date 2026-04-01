---
name: create-wiki
description: Deep-investigate a codebase and generate a single-page project wiki at .docs/index.html. Use this skill whenever the user asks to "create a wiki", "generate docs", "document this project", "explain this codebase", "make a project wiki", or wants a comprehensive HTML overview of a repository. Also trigger when the user says /create-wiki. Even if the user just says "wiki" in the context of documentation, use this skill.
---

# Create Wiki

Generate a comprehensive single-page wiki for any codebase, saved to `.docs/index.html`. The wiki explains the project's architecture, implementation, and key systems in **explanatory narrative prose** — not terse reference tables or bullet-point dumps.

## Process

1. **Investigate** — Launch parallel agents to deeply explore the codebase
2. **Plan** — Decide which sections are relevant based on findings
3. **Generate** — Build the HTML wiki using the bundled template
4. **Refine** — Review for completeness and readability

---

## Step 1: Investigate the Codebase

Speed matters here — launch **4–6 parallel exploration agents** to cover the codebase from different angles. Before launching, detect the project stack by reading the package manager manifest (package.json, pyproject.toml, go.mod, Cargo.toml, etc.) so you can tailor agent prompts.

### Agent Prompts (adapt to the project)

**Agent 1 — Overview & Architecture:**
Read the package manifest, README, CLAUDE.md, and main entry point. Identify the framework, key dependencies, project purpose, and high-level architecture. Look for architecture docs or diagrams.

**Agent 2 — Core Business Logic:**
Explore the main source directories (services/, models/, lib/, core/, src/). Understand the primary domain logic, key abstractions, data flow patterns, and how the pieces connect.

**Agent 3 — API / Routes / CLI:**
Explore API routes, controllers, handlers, middleware, or CLI command definitions. Map the endpoint structure, auth patterns, and request flows.

**Agent 4 — Data & Infrastructure:**
Investigate database schema, migrations, ORM config, CI/CD pipelines (Jenkinsfile, GitHub Actions, Dockerfile), configuration loading, environment variables, background jobs, queues, and cron tasks.

**Agent 5 — Testing, UI & Integrations:**
Explore test structure and frameworks, frontend pages and components (if any), third-party service integrations, monitoring, logging, and metrics.

Skip agents that don't apply. A CLI tool doesn't need an API routes agent. A backend service doesn't need a UI agent. A library doesn't need a CI/CD agent. Use your judgment.

---

## Step 2: Plan Sections

Based on investigation results, decide which sections to include in the wiki.

**Always include:**
- Overview (what the project is, what it does, tech badges)
- Architecture (high-level system design)
- Project Structure (annotated file tree)
- Development Commands (how to run, build, test)

**Include if the project has them:**
- Tech Stack (when there are enough interesting dependencies to warrant a section)
- Key Flows / Lifecycle (main processes or data flows)
- API Routes (REST/GraphQL/gRPC endpoints)
- Background Workers (queues, cron, async processing)
- Database (schema, migrations, access patterns)
- Integrations (third-party services and how they're used)
- Services (core business logic layer, when complex enough)
- Configuration (env vars, config loading strategy)
- Monitoring & Metrics (observability stack)
- CI/CD & Deployment (build pipeline, Docker, deploy process)
- UI & Pages (frontend pages and components)
- Testing (test strategy, frameworks, how to run)

Order sections so earlier ones provide context for later ones. Architecture before implementation details. Core flows before supporting infrastructure.

---

## Step 3: Generate the Wiki

### Theming: DESIGN.md or Neutral

Before generating, check if a `DESIGN.md` file exists in the project root. This changes how you style the wiki:

**If DESIGN.md exists:** Read it and map its design tokens (colors, fonts, spacing, shadows, radii) onto the CSS variables in the template. Replace the neutral defaults with the project's brand colors, typography, and elevation system. For example, if DESIGN.md specifies a primary accent of `#7610C6` and a font of "Neue Haas Grotesk Text", update `--accent`, `--accent-hover`, `--accent-surface`, and the `font-family` in the template accordingly. Apply both light and dark mode tokens if the design system defines them.

**If no DESIGN.md exists:** Use the template as-is — it ships with a neutral gray/slate palette and system fonts that look clean and professional without any brand identity.

### Using the Template

Read the template from `references/template.html` in this skill's directory. It provides the complete HTML/CSS/JS shell — sidebar, theme toggle, responsive layout, and all component styles.

### Using the Template

1. Read the template file
2. Replace `{{PROJECT_NAME}}` with the project name
3. Build the sidebar `<nav>` with `<a href="#section-id">` links for each section
4. Fill the `<main>` element with your content sections
5. Write the complete file to `.docs/index.html` (create `.docs/` if needed)

### Content Writing Guidelines

The most important thing: **write like you're explaining the project to a smart new team member**, not generating API reference docs. Every section should help the reader build a mental model of *why* things work the way they do.

**For each section:**
1. Open with a paragraph explaining the purpose and context — why does this part of the system exist?
2. Add detailed subsections with narrative explanations
3. Use tables and code blocks as *supporting material*, not as the primary content
4. Use callout boxes for important warnings, gotchas, or non-obvious behavior
5. Connect to other sections ("this is consumed by the SQS handler described above")

**What to avoid:**
- Sections that are just a table with no surrounding explanation
- Bullet-point-only sections with no narrative context
- Repeating information that's already in another section
- Generic descriptions that could apply to any project ("this service handles business logic")

**What to aim for:**
- Concrete references to actual file paths, function names, and class names
- Explanations of non-obvious design decisions and tradeoffs
- Descriptions of how data flows between components
- "Why" before "what" — motivation before mechanics

### HTML Components Available

The template CSS supports these components:

**Flow diagrams** — for multi-step processes:
```html
<div class="flow">
  <div class="flow-steps">
    <span class="flow-step blue">Step 1</span>
    <span class="flow-arrow">&rarr;</span>
    <span class="flow-step green">Step 2</span>
    <span class="flow-arrow">&rarr;</span>
    <span class="flow-step purple">Step 3</span>
  </div>
</div>
```
Colors: `blue`, `green`, `orange`, `purple`, `cyan`.

**Callout boxes** — for warnings and notes:
```html
<div class="callout info"><strong>Note:</strong> message here</div>
<div class="callout warn"><strong>Warning:</strong> message here</div>
```

**File tree** — for project structure (whitespace-preserving):
```html
<div class="file-tree">
<span class="dir">project/</span>
├── <span class="dir">src/</span>
│   ├── <span class="file">index.ts</span>   <span class="comment"># Entry point</span>
│   └── <span class="dir">lib/</span>        <span class="comment"># Utilities</span>
└── <span class="file">package.json</span>
</div>
```

**Tables** — for structured reference data:
```html
<table>
  <thead><tr><th>Column</th><th>Description</th></tr></thead>
  <tbody><tr><td>value</td><td>explanation</td></tr></tbody>
</table>
```

**Tags** — for HTTP methods or status labels:
```html
<span class="tag tag-green">GET</span>
<span class="tag tag-blue">POST</span>
<span class="tag tag-red">DELETE</span>
<span class="tag tag-orange">PATCH</span>
<span class="tag tag-purple">SOCKET</span>
```

**Tech badges** — for the hero section:
```html
<span class="tech-badge">Next.js</span>
<span class="tech-badge">PostgreSQL</span>
```

**Code blocks** — for config, schema, or code examples:
```html
<pre><code>const config = loadConfig();</code></pre>
```

---

## Step 4: Refine

After generating, check for:
- Sidebar links match section `id` attributes
- File tree renders correctly (the template has `white-space: pre` on `.file-tree`)
- No sections are still in terse/listy style — rewrite any that are
- Cross-references between sections are accurate
- The hero section is not styled as a card (it should be flat with a bottom border)
- Footer has the project name and generation date

---

## Large Codebases

For large projects where generating everything at once would be unwieldy, break the generation into chunks:
1. Generate the HTML shell with sidebar, hero, and the first few sections
2. Add remaining sections in batches of 2–3, using Edit to append before the closing `</main>` tag
3. Each chunk should be self-contained enough to write in one edit

This avoids context pressure from trying to hold the entire wiki in a single generation pass.
