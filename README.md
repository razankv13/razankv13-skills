# skills

Agent skills for Claude Code (and other agents that read `SKILL.md`).

| Skill | What it does |
|-|-|
| [mobile-ux](skills/mobile-ux/) | Behavioral UX for iOS/Android apps: flow design, friction audits, and single-question consults (onboarding, paywalls, permissions, deletion, errors, notifications). Behavior only; visuals go to a UI skill. |

## Install

**Claude Code**

```
/plugin marketplace add razankv13/skills
/plugin install mobile-ux@razankv13-skills
```

**Codex CLI**

```bash
codex plugin marketplace add razankv13/skills
codex plugin add mobile-ux@razankv13-skills
```

**Any other agent that reads `SKILL.md`** (manual copy):

```bash
git clone --depth 1 https://github.com/razankv13/skills.git
cp -r skills/skills/mobile-ux ~/.claude/skills/   # Claude Code
cp -r skills/skills/mobile-ux ~/.agents/skills/   # Codex and others
```

Restart the agent after installing. The skill triggers on its own for mobile UX questions, or ask for it by name.

## Evals

`skills/mobile-ux/evals/evals.json` holds 5 test prompts and 30 pass/fail checks (consult, flow design, audit, mixed UX/UI request, audit with no artifact).

| Config | Pass rate |
|-|-|
| With skill | 100% (30/30) |
| Without skill | 47% |

One run per prompt on Claude Sonnet 5, graded by a separate model. Treat it as a regression check, not proof of zero variance.

## License

MIT
