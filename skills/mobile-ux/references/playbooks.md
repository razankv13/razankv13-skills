# Flow Playbooks

Per-flow defaults with verification steps. Apply patterns when the product and evidence support them; retain accessibility, truthful feedback, and data-preservation requirements. Each playbook ends with "Verify by": run applicable checks or mark them Not verified with the missing evidence. Numeric budgets here (destination count, depth, tap counts, list length) are heuristics for spotting friction, not measured thresholds: a flow over budget earns a finding only when the extra steps cause observed or likely harm.

More playbooks — sign-in/recovery, session expiry, lists/feeds, app updates, rating prompts, platform consent prompts, screen size/rotation, localization, assistive tech, experiments — live in `playbooks-lifecycle.md`.

## Contents

- Onboarding / First Run
- Lifecycle-Adaptive Home
- Empty States
- Search
- Errors and Recovery
- Forms and Input
- Decision & Commit Screens
- Post-Action Status & Tracking
- Permissions
- Interruption and Resume
- Navigation & Information Architecture
- Platform Back Behavior & Entry Points
- Notifications & Re-Engagement
- Paywall & Upgrade Placement
- Settings & Account
- Data Export, Deletion & Leaving
- Sharing & Invites
- Multi-Device & Sync

## Onboarding / First Run

- Define the "aha" action (first real value) and count steps to it. Remove unnecessary steps before value; measure abandonment rather than assuming every step causes it.
- Let users experience value before requiring an account when the service permits it. Offer creation only when useful and preservable; require identity earlier when necessary and explain why. Frame signup as saving progress only when that is what it does.
- Show progress based on actual completed steps; start at 0% when none are complete.
- Ask only for data you use immediately. Everything else can wait for context.
- Onboarding content retires. Once the user has completed the aha action a few times, the entry screen stops showing setup prompts and hands over to the stage-appropriate home (see Lifecycle-Adaptive Home).
- Verify by: counting taps/screens from launch to first value; checking that signup follows value or has an explained service need to come earlier; checking new and experienced user states — completed prompts retire, and any different content serves a demonstrated need.

## Lifecycle-Adaptive Home

Adapt home when users at different stages have different demonstrated needs. A stable home is appropriate when their core task stays the same.

- Add only the variants the product needs. Possible examples: new users see one setup action; returning users see today's task; expert users see relevant status. Use existing usage signals when adapting; do not add tracking or a stage model solely to satisfy this pattern.
- Personalized content only where the data exists — never fabricate "your stats" for a user who has none; show the path to earning them instead.
- Stage transitions are silent; the user never sees a "you are now a power user" moment or loses a control they relied on.
- Verify by: checking each supported user state — completed onboarding stays retired, primary actions remain findable, and any adaptation addresses a stated need. A shared home can pass.

## Empty States

- An empty state is onboarding, not a void. It must state what belongs here, why it's useful, and offer the single next action to fill it.
- Never show a bare "no items" message with no path forward.
- Where honest, seed with a sample or template so the first interaction is "edit" rather than "create from nothing."
- Verify by: every list/collection screen has a designed empty case with exactly one primary action.

## Search

Tapping search is a moment of intent, often without a precise query. A blank screen puts all the work on the user.

- Offer useful pre-query guidance: removable recents, browse, or suggestions when appropriate data exists. For private or exact-identifier search, a clear input hint can be enough; do not expose sensitive history or invent popularity.
- Suggestions are passive: a user who knows what they want types and ignores them; an unsure user gets a starting point.
- No-results state offers recovery — spelling alternatives, broader category, clear filters — not a dead end.
- Preserve query and filters through back, error, and resume when appropriate; sensitive searches follow the product's privacy and retention rules.
- Verify by: opening search with empty history, with history, and with a zero-hit query; none is blank and each has a next action.

## Errors and Recovery

- Every error message answers: what happened, why (if knowable), what the user can do now. Blame the system, not the user.
- Preserve user input across every failure.
- Offer retry in place; never dead-end into a screen the user must back out of.
- Offline: distinguish "no connection" from "server error"; queue reversible actions locally and sync when possible; show what is stale vs. current.
- Duplicate submits: a submit is idempotent or blocked while pending; a second tap never creates a second order, message, or charge.
- Timeouts: the outcome is unknown, not failed. Reconcile (check server state) before offering retry, and say "checking" rather than "failed".
- Partial success: in a batch (upload 10 photos, send to 5 people) report which items succeeded, keep them, and retry only the failed ones.
- Payment failure: plain-language reason when the processor gives one, retry with the same or another method, order/cart intact, no charge stated until confirmed.
- Verify by: walking each flow with network disabled, forced server errors, and a forced timeout after the server accepted the request; double-tapping every submit; confirming no input loss, no duplicate, and a recovery path at each failure point.

## Forms and Input

- Every field must justify its existence. Treat the effect of extra fields as a hypothesis; split long forms when grouping helps completion and preserves context.
- Pre-fill everything pre-fillable (defaults, device data, prior answers).
- Match the input control to range, precision, frequency, and accessibility. A wheel or slider can suit a small bounded range; a field or stepper can suit precise or repeated entry. One-time use alone does not make a text field a defect; compare the actual effort needed.
- Where real usage clusters on a few values, offer those as one-tap presets ahead of the free-form control; keep the free-form control for the rest.
- CTA copy states the outcome when it is known ("Search 12 available tables", "Add to cart · $3.10"), not the verb alone.
- Validate inline as the user completes each field, not as a wall of errors on submit. Never clear the form on validation failure.
- Support autofill and keep the submit action reachable while the keyboard is up.
- Verify by: counting required fields (challenge each); submitting with each field invalid and confirming input survives; for each numeric control, naming its entry frequency and precision and checking the control matches.

## Decision & Commit Screens

Product detail, plan selection, booking summary — any screen where the user decides, then commits.

- Information order follows the decision. For a simple purchase, title → relevant trust evidence → cost → action is a useful starting point. Show price and terms before commitment; complex or regulated decisions may need explanation first. Do not invent ratings or require them for products without reviews.
- The commit control shows the total the user is about to pay or commit to ("Add to cart · $6.20"). Choosing quantity/variant and committing happen in one uninterrupted sequence, with the unit stated in the choice ("0.5 kg"); where they sit is the UI skill's call.
- A static title never carries a value a control can change. "Strawberries 1 kg" next to a quantity selector is misleading after the first tap; the title describes the product, the variable lives in the buying area.
- If the decision commonly happens after reading details or browsing related items, the user can commit and still see what they are committing to at any point in the screen, without scrolling back; how that is achieved is the UI skill's call.
- Offer the common choices as one-tap presets derived from real purchase data (with their price each); keep custom entry.
- Remove redundant labels only when meaning stays unambiguous. Keep labels needed to distinguish unit price, total, fees, or quantities, including for screen-reader users.
- Verify by: checking that cost and terms are clear before commitment (zero scrolls is a useful goal for simple purchases); tapping commit — the amount charged matches the accepted total; changing quantity — the title is still true.

## Post-Action Status & Tracking

Order, booking, application, upload, delivery — any state between commit and fulfillment. The user has already paid or decided and now waits in uncertainty; the screen's job is to answer questions before they are asked.

- Prioritize current state, then relevant timing, location, and help. Use a window when timing is uncertain and a timestamp when precision matters. Show a named contact only when one exists and is appropriate; explain unknown timing instead of inventing it.
- Use a step timeline when the process has meaningful stages; use an event log when exact history matters. Summarize items only when full details are not needed for the current task, and keep those details accessible.
- The primary action fits the stage (track, reschedule, contact); cancel/change stays findable but secondary.
- Push updates fire only on state change and land on this screen.
- Verify by: checking that users can identify the current state, relevant timing, and available help; mark unavailable details honestly. Kill and reopen mid-wait — restore the operation and reconcile its current state.

## Permissions

- Never ask on launch. Ask in the moment the feature needs it, after the user initiated the related action ("contextual ask").
- Pre-frame with your own explanation of value before triggering the OS dialog — a declined OS prompt is expensive to reverse.
- Handle denial gracefully: the app must remain usable, with a path to re-enable when the user later wants the feature.
- Verify by: mapping each permission to the user action that triggers it; confirming app behavior when every permission is denied.

## Interruption and Resume

- Assume every session ends mid-task. Persist state continuously; restore users to where they were, not to the home screen.
- Long operations survive backgrounding; report completion when the user returns.
- Verify by: killing the app mid-flow and relaunching — the flow resumes or offers to.

## Navigation & Information Architecture

- Structure follows user tasks, not org chart or database schema. Group by what users do, not what the system stores.
- Primary destinations: 3–5. More means the model is unclear — consolidate.
- Every screen has an obvious way back and an obvious primary action. If you can't name a screen's single main job, split or cut it.
- Depth budget: core tasks reachable within ~3 levels. Frequent actions must not live behind rare ones.
- Gestures are accelerators, never the only path — every gesture-invokable action needs a discoverable visible equivalent.
- Reachability: name which actions are frequent so the UI skill can place them within one-handed reach; rare actions may cost an extra step, frequent ones may not.
- Preserve navigation state per section (switching tabs and returning keeps your place).
- Verify by: asking of any item "would a first-time user predict it lives here?"; tracing back-behavior from every screen.

## Platform Back Behavior & Entry Points

Back is platform behavior, not a button you draw. Flows that ignore it break on real devices.

Android:

- System back (button, gesture, predictive back) must always map to the app's logical back — same destination as the visible back affordance.
- Back from the root screen exits the app; never trap the user in a loop or re-show the same screen.
- Back never silently discards typed input. Unsaved-work screens intercept back with save/discard, or auto-save as draft.
- Back closes transient surfaces first (sheet, dialog, search overlay), then navigates.

iOS:

- Edge swipe-back is the primary back path; don't block the leading screen edge with drawers, carousels, or full-width horizontal gestures on push-navigation screens.
- Sheets dismiss by swipe-down; if a sheet holds unsaved input, guard dismissal (confirmation or draft), don't just lose it.
- Tapping the active tab again conventionally pops that tab's stack to root — keep it.

Entry without a back stack (applies to both platforms):

- Deep links, notification taps, home-screen widgets, and app shortcuts land the user mid-hierarchy. Synthesize a sensible back/up path to the logical parent — back must never exit the app from three levels deep or land on a blank screen.
- If the linked content is gone (deleted, expired, no permission), show an explanatory fallback with a route into the app — never a raw error or empty screen.
- Verify by: pressing system back on every screen and after every deep-link/notification entry; opening a link to deleted content; starting input, pressing back, relaunching — input survives or user chose to discard.

## Notifications & Re-Engagement

A notification is an interruption spent from a limited trust budget. Every send must serve the user's stated interest, not the business's impatience.

- One job per notification: it announces one thing and taps through to exactly that thing — never the home screen.
- Ask for notification permission in context, after the user has done the thing notifications extend (placed an order, followed a topic). On iOS consider provisional authorization (quiet delivery) before asking for full alerts.
- Give per-category controls in-app (orders vs. marketing vs. social), not one master switch. Honor them absolutely.
- Batch low-priority events; respect quiet hours; never notify to announce features the user didn't ask about.
- Re-engagement triggers on value events in the user's world (their content received activity, a thing they track changed, a real deadline nears) — not on mere absence. "We miss you" carries no user-side news, so it is likely to read as noise.
- Every re-engagement notification passes the test: would the user thank you for the interruption? If not, don't send.
- Verify by: tapping every notification type — lands on the exact content with a working back path; disabling each category and confirming zero sends from it; checking each template names a concrete user-side event.

## Paywall & Upgrade Placement

- Place the paywall after a value moment (the first completed "aha" action from Onboarding; finishing setup is not one), at a natural task boundary — never mid-task, and never as the first screen a new user meets (unless the product is paid-only and says so honestly).
- Anchor with the user's own investment: show the things they built, saved, or tracked (contrast + IKEA effect with real data, not marketing claims).
- State free-tier limits up front at the start of use; hitting an undisclosed limit mid-task reads as a bait-and-switch.
- The decline path is visible from the first frame (no delayed close button), one tap, and neutrally worded.
- One ask per success moment: a paywall, rating prompt, or permission request each gets its own boundary, never stacked on the same completion. After decline, the app keeps every promise the free tier made.
- Trial mechanics are honest: end date shown at start, reminder before charging, cancel path per the Hard rules in `SKILL.md`.
- Before purchase, state what the user gets, price, billing period, and renewal terms, and how to cancel (App Store Review Guideline 3.1.2; Google Play Subscriptions policy). The price the user will be charged is the most prominent price.
- Verify by: tracing where the paywall appears relative to the value moment; declining and confirming full free-tier function; counting cancel steps vs. subscribe steps.

## Settings & Account

Settings are where users go to fix something that annoyed them; the screen must let them find it fast and trust the change took.

- Group by user intent (notifications, privacy, appearance, account), not by subsystem. Frequent toggles surface at the top; rarely used ones nest. Search once the list exceeds about 20 items.
- Every toggle applies immediately and shows its effect; no separate "Save" for independent switches. Grouped forms (address, profile) save explicitly with dirty-state guard on back.
- Each setting carries its consequence in one line ("Off: order updates arrive by email only"). Destructive or account-wide changes (email, password, plan) confirm identity and confirm the change by the old and new channel.
- Sign-out never silently drops unsynced work; it says what is unsynced or syncs first.
- Verify by: for 5 common complaints ("stop these notifications", "change my email", "delete my account"), count taps from home; each under 4 and findable by search.

## Data Export, Deletion & Leaving

A user leaving must be able to take their data and be sure the deletion happened. This is also a legal and store-policy surface: GDPR, CCPA; App Store Review Guideline 5.1.1(v) (apps with account creation must offer in-app deletion); Google Play's account deletion policy (an in-app path plus a web link that works without reinstalling).

- Export: one action, common format (CSV/JSON/PDF as fits), delivered in-app or by link; progress and completion visible; works for the free tier.
- Delete account: reachable from settings in ≤3 taps, no support ticket, no dark-pattern retention flow. One honest screen states what is deleted, what is retained (legal/billing), and when. Confirm with re-auth, not with typed phrases.
- Grace period allowed only if stated with its end date and a way to cancel deletion; after it, confirmation by the user's channel.
- Cancel subscription sits beside delete account (step count per the Hard rules in `SKILL.md`).
- Verify by: running export and delete on a test account end to end; checking the data is gone from every user-visible surface and the confirmation arrived.

## Sharing & Invites

- Share the thing, not the app: the share target receives the exact content with a link that opens it (or a graceful web fallback), never a store page.
- Use the platform share sheet; own the preview (title, image) and the deep-link landing (see Platform Back Behavior & Entry Points).
- Invite flows show the recipient's state after sending (pending, joined) and never re-invite silently. Contact access is asked in context, with a manual-entry path when declined.
- Recipient side: the invited content is reachable before signup where the service allows; signup framed as "join" to what they were shown.
- Verify by: sharing from every shareable surface and opening each link on a device without the app installed and with it installed; both land on the content.

## Multi-Device & Sync

- State follows the account: a change on one device is visible on another within the sync interval, and the interval is stated where it matters (drafts, progress).
- Conflicts resolve without data loss: last-writer-wins only for trivially reversible fields; for content, keep both versions and let the user choose.
- Offline edits queue with a visible pending marker and sync on reconnect; sync errors surface once, with retry, never as a modal on every launch.
- New-device sign-in restores the user to their state, not to onboarding.
- Verify by: editing the same item on two devices offline, reconnecting both, and confirming neither edit is lost; signing in on a fresh device and checking onboarding is skipped.
