---
name: mobile-ux
description: Use when designing, reviewing, or answering a question about iOS/Android app behavior and flows — onboarding, sign-in/recovery/session expiry, navigation and back/deep links, empty states, lists and feeds, forms, permissions and consent prompts, notifications, paywalls and rating prompts, error/offline recovery, app updates, account deletion, accessibility and localization behavior, engagement/retention mechanics, validating a change — or running a UX/friction audit. Behavior, flows, and psychology only; visual look-and-feel belongs to a UI skill.
---

# Mobile UX

Behavioral UX for mobile apps (iOS + Android, any framework): how an app behaves, flows, and feels over time — never how it looks.

**The wireframe test** gates every recommendation: would it survive if the app were rendered as unstyled wireframes? Fails the wireframe test → out of scope, hand it to the UI skill.

## Scope

Handle: flow design and review, onboarding/first-run, sign-in/recovery/session expiry, lifecycle-adaptive home, activation and time-to-value, navigation logic and information architecture, search, lists and feeds, decision-and-commit screens, post-action status/tracking, friction audits, engagement and retention mechanics, notifications and re-engagement, paywall and rating-prompt placement, app updates, error/offline behavior, forms, permissions and platform consent, interruption/resume, localization and assistive-technology behavior, validating a change.

Route elsewhere: anything that fails the wireframe test — styling, layout geometry, component appearance, motion aesthetics — goes to **the UI skill**: the project's own, or `premium-flutter-ui` for Flutter. Every later mention of "the UI skill" means this. Mixed request: handle the behavioral half here, hand the visual half off with the contract under Output. When the behavioral half is causing harm now (money, data loss, lockout), say explicitly that it ships first and the visual change can follow.

## Modes

Pick one mode per request, read its reference, and produce its output shape (see Output).

| Request | Mode | Read first |
|-|-|-|
| "Audit / review this flow", friction hunt | **Audit** | `references/audit.md`, then the playbook for each screen type in the flow |
| "Design / redesign this flow" | **Flow design** | `references/flow-design.md`, then the playbook for each screen type the flow passes through |
| Single question or single screen ("where should the paywall go?", "is this empty state OK?") | **Consult** | The playbook for each decision point asked about |

Compound request: a question whose answer needs new screens or steps is Flow design; several independent questions are one Consult each; a question plus a visual ask is Consult plus the UI hand-off.

Audits need a flow artifact (screens, flow map, recording, or written flow description). Missing → ask for one first, and say why: an app name alone is not auditable because the live flow varies by version, platform, region, and running experiments, so an audit from memory reviews a flow the user may not ship.

Playbooks (per-flow rules, each ending in "Verify by"):

- `references/playbooks.md` — onboarding, lifecycle-adaptive home, empty states, search, errors, forms, decision & commit screens, post-action status & tracking, permissions, interruption/resume, navigation & IA, platform back & deep-link entry, notifications & re-engagement, paywall placement, settings & account, data export/deletion, sharing & invites, multi-device & sync.
- `references/playbooks-lifecycle.md` — sign-up/sign-in & recovery, session expiry, lists/feeds & refresh, app updates & migration, rating & review prompts, platform consent prompts (Android 13 notifications, iOS provisional, ATT), screen size/rotation, localization, assistive-technology behavior, validating a change.

## Core Principles

Context-dependent heuristics, not guaranteed effects. Predicted conversion or retention gains are hypotheses; separate observed evidence from assumptions and validate against the stated metric.

1. **Decision fatigue is the default enemy.** Cut options, defer optional decisions, merge two decisions into one wherever possible.
2. **Smart defaults do the user's work.** Pre-select the majority-case value where a dominant behavior exists, and make changing it one tap; "compose from scratch" becomes "scan and adjust". Presets come from real usage data.
3. **Goal proximity.** Progress credits genuinely completed work; start at 0% when none is complete.
4. **Reciprocity: value before the ask.** Deliver a real result before requesting signup, data, or payment. Require identity earlier only when privacy or the core service needs it, and say why.
5. **Investment / IKEA effect.** Meaningful creation builds attachment; offer it before signup when it delivers immediate value and can be preserved. Labor added only to raise commitment is a dark pattern.
6. **Loss framing is a hypothesis.** Use it only to convey a real, verifiable consequence (actual data, actual expiry).
7. **Contrast.** Every cost or ask is judged against what came just before; sequence context deliberately and show the honest anchor first.
8. **Feedback is a contract.** Acknowledge every tap and give every perceptible wait a status. Optimistic updates only for recoverable actions, with defined rollback; payments, permissions, and irreversible actions stay pending until authoritative confirmation.
9. **Undo over confirm.** Act-then-undo for reversible actions; confirmation for genuinely irreversible ones.
10. **Flows, not screens.** Every screen answers: how did I get here, what shows while loading, on failure, when empty, and where success leads.
11. **The platform owns back.** System back and swipe-back are reflexes every screen and deep-link entry honors (`playbooks.md`, Platform Back).
12. **Metric first.** Name the user problem and its behavioral metric before designing; record the rationale per decision.

## Engagement Mechanics + Ethical Guardrails

The line: influence aligned with the user's own goals is design; influence against their interest is deception.

| Mechanic | Ethical use | Never |
|-|-|-|
| Streaks / progress | Reinforce a habit the user chose; allow repair for missed days | Punish lapses to manufacture anxiety |
| Variable rewards | Vary genuine content the user came for | Slot-machine loops empty of value |
| Loss framing | State real, verifiable losses (deletion date, expiring benefit) | Fake countdowns, invented scarcity |
| Investment | Let users build things they own and can export | Hold user data hostage to block leaving |
| Social proof | Show real activity and real numbers | Fabricated counts, reviews, "X people viewing" |
| Curiosity gaps | Tease real content behind the tap | Bait with content that isn't there |
| Reciprocity | Give value first, then ask | Confirmshaming decline copy |
| Defaults | Default to the user's likely intent | Pre-checked upsells |

Hard rules: decline options are always available, findable, and neutrally worded. Cancel takes no more steps than subscribe. Notifications serve the user's stated interest, are individually controllable, and earn their interruption. A mechanic that works only while unnoticed is a dark pattern; ship only mechanics that survive the user noticing them.

## Output

**Audit** → findings sorted P0 / P1 / P2 (definitions and sample in `references/audit.md`), ordered within each level by likely drop-off, with the fix-first finding named. Rate each issue on its own harm: don't bundle a polish item into a bigger finding, since the team then can't tell what to fix first. Each finding: evidence (screen/step + observed behavior), user harm, minimal behavioral fix, metric that proves the fix. Scope the checklist to the requested flow; mark each item Pass / Fail / Not applicable / Not verified, with a reason for the last two. Screenshots establish visible content only — runtime timing, offline, screen-reader behavior, and recovery stay Not verified without suitable evidence.

**Flow design** → numbered step map (entry → value moment → exit), applicable states per step (loading/empty/error/success, N/A with reason), interruption/resume, primary metric + guardrail with a baseline-backed or user-supplied target, else labeled *pending baseline* (`references/flow-design.md`). One flow per deliverable.

**Consult** → the answer first, the playbook rule it rests on, one "Verify by" check the user can run, and a note on which part of their question fell outside the evidence given. Name a metric only when the answer changes a flow's behavior; baseline it per `references/flow-design.md` or mark it *pending baseline*.

> *Q: "Should the paywall show right after signup?"* No: signup isn't a value moment. Show it after the first completed core action, at a task boundary, with a neutrally worded decline visible from the first frame (`playbooks.md`, Paywall & Upgrade Placement). Verify by: tracing the paywall trigger and confirming a completed core action precedes it. Outside the evidence given: whether your free tier limits are disclosed up front. Metric: value moment → paid conversion, *pending baseline*. Assumptions: the app has a free tier; "signup" means account creation, not purchase.

Every mode ends by listing assumptions made from missing context. For mixed UX/UI work, hand the UI skill one compact contract: **user goal → flow and applicable states → constraints → acceptance checks**. The UI skill reuses it instead of repeating the flow work.

## Completion Check

Done when every line passes for the chosen mode:

- [ ] Every recommendation passes the wireframe test; visual items are handed off, not prescribed.
- [ ] Audit: findings are ranked (severity per own harm, ordered within level, fix-first named); every in-scope checklist item has a status; every Pass/Fail cites evidence; every finding has severity, evidence, harm, fix, and metric.
- [ ] Flow design: every step names its states; interruption/resume and back behavior are stated; primary metric + guardrail are present with a baseline or a *pending baseline* label.
- [ ] Consult: the answer cites the playbook rule and gives one runnable check.
- [ ] No uplift or behavioral effect is stated as certain; assumptions are listed.
- [ ] Any engagement mechanic used passes the Ethical Guardrails table.
