# Skill Evaluation

Maintenance only. These authored scenarios test whether an agent applies this skill consistently; they are not evidence of a shipped app's quality. Use them after changes to routing, defaults, state contracts, or evidence rules. The bundled Dart tests exercise the reference component separately.

## Procedure

Give each scenario's Prompt and Input to a fresh agent with this skill. Keep the acceptance checks hidden until grading. Requests below are reviews of supplied evidence: no production edits or external actions. Save the response, mark every acceptance check pass/fail/unverified, and cite the response supporting each pass. A missing required observation is a failure; a stated lack of runtime evidence is expected. Fix guidance only when the miss exposes ambiguity or missing coverage, then repeat the failed scenario.

## Form: uncertain write and dismissal

**Prompt:** Review this Flutter edit-address sheet. Give prioritized findings and the smallest fixes. Do not implement changes or claim tests ran.

**Input:** The draft is owned by the sheet. Save sends a request; the server can commit before the client times out. Timeout shows “Save failed” and Retry repeats the write. There is no offline queue or known idempotency contract. `PopScope(canPop: !dirty)` surrounds the editor, but `showModalBottomSheet` uses default drag/barrier dismissal. The actions have no bottom safe-area or keyboard padding. No screenshots, test results, or native-access checks are supplied.

**Acceptance:**
- Flags uncertain completion and requires reconciliation/existing idempotency before another write; invents no offline queue.
- Covers drag, barrier, explicit close, and system-back draft protection; distinguishes these dismissal paths.
- Requires reachable actions under keyboard/system insets and identifies the missing rendered/access evidence.
- Uses findings with evidence and minimal fixes; makes no verified quality or test-pass claim.

## Search: reversed completion and numeric locale

**Prompt:** Review this search/filter behavior for implementation risks. Give prioritized findings, minimal fixes, and focused checks.

**Input:** Request A searches “tea”; request B then searches “teapot”. B succeeds first, then A fails and clears the results. The widget is disposed while a third request remains pending. The price filter offers a decimal keyboard but parses text using `double.parse` with no validation. A supported locale uses comma decimals. No render, semantics, native, or performance evidence is supplied.

**Acceptance:**
- Guards both stale success and stale error, preserving B's valid results; names a reversed-completion check.
- Separates widget disposal/mounted checks from request cancellation and state-owner lifetime.
- Treats keyboard type as an input aid; handles locale, pasted/temporary invalid text, finite values, and domain range/precision before commit.
- Names focused failure checks and leaves appearance, access, and performance unverified.

## Refinement: protect scope and meaning

**Prompt:** Review a proposed spacing-only change to an existing Flutter navigation bar. Recommend the smallest correction.

**Input:** The app intentionally uses purple, rounded shapes, and stock Material controls. Existing tokens and navigation behavior are consistent. The proposed diff changes label padding but also replaces the palette/font and adds a new navigation package. At the project's large-text test size, ellipsis makes two destination labels identical. Only before/after screenshots and this diff summary are supplied.

**Acceptance:**
- Preserves the deliberate brand, framework controls, and navigation behavior; removes unrelated redesign/dependency changes.
- Treats indistinguishable labels as a task/access defect, not an acceptable fixed-height shortcut; proposes shorter localized labels or adaptive layout while preserving full semantic names.
- Restricts validation to the affected layout/states, with runtime/access checks marked unverified.
- Does not award a precise full-screen score from screenshots; if asked for a score, follows `review-calibration.md` evidence ranges.

## Paywall: plan cards at large text

**Prompt:** Review this Flutter paywall. Give prioritized findings and the smallest fixes. Do not implement changes.

**Input:** Three plans (Monthly, Annual, Lifetime) render as a `Row` of three `Expanded` cards. Each card shows "$4.16/mo" in `displaySmall` and the billed "$49.99 billed yearly" in grey `labelSmall`. Each card carries two badges, "BEST VALUE" and "SAVE 58%". Selection is a `GestureDetector` that changes the border color. Restore purchases sits at the end of the scrolling feature list. At text scale 2.0 the prices wrap to three lines and the badges clip at the card's top edge. No screenshots of loading or failure states; no store-connection evidence.

**Acceptance:**
- Makes the billed amount the strongest price and the per-month equivalent secondary, citing the `mobile-ux` billed-price rule.
- Replaces the three-across row with stacked cards, or a stacked fallback chosen by width and text scale, before any price wraps; reserves space so the badge cannot clip; one fact per badge.
- Replaces the bare `GestureDetector` with radio-group selection so selection state reaches screen readers and keyboard users.
- Moves Restore purchases, with Terms and Privacy, under the purchase button where it is visible without scrolling.
- Lists price-loading, store-unavailable, pending, and failed states as missing or unverified; claims none of them.

## Code field: paste and autofill

**Prompt:** Review this Flutter verification-code widget. Give prioritized findings, minimal fixes, and focused checks. Do not implement changes.

**Input:** Six `TextField`s, one per digit, each with its own `FocusNode`; `onChanged` moves focus to the next node. No `autofillHints`. A "Verify" button submits. The countdown is an `int` decremented by `Timer.periodic`, rendered in `bodyMedium` with proportional figures. Testers report: pasting a code fills only the first box, iOS does not suggest the code from Messages, the countdown resumes from where it paused after backgrounding, and TalkBack reads "edit box" six times. No device recordings are supplied.

**Acceptance:**
- Replaces the six fields with one `TextField` (digits-only and length formatters, `AutofillHints.oneTimeCode`) under drawn boxes, which fixes paste and SMS autofill on both platforms.
- Excludes the boxes from semantics and labels the single field ("Verification code, 6 digits").
- Computes the countdown from a stored deadline, renders tabular figures, and shows Resend at zero.
- Submits on the final digit per the `mobile-ux` contract instead of requiring Verify.
- Names focused widget tests (pasting a full code, the autofill hint, the semantics label) and leaves on-device autofill unverified.

## Status view and undo snackbar

**Prompt:** Review the empty, error, and undo handling in this Flutter inbox tab. Give prioritized findings and minimal fixes. Do not implement changes.

**Input:** A `StatefulShellRoute` shell `Scaffold` draws a floating pill navigation bar in a `Stack`, not in `bottomNavigationBar`; each tab has its own inner `Scaffold`. Empty inbox: a 200 px stock illustration, a title, two paragraphs, and two full-width buttons. The project has no illustration set. A load error pushes a full-screen error page with "OK". Archiving shows a fixed `SnackBar` reading "Done" with an Undo action; testers say it sits behind the pill bar. Only this written description is supplied.

**Acceptance:**
- Introduces one shared Status View for empty and error: icon at most 48 logical px or none, title, short body, one compact action; drops the stock illustration and the second button.
- Renders the load error in the tab's content region instead of pushing a page, keeping navigation usable.
- Explains that the snackbar shows on the outer shell `Scaffold` and, because the bar lives in a `Stack`, needs `SnackBarBehavior.floating` with a bottom margin that clears the bar; rewrites the message to name the object and action ("Message archived").
- Accepts the centered status layout instead of flagging it as a generic tell.
- Leaves rendered appearance and screen-reader announcements unverified.

## Question screen: advance and disabled state

**Prompt:** Review this build plan for two Flutter onboarding question screens and recommend their structure. Do not implement changes.

**Input:** Screen 1 asks "How often do you cook?" with four single-choice answers that tailor recipe suggestions. Screen 2 asks "Which cuisines do you like?" with 12 options, multi-select. The draft renders options as a `Column` of `ElevatedButton`s, keeps Continue always enabled (tapping it with nothing selected shows an error snackbar), and has no progress indicator and no Skip. The `mobile-ux` contract says: both questions are optional; single choice advances on tap; multi-select enables Continue at the first pick.

**Acceptance:**
- Adds progress in the app bar with semantics ("Question 1 of 2") and Skip as a trailing text button on both screens.
- Uses full-width selectable rows at least 56 dp tall, with radio semantics on screen 1 and checkbox semantics on screen 2; selected state carries a border plus radio or check, not color alone.
- Screen 1 advances on tap with no Continue; screen 2's Continue stays disabled until the first pick, citing the Form Screen exception rather than the general never-disable rule.
- Lists back (previous answer preserved), scaled text, and RTL as states to check, marked unverified.
