# Repository Instructions

This repository coordinates n8n-to-Codex Cloud implementation handoffs.

## General guidance

- Keep changes small and reviewable.
- Prefer Markdown documentation for coordination artifacts.
- Do not add OpenAI API keys or API-key based Codex GitHub Actions.
- Use Codex Cloud GitHub tasks for implementation delegation.

## Verification task guidance

When asked to verify Codex Cloud access, create or update `codex-cloud-verification.md` with:

- UTC timestamp of the task execution.
- Confirmation that this repository was readable.
- A short summary of the requested task.
- Any blocker encountered.

Open a pull request for the change rather than pushing directly to `main`.
