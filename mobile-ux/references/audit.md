# UX Audit Reference

Friction checklist, anti-pattern catalog, and a worked example showing the required output shape.

## Contents

- Friction Audit Checklist
- Anti-Patterns
- Sample Audit (output shape)

## Friction Audit Checklist

Run against the requested flow. Record Pass / Fail / Not applicable / Not verified per item. Include evidence for Pass/Fail and a reason for N/A or Not verified. Static artifacts cannot establish response times, offline sync, permission handling, or interruption recovery. A written flow description is the user's claim of behavior: items it states explicitly are Pass/Fail citing its step ("per description, step 4"); items it omits are Not verified. Separate behavioral checks from accessibility checks requiring rendered or assistive-technology evidence.

- [ ] Steps from entry to task completion counted — can any be merged or removed?
- [ ] Any decision asked of the user that a smart default could make?
- [ ] Any data requested that isn't used immediately?
- [ ] Signup/paywall placed after real value, or an earlier gate justified by the service and explained to the user?
- [ ] Progress indication reflects actual completed steps, starting at 0% when none are complete?
- [ ] Every tap yields perceivable feedback within ~100ms (Nielsen's "feels instantaneous" limit)?
- [ ] Every wait over ~1s shows status; waits near 10s show progress or allow leaving (Nielsen response-time limits)?
- [ ] Destructive actions use undo (reversible) or true confirmation (irreversible) — not confirm-everything?
- [ ] Each in-scope screen has a back path, applicable loading/error/empty states, and a post-success destination?
- [ ] System back (Android) and swipe-back (iOS) behave identically to the drawn back affordance on every screen?
- [ ] Deep-link and notification entries land on exact content with a synthesized back path?
- [ ] Form fields all justified, pre-filled where possible, validated inline, input preserved on failure?
- [ ] Every numeric control fits the range, precision, entry frequency, and accessibility needs?
- [ ] Search pre-query guidance fits the task and privacy needs; suggestions use real data and no-results offers recovery?
- [ ] Completed onboarding prompts retire; home adapts only where usage evidence supports different needs?
- [ ] Decision screens present relevant trust evidence, cost, and the committed outcome before the user commits?
- [ ] Static titles and labels free of values a control can change?
- [ ] Status/tracking screens answer the relevant state, timing, location, and recovery/contact questions without inventing unavailable details?
- [ ] Settings grouped by intent, toggles apply immediately, common fixes under 4 taps from home?
- [ ] Export and account deletion self-serve, ≤3 taps, consequences stated, no retention maze?
- [ ] Shared links open the exact content with and without the app installed?
- [ ] Offline edits on two devices reconcile without silent loss; new-device sign-in skips onboarding?
- [ ] Permissions requested in context, never at launch; denial leaves app functional?
- [ ] Notifications map to per-category controls; each tap-through lands on its exact subject?
- [ ] App resumes mid-task state after being killed?
- [ ] Offline behavior defined for every network-dependent screen?
- [ ] All gesture actions have visible alternatives?
- [ ] Core actions operable one-handed (placement itself is the UI skill's call)?
- [ ] Flow usable with system text scaled to maximum?
- [ ] Flow intact with reduced-motion enabled (nothing conveyed only via animation)?
- [ ] Touch-target size: hand to the UI skill as an acceptance check (platform minimums: 44×44 pt iOS HIG, 48×48 dp Material)?
- [ ] Every "Verify by" line in each playbook loaded for this flow (`playbooks.md`, `playbooks-lifecycle.md`) — each counts as a checklist item with its own status.

## Anti-Patterns

Format: symptom — why it hurts — fix.

- **Screen-by-screen design.** Flows have gaps: dead ends, missing back paths, undefined states. — Users fall into holes you never drew. — Design the flow graph first; enumerate loading/error/empty/success for every node.
- **Blank-slate onboarding.** New users face unnecessary setup before a useful action. — Effort before value can increase abandonment. — Useful defaults, optional samples, and progress reflecting actual completed steps.
- **Unnecessary signup wall before value.** Registration demanded before any value without a service need. — Adds effort before users can evaluate the product. — Defer signup when possible; explain any necessary early identity requirement.
- **Silent actions.** Taps with no acknowledgment; saves with no result signal. — User can't tell working from broken; retries cause duplicates. — Immediate acknowledgment and pending status; optimistic updates only with defined failure feedback and rollback or reconciliation.
- **Confirm-dialog everything.** Every minor action interrupted with "Are you sure?" — Can train reflexive dismissal, so real warnings get dismissed too. — Principle 9 in `SKILL.md` (undo over confirm).
- **Launch-time permission barrage.** All OS prompts fired on first open. — No context raises denial risk, and a denied OS prompt usually can't be re-shown. — Contextual asks with pre-framing.
- **Error dead-ends.** Failure screens with no retry, or that discard input. — One flaky request can cost the user their work and erode trust. — Preserve input, retry in place, recovery path everywhere.
- **Feature-count thinking.** Adding options and settings as the answer to every request. — Each addition can tax every user's decisions. — Solve with defaults and removal before addition.
- **Navigation mirroring internal structure.** Sections named after the team's modules or tables. — Users can't predict where things live. — Task-based grouping; test findability with naive users.
- **Interrupting mid-task.** Rating prompts, upsells, announcements during a flow. — Risks breaking task focus when the user is most likely to abandon. — Interrupt only at natural completion boundaries, if at all.
- **Isolated big asks.** A price or effort request shown cold. — Judged against nothing, it is more likely to read as expensive. — Control the preceding context; anchor deliberately and honestly.
- **Back-trap navigation.** System back exits the app from deep screens, loops, or discards input. — Breaks the platform contract users rely on reflexively. — Map system back to logical back everywhere; synthesize stacks for deep-link entries.
- **Notification spam.** Sends triggered by business goals ("we miss you", feature ads) landing on the home screen. — Spends the trust budget; raises the risk that users disable all notifications or uninstall. — Value-event triggers only, per-category controls, tap lands on exact subject.
- **Design without a metric.** Flow shipped with no stated behavioral target. — No way to know it worked; debates settle by opinion. — Name the metric and the rationale before building; check it after.
- **Blank search.** Nothing under the search field until the user types. — All effort on the user; unsure users may leave at the moment of intent. — Pre-query guidance per `playbooks.md` Search (removable recents, browse, or real-data suggestions; an input hint for private or exact-identifier search).
- **Data-dump status screen.** Order number, item list, courier text, date list. — User pieces together state and timing alone; support contacts tend to rise. — Headline state, time window, contact, step timeline.
- **Irrelevant home content.** Completed setup prompts remain, or users see statistics without data. — Primary tasks become harder to find. — Retire completed prompts; adapt other content only when usage evidence shows different needs.
- **Mutable value in static copy.** Title carries a quantity or option the selector changes. — Title lies after the first interaction. — Variable lives in the control; title describes the thing.
- **Cold commit control.** Button says "Buy" with the total elsewhere or nowhere. — Uncertainty at the moment of maximum hesitation. — The commit action states the total; choosing quantity/variant and committing form one uninterrupted sequence (`playbooks.md`, Decision & Commit).
- **Input control mismatched to the task.** A wheel requires excessive scrolling for precise repeated entry. — Extra adjustments increase effort and mistakes. — Choose using range, precision, frequency, and accessibility; one-time text entry is valid when it is easier.

## Sample Audit (output shape)

Condensed example of the required audit format. Include only supported findings. The core task is the job the user opened the flow to do (first meditation, placing an order), not a gate on the way to it. P0 requires evidence that the core task cannot complete (a gate that can be passed, however costly, is P1), or a store-policy or legal violation that blocks release (e.g. no in-app account deletion, custom review gate); a dark pattern is at least P1; P1 covers material friction, recovery, or data-integrity risk; P2 is polish without material task impact. Severity follows observed harm, not the pattern name. Split issues whose harm differs (a 2s splash is P2 even when it precedes a no-skip carousel), order findings within a level by likely drop-off, and name the one to fix first.

> **Flow audited:** Recipe app — first launch to first saved recipe (7 screens, from screen recording).
>
> **P1 — Unjustified signup wall at launch.** Evidence: screen 1 demands email/password before any recipe is visible. Harm: user has zero evidence of value; the gate creates abandonment risk before the product has shown anything; the drop-off is unmeasured. Fix: allow browse + save locally; request signup at first cross-device action, framed as "save your collection everywhere." Metric: launch → first-save conversion.
>
> **P1 — No back path from recipe detail after notification tap.** Evidence: tapping the "weekly pick" notification opens detail with no back affordance and swipe-back does nothing; the user must relaunch to reach Home. Harm: entry point likely converts one recipe view instead of a session. Fix: synthesize back stack to Home → Detail on notification entry. Metric: screens-per-session from notification opens.
>
> **P1 — Search filters reset on error.** Evidence: applying 4 filters then losing connection returns to unfiltered list; selections gone. Harm: user repeats work; second failure likely abandons. Fix: preserve filter state through failure; retry in place. Metric: search abandonment rate after network error.
>
> **P1 — "Save" gives no feedback and permits duplicates.** Evidence: tap on save shows no change for ~1s until list syncs. Harm: double-taps create duplicate saves; uncertainty reads as breakage. Fix: acknowledge the save immediately and prevent duplicate submission while pending; show saved state on confirmation and retry on failure. Metric: duplicate-save rate.
>
> **Checklist status (excerpt):** Steps counted — Fail (signup gate, see P1). Signup after value — Fail. Tap feedback ≤100ms — Not verified (recording frame rate too low to time). Back/deep-link entry — Fail on iOS (see P1); Android Not verified (iOS recording only). Resume after kill — Not verified (not shown). Text scaled to maximum — Not verified (needs rendered device). Paywall — Not applicable (none in flow).
>
> **Assumptions:** no analytics access — funnel claims from flow structure, not measured data; iOS recording only — Android back behavior unverified.

Every finding = severity + evidence (screen/step + observed behavior) + user harm + minimal behavioral fix + proving metric. No visual prescriptions anywhere.
