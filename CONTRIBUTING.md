# Contributing to GStack

Thanks for considering a contribution. GStack stays useful by staying small and opinionated, so the bar for additions is: would this help another team ship better software?

## What we welcome

- **Fixes to existing agents/commands**: clearer prompts, fewer false positives, better verdicts.
- **New examples**: real transcripts of GStack used on real work. Bugfix, refactor, design audit — whatever's useful.
- **Documentation improvements**: shorter sentences, working code snippets, fixing stale references.
- **New agents or commands**: only if there's a strong general case. "We need an accessibility reviewer" — probably yes. "We need a Kubernetes deploy command" — probably not (too stack-specific; better as a fork).

## What we're cautious about

- **Adding tools to agents**. Tighter tool allowlists are safer. Don't add `Bash` to a reviewer agent just because it would be convenient.
- **Adding new commands that overlap existing ones**. Better to extend `/review` than to add `/check`.
- **Marketing voice in docs**. "Revolutionary AI-powered" gets reverted. Keep it plain.

## Process

1. **Open an issue first** for anything bigger than a typo. Saves you and us time if it's not a fit.
2. **Fork, branch, PR.** Standard GitHub flow.
3. **Test your change** by installing locally:
   ```bash
   GSTACK_REPO=https://github.com/your-org/gstack GSTACK_REF=your-branch \
     bash scripts/install.sh
   ```
   in a scratch directory, then actually use the agent or command on real code.
4. **Update docs and the README's command/agent table** if you added something.
5. **Include an example transcript** in `examples/` if you added a new command or agent. Even a short one helps next person understand the intent.

## Style

- Agent and command files are markdown with YAML frontmatter. Match the existing tone — direct, specific, anti-bullshit.
- Avoid emojis except where they carry information (the 🟢/🟡/🔴 release verdicts).
- When in doubt, cut. A working agent that loses its voice is a regression.

## Releasing

Maintainers cut releases. Contributors don't need to bump versions in PRs — that happens at release time.

## Code of conduct

Be a colleague. Disagree with ideas, not people. If a review comment lands wrong, assume good intent and clarify.

## Questions

Open a discussion on the repo, or file an issue tagged `question`.
