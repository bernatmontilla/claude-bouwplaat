# Starting a project from this template

A generic setup for building a project with AI agents: rules, agents, a docs loop and GitHub
automation, with nothing project-specific in it. Delete this file once the project is set up.

## Where each piece lives, and why

| Piece | Lives in | Reaches |
|---|---|---|
| Method skills: `test-first`, `root-cause-debugging`, `write-spec`, `plan-product` | your **claude.ai account** | chat, Projects, local Claude Code (synced), cloud sessions |
| Rules (`AGENTS.md`), agents and skills in `.claude/`, docs loop, GitHub templates and workflows | **this repository**, copied per project | local and cloud Claude Code, Gemini/Codex (they read `AGENTS.md`), GitHub |
| Two scheduled tasks per repository | **claude.ai/code/routines** ([docs/automation.md](docs/automation.md)) | start cloud sessions from labels and merges |
| Other people's skills a worker needs (design skills for a website, say) | copied into the project's `.claude/skills/` and committed (`npx skills add <repo> --skill <name>`) | that project, local and cloud |
| Plugins, local tools, anything in `~/.claude/` | your machine only | interactive local sessions; cloud sessions never see them |

Rule of thumb: improve a **method** in the account skill (every project gets it); improve the
**workflow plumbing** here in the template (new projects get it; old ones copy the change by hand
when they want it); keep **project facts** in that project's `AGENTS.md` and `docs/`.

## Checklist for a new project

1. **Create the repository** from this template (GitHub → *Use this template*), then clone it.
2. **Labels**: `bash scripts/dev/labels.sh`.
3. **GitHub settings** (repository → Settings):
   - General → Pull Requests: allow squash merging only; *Automatically delete head branches*;
     **Allow auto-merge**.
   - Rules → Rulesets → new ruleset for `main`: require a pull request and the check `build`.
   - Secrets → Actions: `AUTOMERGE_TOKEN`, a fine-grained token for this repository with Contents
     and Pull requests read/write (so an automatic merge still starts CI and a deploy on `main`).
4. **Cloud environment** for workers: [docs/cloud-environment.md](docs/cloud-environment.md).
5. **The two scheduled tasks**: [docs/automation.md](docs/automation.md), then its *First things
   to test* with a throwaway issue.
6. **Planning round**: in a Claude Code session on the repository, *"Run a planning round with the
   skill plan-product."* It fills the TODOs (`grep -rn TODO .`: `AGENTS.md`,
   `scripts/dev/build.sh`, `.github/labeler.yml`, `docs/review-checklist.md`,
   `.claude/agents/risk-checker.md`, the toolchain step in `.github/workflows/ci.yml`) and writes
   `docs/product/`, the first issues and the plan. Until `labeler.yml` names this project's risky
   paths, put `hold` on PRs you want to read: everything else merges by itself.
7. **Claude review in CI** for risky PRs: install the Claude GitHub app on the repository
   (github.com/apps/claude), run `claude setup-token` on your Mac and add the result as the
   repository secret `CLAUDE_CODE_OAUTH_TOKEN`.
8. Delete what the project does not need: `ui-checker` (nothing to look at), `risk-checker` and
   `claude-review.yml` (nothing risky, a plain website), `drain` (never local).
9. Label the plan's first rows `idea`. Fix whatever in this template got in the way, then copy
   that fix back to the template.

## Per kind of project

| Project | Keep | Add |
|---|---|---|
| App whose data must stay correct | everything | the invariants in `risk-checker`, wide risky paths, guard tests for the hard rules |
| Website | no `risk-checker`, no CI review; nearly everything auto-merges | `ui-checker`; any design skills you use, copied into `.claude/skills/`; a `DESIGN.md` |
| Device with a screen or hardware | everything except CI review, if nothing is risky | a host command that renders the screen to a PNG, so `ui-checker` and the PR can show it; rows that need the hardware are `(local)` |
