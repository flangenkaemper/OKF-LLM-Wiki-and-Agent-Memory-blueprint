# LLM Wiki & Knowledge Base Blueprint (Karpathy + OKF v0.2)

This repository is a ready-to-use **Blueprint** combining:
1. **Andrej Karpathy's LLM Wiki workflow** (Ingest, Query, Lint over plain Markdown files).
2. **Google's Open Knowledge Format (OKF v0.2)** (standardized YAML frontmatter, progressive disclosure via index.md, provenance via sources, and human-in-the-loop review via generated & verified).

## Quick Start
1. Unzip this folder into your target directory (e.g., personal-wiki, work-personal-wiki, or work-team-wiki).
2. Run git init && git add . && git commit -m "chore: init llm-wiki".
3. Drop 3-5 initial raw files into raw/.
4. Open your preferred agent harness (Codex, Claude Code, Gemini, etc.) and prompt:
   "Read WIKI.md, then ingest the files currently in raw/ into wiki/ and show me a summary of the concepts you created."
