# Claude Secure Template

A language-agnostic, production-ready template for Claude-first development. Provides secure defaults, AI task routing, BMAD workflow integration, and deterministic CI gates so you can focus on your project rather than its scaffolding.

## Live Project — Upskill Example-Page Redesign

Alongside the template scaffolding, this repo hosts a live web project: the **Upskill programme's Lecture 1 example-page redesign**. The brief — take a deliberately-plain example page and improve it on the four design dials (**typography, colour, grid/spacing, hierarchy**) as a single self-contained `index.html` with a tasteful D&D theme, keeping every word of the original content verbatim.

- 🌐 **Live page:** <https://moon-knight13.github.io/project_dungeon/> — deployed to GitHub Pages from [`site/index.html`](site/index.html)
- 🔁 **Before → after:** the [plain baseline](https://moon-knight13.github.io/project_dungeon/before.html) ([`site/before.html`](site/before.html)) vs. the redesign above
- 📐 **Design rationale:** [`docs/upskill/RATIONALE.md`](docs/upskill/RATIONALE.md) — the four dials plus a hosting comparison (Netlify / GitHub Pages / Cloudflare Pages)

### Source & task tracking

Planned and tracked on the GitHub Project board as an epic with one issue per task (see [docs/KANBAN_WORKFLOW.md](docs/KANBAN_WORKFLOW.md)):

- **Epic:** [#11 — Redesign Upskill example page](https://github.com/Moon-Knight13/project_dungeon/issues/11)
- **Stories:** [#12 redesigned `index.html`](https://github.com/Moon-Knight13/project_dungeon/issues/12) · [#13 `before.html` baseline](https://github.com/Moon-Knight13/project_dungeon/issues/13) · [#14 `netlify.toml` + security headers](https://github.com/Moon-Knight13/project_dungeon/issues/14) · [#15 `web-ci` gate](https://github.com/Moon-Knight13/project_dungeon/issues/15) · [#16 design rationale](https://github.com/Moon-Knight13/project_dungeon/issues/16) · [#17 `.templatesyncignore`](https://github.com/Moon-Knight13/project_dungeon/issues/17)
- **Hosting → GitHub Pages:** [PR #34](https://github.com/Moon-Knight13/project_dungeon/pull/34)

> The live URL goes active once [PR #34](https://github.com/Moon-Knight13/project_dungeon/pull/34) merges to `main` and the GitHub Pages deploy runs.

## What's Included

- **AI routing** — routes low-risk work to a local Ollama model; escalates to Claude for security, architecture, and cross-cutting changes
- **Security gates** — gitleaks secret scanning, semgrep SAST (including MITRE ATLAS AI/ML rules), Trivy container scanning, all enforced in CI
- **BMAD workflow** — structured product → engineering planning via the `/bmad` skill
- **Kanban orchestration** — a per-repo GitHub Project board where a human orchestrator hands work to Claude sessions or local models; agents claim issues collision-free via `/next-issue` and `/run-epic` (see [docs/KANBAN_WORKFLOW.md](docs/KANBAN_WORKFLOW.md))
- **Devcontainer** — deny-by-default network firewall, pre-installed tooling, Claude CLI with mounted auth volume
- **Branch protection bootstrap** — one-command GitHub branch protection with required status checks
- **Day-0 validation** — `/day0-check` walks you through every setup step with pass/fail output and remediation hints

## Prerequisites

- Docker + VS Code Dev Containers extension
- Git with SSH access to GitHub
- Claude Code CLI (authenticated before first session)
- Optional: Ollama on host port 11434 for local model offload

See [docs/TEMPLATE_GUIDE.md](docs/TEMPLATE_GUIDE.md) for the full setup guide including Caveman token compression and PII-Shield.

## Quick Start

1. **Use this template** — click "Use this template" on GitHub, or clone and re-init:
   ```bash
   git clone <this-repo> my-project && cd my-project && rm -rf .git && git init
   ```

2. **Open in devcontainer** — VS Code prompts to reopen; accept. The container installs all tooling automatically on start.

3. **Complete day-0 setup** — run the checklist in the container terminal:
   ```bash
   bash scripts/check-day0.sh
   ```
   Or from Claude: `/day0-check`

4. **Validate the template** — confirm all template integrity checks pass:
   ```bash
   bash scripts/validate-template.sh
   ```

## Repository Structure

```
.claude/commands/    Claude Code skills (/bmad, /bmad-to-board, /next-issue, /run-epic, /day0-check, /route-task, /security-audit, /firewall-allow)
.devcontainer/       Dev environment with deny-by-default firewall and pre-installed tooling
.github/             Workflows (CI, secret scan, semgrep, container scan, weekly audit); issue & PR templates
docs/                TEMPLATE_GUIDE.md, AI_ROUTING_POLICY.md, BMAD_WORKFLOW.md, KANBAN_WORKFLOW.md
scripts/             Bootstrap (incl. board), routing, CI helpers, and template validator
```

## Deriving a New Project

When you start a new project from this template:

1. Replace this `README.md` with your project README — use [`docs/README.template.md`](docs/README.template.md) as a starting point.
2. Customise `.github/CODEOWNERS` with real GitHub users or teams.
3. Add `scripts/ci/lint-*.sh` and `scripts/ci/test-*.sh` for your language stack (see `scripts/ci/README.md`).
4. Copy config files: `cp .env.example .env && cp .claude/settings.json.example .claude/settings.json`
5. Run `APPLY=true bash scripts/bootstrap-github-settings.sh` to enable branch protection.
6. Set up the Kanban board: `gh auth refresh -s project` then `APPLY=true bash scripts/bootstrap-project.sh` (see [docs/KANBAN_WORKFLOW.md](docs/KANBAN_WORKFLOW.md)).

## License

Apache 2.0 — see [LICENSE](LICENSE).
