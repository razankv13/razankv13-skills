# skills

Agent skills for Claude Code (and other agents that read `SKILL.md`).

| Skill | What it does |
|-|-|
| [mobile-ux](plugins/mobile-ux/skills/mobile-ux/) | Behavioral UX for iOS/Android apps: flow design, friction audits, and single-question consults (onboarding, paywalls, permissions, deletion, errors, notifications). Behavior only; visuals go to a UI skill. |
| [premium-flutter-ui](plugins/premium-flutter-ui/skills/premium-flutter-ui/) | Premium Flutter UI for iOS/Android: generate, refine, review, and audit screens, components, tokenized themes, motion, accessibility, dark mode, and M3 migration. Visuals only; flows go to mobile-ux. |

## Install

**Claude Code**

```
/plugin marketplace add razankv13/razankv13-skills
/plugin install mobile-ux@razankv13-skills
/plugin install premium-flutter-ui@razankv13-skills
```

**Codex CLI**

```bash
codex plugin marketplace add razankv13/razankv13-skills
codex plugin add mobile-ux@razankv13-skills
codex plugin add premium-flutter-ui@razankv13-skills
```

**Any other agent that reads `SKILL.md`** (manual copy):

```bash
git clone --depth 1 https://github.com/razankv13/razankv13-skills.git
cp -r razankv13-skills/plugins/*/skills/* ~/.claude/skills/   # Claude Code
cp -r razankv13-skills/plugins/*/skills/* ~/.agents/skills/   # Codex and others
```

Restart the agent after installing. Each skill triggers on its own for matching questions, or ask for it by name.

## Evals (mobile-ux)

`plugins/mobile-ux/skills/mobile-ux/evals/evals.json` holds 5 test prompts and 30 pass/fail checks (consult, flow design, audit, mixed UX/UI request, audit with no artifact).

| Config | Pass rate |
|-|-|
| With skill | 100% (30/30) |
| Without skill | 47% |

One run per prompt on Claude Sonnet 5, graded by a separate model. Treat it as a regression check, not proof of zero variance.

## License

MIT
