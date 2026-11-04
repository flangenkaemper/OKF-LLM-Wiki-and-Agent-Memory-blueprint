# LLM Wiki & Knowledge Base — Operating Rules (WIKI.md)

This workspace implements an **Andrej Karpathy-style LLM Wiki** structured according to **Google's Open Knowledge Format (OKF v0.2)**.
It serves as a persistent, compounding shared memory across human and AI agent sessions—covering personal context, projects, infrastructure, devices, services, tools, decisions, and external domain knowledge.

All agents operating in this workspace MUST read and follow these rules before reading, creating, or modifying files.

---

## 1. Architecture & Directory Layout

The workspace strictly separates raw input materials and working outputs from the curated OKF knowledge bundle:

```text
.
├── WIKI.md                  # Central operating rules & workflows (this file)
├── AGENTS.md / CLAUDE.md    # Agent harness entry points (delegate to WIKI.md)
├── GEMINI.md                # Gemini harness entry point (delegates to WIKI.md)
├── specs/
│   └── okf/                 # Local offline copy of the Open Knowledge Format v0.2
│       ├── SPEC.md          # Official OKF v0.2 specification
│       ├── LICENSE.md       # Apache 2.0 License
│       └── UPSTREAM.md      # Source URL, version, commit hash, and retrieval timestamp
├── inbox/                   # Unprocessed quick notes, session logs, or ad-hoc drops
├── raw/                     # IMMUTABLE source materials (articles, transcripts, notes, PDFs)
│   └── assets/              # Downloaded images, diagrams, screenshots referenced by raw/ or wiki/
├── outputs/                 # Standalone query reports, chat syntheses, and lint/audit reports
└── wiki/                    # THE OKF BUNDLE (LLM-curated knowledge base)
    ├── index.md             # Root directory catalog for progressive disclosure
    ├── log.md               # Chronological append-only record of wiki operations
    └── <subdirectory>/      # Optional subdirectories, each with its own local index.md
```

### Folder Boundaries & Ownership
1. **raw/ is Read-Only (Immutable Source of Truth):**
   - Agents MUST NEVER edit, overwrite, or delete files inside raw/.
   - When processing items from inbox/, move the raw file into raw/ (with a clean YYYY-MM-DD-slug.md filename) once ingested into wiki/.
2. **wiki/ is the Curated OKF Bundle:**
   - Every Markdown file inside wiki/ (except index.md and log.md) is an OKF **Concept Document**.
   - Agents own the maintenance of wiki/: synthesizing sources, updating cross-references, flagging contradictions, and keeping index.md and log.md current.
3. **outputs/ is the Staging & Reporting Area:**
   - When the user asks for a session summary, a comparison table, a deep-dive analysis, or a health-check report that should be saved as a file *without* immediately altering the curated wiki, write it to outputs/YYYY-MM-DD-<slug>.md.
   - Files in outputs/ can later be ingested into wiki/ if the user decides they represent permanent knowledge.

---

## 2. Open Knowledge Format (OKF) Conventions

For full structural details of the Open Knowledge Format, consult specs/okf/SPEC.md.
The rules below define how this specific wiki applies OKF v0.2:

### 2.1 YAML Frontmatter Template
Every concept file in wiki/ MUST start with a YAML frontmatter block following this structure:

```yaml
---
type: <Type>
title: "<Human-readable title>"
description: "<Single-sentence summary used for progressive disclosure and index.md>"
tags: [<tag1>, <tag2>]
resource: <Optional canonical URI, repo URL, host/IP, or local path if bound to a concrete asset>
sources:
  - id: <short-source-key>
    resource: ../raw/<filename>.md
    title: "<Title of the raw source or external URL>"
generated:
  by: <harness>/<model>
  at: <YYYY-MM-DDTHH:MM:SSZ>
---
```

### 2.2 Provenance, Trust, and Human-in-the-Loop Verification (generated & verified)
- **Timestamps:** Every timestamp (generated.at, verified.at, stale_after) MUST be an ISO 8601 datetime with an explicit UTC offset (e.g., 2026-11-04T21:00:00Z).
- **generated:** Whenever an agent creates or substantially updates a concept page, set generated: { by: "<harness>/<model>", at: "<ISO-8601-UTC>" } (for example, codex/gpt-5, claude-code/claude-opus-4, gemini/gemini-2.5-pro).
- **verified:**
  - Leave verified **omitted** (do not include an empty verified: key or fake verification) when an agent creates or edits a concept. In OKF v0.2, the absence of verified signals the unverified trust tier.
  - If a human-verified page (verified: { by: "human:...", at: "..." }) is later modified by an agent with new substantive claims, remove the verified field so the user knows the updated content awaits fresh review.
  - Only set verified: { by: "human:<user-id>", at: "<ISO-8601-UTC>" } when the user explicitly approves or verifies the concept.
- **Optional Freshness (status & stale_after):**
  - For fast-changing information, optionally set stale_after: <ISO-8601-UTC> or status: active | deprecated | draft.

### 2.3 Emergent type Consistency
In accordance with OKF v0.2, type is required on every concept document, and there is no rigid pre-set taxonomy:
- Choose a concise, descriptive, singular noun (or Title Case phrase) that best represents the concept's nature.
- **Consistency Check:** Before inventing a new type string, inspect wiki/index.md to see which type values are already used in this wiki. Reuse an existing type whenever it fits naturally so synonymous types do not proliferate across sessions.

### 2.4 Relative Links Override (Crucial for Obsidian & VS Code)
- Although some examples in specs/okf/SPEC.md show bundle-root paths starting with a leading slash (/tables/customers.md), **this wiki strictly requires standard relative Markdown paths** (./concept.md, ../subfolder/concept.md, ../raw/assets/diagram.png).
- Relative paths ensure that clicking links works natively in **VS Code**, **Obsidian** (including Graph View), and **Git web UIs** without custom path resolvers.

---

## 3. Organic Directory Structure & Progressive Disclosure

Do not pre-create empty subdirectories, and do not over-nest files prematurely:

1. **Default Placement During Ingest:**
   - Place new concept files at the root of wiki/ unless an existing subdirectory in wiki/ already clearly matches the topic.
2. **Reorganization During Lint (Subfolder Creation):**
   - Subdirectories are introduced organically during **Lint / Health Check** passes (or upon explicit user request) once wiki/ accumulates enough files (~12–15+ concepts) or a clear thematic cluster emerges across multiple pages.
   - When grouping concepts into a subdirectory (wiki/<topic-slug>/), move the related files, update all relative links, and create a local wiki/<topic-slug>/index.md.
3. **Progressive Disclosure via index.md:**
   - The root wiki/index.md lists top-level concepts and links to each subdirectory's index.md with a one-line summary.
   - Every subdirectory MUST contain its own index.md listing every concept inside that folder (- [Title](./file.md) — [Type] One-line description).

---

## 4. Separating External Knowledge from Personal Context

Raw notes often mix external information (e.g., takeaways from a video or article) with the user's own thoughts, opinions, or project notes. When compiling raw notes into wiki/:
1. Keep the core explanation of an external concept objective and clearly attributed to its sources.
2. Capture the user's personal thoughts, planned experiments, or environment-specific adaptations either in a dedicated ## Personal Context & Notes section within that page, or as a separate linked concept page where appropriate.
3. If a raw source references visual diagrams or screenshots stored in raw/assets/, embed or link them using relative paths (../raw/assets/...) and describe their key takeaway in prose or a table so text-only queries still capture the insight.

---

## 5. Core Operations & Workflows

### 5.1 INGEST (Adding Knowledge to the Wiki)
When instructed to ingest a file (or batch of files) from raw/ or inbox/:
1. **Read & Analyze:** Read the raw source(s) completely. Check wiki/index.md (and relevant subfolder indexes) to see what related concepts and type values already exist.
2. **Integrate, Don't Just Append:**
   - Update existing concept pages if the source adds new facts, nuances, or contradictions.
   - Create new concept pages where distinct topics emerge.
   - Cross-link related pages using relative Markdown links.
   - Flag any contradictions with prior sources explicitly in the page body.
3. **Update Metadata & Indexes:**
   - Ensure every touched page has valid OKF frontmatter (type, title, description, tags, sources, generated, with verified omitted unless human-confirmed).
   - If processing a file from inbox/, move it into raw/ first so sources points to ../raw/<file>.md.
   - Update affected index.md files.
   - Append an entry to wiki/log.md using the format:
     ## [YYYY-MM-DDTHH:MM:SSZ] ingest | <Source Title or Filename> followed by a bullet list of created/updated concept paths.
4. **Report to User:** Present a concise summary of created and modified wiki pages so the user can optionally inspect and verify them.

### 5.2 QUERY (Retrieving & Compounding Knowledge)
When answering questions or providing context for a task:
1. **Progressive Disclosure First:** Always read wiki/index.md first. Drill down into relevant subfolder index.md files, inspect candidate frontmatter, and read the relevant concept pages.
2. **Fallback to raw/ When Nuance Matters:** Because wiki pages are compressed syntheses, if a query requires exact figures, verbatim quotes, edge-case constraints, or details from a diagram, follow the sources path in the concept's frontmatter and inspect the original file in raw/.
3. **Save Outputs When Valuable:**
   - If the user asks to save a session report, comparison, or draft without modifying the wiki immediately, write it to outputs/YYYY-MM-DD-<slug>.md.
   - If the user asks to file the synthesis permanently into the wiki, create or update the relevant concept in wiki/ and log it in wiki/log.md (## [YYYY-MM-DDTHH:MM:SSZ] query-synthesis | <Topic>).

### 5.3 LINT / HEALTH CHECK (Auditing & Reorganizing the Wiki)
When asked to lint or audit the wiki:
1. Scan wiki/ and check for:
   - **Contradictions or Stale Claims:** Conflicting statements across pages or concepts past their stale_after date.
   - **Broken Links & Orphans:** Broken relative links, concepts missing from index.md, or orphan pages with no links.
   - **Subfolder & type Consolidation:** Propose grouping loose related concepts into thematic subdirectories (with local index.md files) once a folder grows beyond ~12–15 files, and flag any inconsistent/duplicate type names that should be unified.
   - **Unprocessed Items:** Files waiting in inbox/ or files in raw/ not referenced by any concept's sources.
   - **Unverified Queue:** A prioritized list of concepts currently lacking a verified field for optional human review.
2. Save the audit report to outputs/YYYY-MM-DD-lint-report.md, append a lint entry to wiki/log.md, and ask the user which structural fixes or subfolder reorganizations to execute.

### 5.4 VERIFY (Human-in-the-Loop Review)
When the user confirms they have reviewed a concept page (or asks to review unverified pages interactively):
1. Add or update the verified block in the concept's YAML frontmatter:
```yaml
verified:
  by: human:<user-id>
  at: <YYYY-MM-DDTHH:MM:SSZ>
```
2. Append a brief verify entry to wiki/log.md.
