# skills

Agent skills for Claude Code (and other agents that read `SKILL.md`).

| Skill | What it does |
|-|-|
| [mobile-ux](mobile-ux/) | Behavioral UX for iOS/Android apps: flow design, friction audits, and single-question consults (onboarding, paywalls, permissions, deletion, errors, notifications). Behavior only; visuals go to a UI skill. |

## Install

Claude Code, personal (all projects):

```bash
git clone https://github.com/razankv13/skills.git
cp -r skills/mobile-ux ~/.claude/skills/
```

Project only: copy `mobile-ux/` into `.claude/skills/` in your repo.

## Evals

`mobile-ux/evals/evals.json` holds 5 test prompts and 30 pass/fail checks (consult, flow design, audit, mixed UX/UI request, audit with no artifact).

| Config | Pass rate |
|-|-|
| With skill | 100% (30/30) |
| Without skill | 47% |

One run per prompt on Claude Sonnet 5, graded by a separate model. Treat it as a regression check, not proof of zero variance.

## License

MIT
