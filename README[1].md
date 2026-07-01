# GStack

**A virtual product team for Claude Code.**

GStack drops a small crew of expert helpers into your repo — a CEO for product thinking, a designer with taste, an engineering manager who plans before coding, a reviewer who's a stickler about quality, a QA lead that can drive a real browser, a security reviewer, a docs writer, and a release manager.

You describe what you want to build. They plan it, build it, review it, test it, and help you ship it.

```bash
# Install in any repo (takes ~10 seconds)
curl -fsSL https://raw.githubusercontent.com/raoksn88/gstackrao/main/scripts/install.sh | bash

# Then in Claude Code:
/plan a tiny URL shortener with a paste box and a copy button
/build
/review
/qa https://localhost:3000
/ship
```

That's the whole experience.

---

## Why this exists

Claude Code is great, but a blank prompt asks a lot of you. You have to remember to plan before building. To review before merging. To test the actual UI in a browser, not just trust that the code compiles. To think about whether a stranger could understand the README.

GStack encodes the discipline of a small product team into eight subagents and ten slash commands. You get the muscle memory of "plan, build, review, test, ship" without having to type the prompts.

It's also opinionated about one thing: **a real product team disagrees with itself constructively.** The reviewer will push back on the engineering manager's plan. The designer will call out the developer's ugly modal. The QA lead will refuse to sign off on a flaky flow. That friction is the point.

---

## The team

| Role | Who they are | When to call them |
|---|---|---|
| **CEO** (`@ceo`) | Product thinker. Asks "what are we actually solving?" before any code is written. | Start of every project. Whenever you feel scope-drifty. |
| **Designer** (`@designer`) | Has taste. Spots ugly modals, confusing flows, fonts that fight each other. | After UI lands. Before launch. |
| **Eng Manager** (`@em`) | Architects the work. Breaks features into shippable pieces. Predicts the edge cases nobody thought of. | Before implementation. |
| **Reviewer** (`@reviewer`) | Code quality stickler. Catches the bugs your tests won't. | Before every merge. |
| **QA Lead** (`@qa`) | Drives a real browser (via Playwright MCP). Tests the thing, doesn't just believe the thing. | Before any deploy. |
| **Security** (`@security`) | Reads code adversarially. Looks for the door you left unlocked. | Before public launches. After auth changes. |
| **Docs** (`@docs`) | Writes README/CHANGELOG/onboarding that humans actually read. | Before any release. |
| **Release** (`@release`) | Cuts the version, writes the notes, sequences the rollout. | When you're ready to ship. |

Each one is a **Claude Code subagent** — its own context window, its own tools, its own system prompt. They don't pollute your main session and they can run in parallel.

---

## Quickstart

### Requirements

- [Claude Code](https://docs.claude.com/en/docs/claude-code/overview) installed (`npm install -g @anthropic-ai/claude-code`)
- Node.js 18+
- A git repo (any language, any stack)

### One-line install

**macOS / Linux:**

```bash
curl -fsSL https://raw.githubusercontent.com/raoksn88/gstackrao/main/scripts/install.sh | bash
```

**Windows (PowerShell):**

```powershell
iwr -useb https://raw.githubusercontent.com/raoksn88/gstackrao/main/scripts/install.ps1 | iex
```

That drops `.claude/agents/`, `.claude/commands/`, and a starter `CLAUDE.md` into your current directory. Nothing global. Nothing magic.

### Manual install

```bash
git clone https://github.com/raoksn88/gstackrao.git
cd your-project
cp -r gstackrao/template/.claude .
cp gstackrao/template/CLAUDE.md ./CLAUDE.md   # (skip if you already have one)
```

### Verify

Open Claude Code in your repo and type `/`. You should see the GStack commands listed: `/plan`, `/build`, `/review`, `/qa`, `/security`, `/docs`, `/ship`, `/critique`, `/standup`, `/team-init`.

Type `@` and you should see the eight specialists.

---

## The main workflow

The flow GStack is built around is **plan → build → review → test → ship**.

```
┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐
│  /plan  │ →  │ /build  │ →  │/review  │ →  │  /qa    │ →  │ /ship   │
│  (CEO+  │    │ (you +  │    │(reviewer│    │ (QA on  │    │(release │
│   EM)   │    │  Claude)│    │+security│    │ real    │    │ manager)│
│         │    │         │    │         │    │ browser)│    │         │
└─────────┘    └─────────┘    └─────────┘    └─────────┘    └─────────┘
```

### 1. Plan

```
/plan a small expense tracker. users can add expenses, tag them, see a monthly chart.
```

The CEO subagent asks the product questions (who's it for? what's the smallest version that's actually useful? what's explicitly out of scope?). The engineering manager turns the answers into an architecture and a sequenced task list. Output is a `PLAN.md` you can argue with before any code is written.

### 2. Build

```
/build
```

Claude Code (your main session, not a subagent — it needs full tool access) reads `PLAN.md` and implements task by task. It stops between major chunks to let you sanity-check.

### 3. Review

```
/review
```

Runs the reviewer and security subagents in parallel against the diff. Reviewer focuses on code quality, error handling, and tests. Security focuses on injection vectors, auth, secrets, and privilege escalation. You get a prioritized list of fixes.

### 4. QA

```
/qa https://localhost:3000
```

The QA lead spins up Playwright MCP, navigates your staging URL, walks the happy path, then tries to break it. Returns a report with screenshots and repro steps for anything that fails. **This requires Playwright MCP** — see [Optional: Browser QA setup](#optional-browser-qa-setup) below.

### 5. Ship

```
/ship
```

The release manager runs the docs subagent (does the README match what shipped?), drafts release notes, checks the version is bumped, and gives you the go/no-go.

---

## Other commands

| Command | What it does |
|---|---|
| `/critique` | Designer + reviewer + security all roast the current code in parallel. Honest, brutal, useful. |
| `/standup` | Quick "what changed since yesterday, what's blocked" summary from git history. |
| `/team-init` | Set up GStack for a new teammate. Walks them through CLAUDE.md and which commands they'll use most. |
| `/docs` | Update README, CHANGELOG, and inline docs to match what actually shipped. |
| `/security` | Standalone security pass (you don't have to wait for `/review`). |

Each command lives in `.claude/commands/` as a readable markdown file. **Read them. Edit them. They're not magic — they're prompts.**

---

## Optional: Browser QA setup

`/qa` uses [Playwright MCP](https://github.com/microsoft/playwright-mcp) to drive a real Chromium. To enable it:

```bash
# In your project root
claude mcp add playwright -- npx @playwright/mcp@latest
```

Then in Claude Code, run `/mcp` to confirm Playwright is listed. If it's not, restart Claude Code.

Without Playwright MCP, `/qa` falls back to having the QA lead write a Playwright test file you can run yourself. Less magical, still useful.

---

## Team mode

If you want your whole team using GStack, commit `.claude/` to git:

```bash
git add .claude/ CLAUDE.md
git commit -m "chore: add GStack virtual team"
```

Teammates who clone the repo automatically get the same agents, commands, and CLAUDE.md. No install step.

For **recommending but not requiring**, ship a `.gstack-recommended` marker and an onboarding command:

```bash
# In your repo
echo "1.0" > .gstack-recommended
```

When a teammate opens Claude Code in the repo and runs `/team-init`, GStack detects the marker and offers to set everything up.

For **requiring**, add a CI check (template provided in `examples/ci/`) that fails the build if `.claude/agents/` is missing or out of date.

See [`docs/team-mode.md`](docs/team-mode.md) for the full story.

---

## Customizing

Every agent and command is a plain markdown file. To change how the reviewer behaves, open `.claude/agents/reviewer.md` and edit the prompt. To add a new command, drop a `.md` file in `.claude/commands/`.

The repo includes `examples/custom/` showing how teams have extended GStack — a designer subagent tuned for marketing sites, a reviewer that knows your team's specific linting rules, a `/postmortem` command, and a release manager that drafts changelogs from your team's commit conventions.

---

## What this is not

- **Not a replacement for thinking.** The agents will produce confident-sounding plans for terrible ideas. The CEO subagent helps catch this, but ultimately you're the one shipping.
- **Not a CI replacement.** `/review` and `/qa` are pre-merge checks, not the only checks. Keep your real tests.
- **Not autonomous.** Every command stops and waits for you. That's intentional.
- **Not deeply opinionated about your stack.** GStack works in any language. The agents adapt to your repo's conventions (which is why CLAUDE.md matters — see below).

---

## CLAUDE.md is the source of truth

Every subagent reads `CLAUDE.md` at the start of every run. It's where you encode the specific rules of *your* codebase:

- The build/test/lint commands
- Coding conventions (TypeScript strict? Tabs vs spaces? Commit format?)
- Architectural rules ("never call the DB directly from a route handler")
- What "done" means on your team

The starter `CLAUDE.md` is a template with prompts. Fill it in — it's the single highest-leverage thing you can do to make GStack good in your repo.

See [`docs/claude-md-guide.md`](docs/claude-md-guide.md).

---

## Examples

- [`examples/saas-feature/`](examples/saas-feature) — End-to-end transcript of using GStack to ship a billing feature.
- [`examples/bugfix/`](examples/bugfix) — Triaging and fixing a P1 bug.
- [`examples/refactor/`](examples/refactor) — A multi-day refactor across many PRs.
- [`examples/custom/`](examples/custom) — Teams who've forked GStack and added their own roles.

---

## Documentation

- [Setup guide](docs/setup.md) — install, verify, troubleshoot
- [Team mode](docs/team-mode.md) — recommend or require for your team
- [CLAUDE.md guide](docs/claude-md-guide.md) — how to write the constitution
- [Customizing](docs/customizing.md) — add roles, tune prompts, fork the toolkit
- [How it works](docs/how-it-works.md) — what's under the hood

---

## Contributing

PRs welcome. The bar is: does this help a small team ship better software? If yes, we want it.

If you're adding a new subagent or command, please include:
- A clear description of when to use it
- An example transcript in `examples/`
- An update to this README's table

---

## License

MIT. See [LICENSE](LICENSE).

---

## Credits

GStack is built on top of [Claude Code](https://docs.claude.com/en/docs/claude-code/overview) by Anthropic. It uses the [Playwright MCP](https://github.com/microsoft/playwright-mcp) server for browser QA. The subagent and slash command formats are documented in the [Claude Code docs](https://docs.claude.com/en/docs/claude-code).
