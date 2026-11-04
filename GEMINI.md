# Agent Instructions (GEMINI.md)

This workspace contains an **LLM Wiki & Knowledge Base** following the Karpathy LLM Wiki workflow and Google's Open Knowledge Format (OKF v0.2).

## Mandatory Operating Rules
1. **Read WIKI.md First:** Before searching, creating, or modifying any files in this repository, read [WIKI.md](./WIKI.md) and follow its rules strictly.
2. **Format Reference:** The official Open Knowledge Format v0.2 specification is stored locally at [specs/okf/SPEC.md](./specs/okf/SPEC.md). Consult it when checking frontmatter or bundle rules, while honoring the project-specific overrides in WIKI.md (such as using relative Markdown paths).
3. **Progressive Disclosure:** When looking up information, always start by reading [wiki/index.md](./wiki/index.md) rather than scanning random files.
4. **Capturing New Decisions & Context:** Whenever an important architectural decision, infrastructure change, tool configuration, or key takeaway emerges during a session and you are not running a full wiki ingest right away, drop a concise Markdown note into [inbox/](./inbox/) (YYYY-MM-DD-short-topic.md) so it can be ingested into wiki/ later.
