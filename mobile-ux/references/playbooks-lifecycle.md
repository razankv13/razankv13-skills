# Lifecycle, Identity & Platform Playbooks

Second playbook file: same format and rules as `playbooks.md` (patterns apply when product and evidence support them; each ends with "Verify by"). Platform and store-policy facts name their version or guideline; recheck them when a store updates its rules.

## Contents

- Sign-Up, Sign-In & Account Recovery
- Session Expiry & Re-Authentication
- Lists, Feeds & Refresh
- App Updates & Migration
- Rating & Review Prompts
- Platform Consent Prompts (Notifications, Tracking)
- Screen Size, Rotation & Multi-Window
- Localization Behavior
- Assistive-Technology Behavior
- Validating a Change (Experiments & Rollout)

## Sign-Up, Sign-In & Account Recovery

- One primary path matched to the audience: passkey, email magic link / one-time code, or platform sign-in. Passwords stay available where users expect them; offer autofill and password-manager support either way.
- iOS: an app offering third-party or social login must also offer an equivalent privacy-focused login option (App Store Review Guideline 4.8). Sign in with Apple satisfies it. 4.8 does not apply to apps using only their own account system, education/enterprise apps using existing org accounts, government or industry ID systems, or clients for a specific third-party service.
- Sign-up and sign-in share one entry ("Continue with email"); the app decides which applies after the identifier, so users never pick the wrong door.
- Guest work carries over: anything created before sign-up merges into the new account; an identifier that already exists signs in and merges, never errors out and discards.
- Recovery is self-serve from the sign-in screen, states where the code/link went, allows resend after a stated wait, and returns the user to the task they started.
- Managed accounts (workforce, school, enterprise SSO): no self sign-up; the organization provisions accounts, and recovery routes to the admin or identity provider when self-serve reset isn't allowed. Say so on the sign-in screen instead of offering a reset that will fail.
- Shared devices: fast user switching from the sign-in entry; each user sees only their own data; signing out or switching never discards another user's queued offline work.
- Offline sign-in: when policy allows, a returning user unlocks cached credentials with a local PIN or biometric and works offline; first sign-in and password reset need connectivity, and the screen states that plainly with an offline fallback (retry when online, ask an admin).
- Verify by: signing up after creating guest content (content survives); entering an existing account's identifier on sign-up (lands signed in); running recovery end to end without support; on shared or offline devices, switching users with queued work and signing in with no connection.

## Session Expiry & Re-Authentication

- Expiry never costs work: re-authenticate in place (sheet or biometric) and resume the pending action; keep input and unsent changes across the round trip.
- Refresh silently while a refresh token is valid; ask the user only when credentials are genuinely needed, and say why ("Sign in again to finish paying").
- Step-up auth (biometric/passcode) guards sensitive actions (payment, email change), not browsing.
- Verify by: expiring the session mid-form, submitting, re-authenticating — the submission completes with input intact and without a duplicate.

## Lists, Feeds & Refresh

- First load shows structure immediately; next pages load before the user reaches the end; a failed page load shows retry at the end of the list with loaded items intact.
- Pull-to-refresh (or an equivalent control) on user-refreshable lists; new items arriving while the user reads appear behind a "N new" affordance instead of shifting the content under their finger.
- Returning to a list (back, tab switch, resume) restores scroll position and filters.
- Stale content is labeled with its age when freshness matters (prices, availability, messages).
- Removed or hidden items leave with undo when the action was the user's.
- Verify by: scrolling past several pages on a slow connection; forcing one page to fail; opening an item and pressing back (same position); receiving new items mid-read (no jump).

## App Updates & Migration

- Force-update only when the old version is broken or unsafe (API removed, security fix); otherwise offer a dismissible update prompt. Android: Play In-App Updates API (immediate vs flexible flow). iOS: no system API; compare against a server-provided minimum version and link to the App Store page.
- The force-update screen states why and has one action; it never appears mid-task when the old version can still finish the task.
- Data migration on first launch after update is invisible or shows progress; it never re-runs onboarding or signs the user out.
- "What's new" appears only for changes that alter a task the user already does, at a task boundary, once.
- Verify by: installing the previous version with real data, updating, launching — same account, same data, same place, no onboarding.

## Rating & Review Prompts

- Use the platform API only: iOS StoreKit `AppStore.requestReview(in:)` or SwiftUI's `requestReview` action (`SKStoreReviewController` is deprecated as of iOS 18; custom review prompts are disallowed, App Store Review Guideline 5.6.1); Android Play In-App Review API. Both are quota-limited by the OS and may show nothing, so no flow may depend on the prompt appearing.
- Trigger after a completed success moment (order delivered, task finished), never mid-task, never on first launch, never after an error, and never on the same moment as a paywall or other ask.
- No gating question before it: Google's In-App Review API guidelines forbid asking "Do you like the app?" or similar before the review card; route feedback through a separate, always-available "Send feedback" path instead.
- Verify by: listing every trigger point and confirming each follows a completed success; confirming no code path waits on the prompt's result.

## Platform Consent Prompts (Notifications, Tracking)

Extends Permissions and Notifications in `playbooks.md`.

- Android 13+ (API 33): posting notifications needs the `POST_NOTIFICATIONS` runtime permission. Request it in context. Apps targeting API 32 or lower get the system prompt automatically when they create their first notification channel, so target 33+ to control the timing. On Android 8+ users can also disable notifications per app and per channel in system settings: map each in-app category to its own `NotificationChannel` so the two sets of controls match.
- iOS 12+: provisional authorization delivers quietly to Notification Center without a prompt; ask for full alerts once the user has seen value from them.
- iOS 14.5+: tracking across other companies' apps and sites needs App Tracking Transparency consent (App Store Review Guideline 5.1.2(i)). A pre-prompt may explain the benefit but must not offer incentives or imitate the system alert (Apple, "User Privacy and Data Use"); the app works identically after "Ask App Not to Track".
- A denied OS prompt usually cannot be re-shown; the path back is a settings deep link offered when the user next tries the feature.
- Verify by: denying each consent on a fresh install and confirming the feature degrades gracefully and the re-enable path exists.

## Screen Size, Rotation & Multi-Window

Behavior only: which layout each size gets is the UI skill's call.

- Rotation, fold/unfold, split-screen, and window resize keep the user's place: input, scroll position, selection, open sheet, and playback survive. (Android recreates the activity on configuration change by default; state must be saved explicitly.)
- A task started on one posture completes on the other; no size class hides an action another size class offers.
- Verify by: rotating and resizing mid-form and mid-scroll on each supported device class; input and position survive.

## Localization Behavior

- Names, addresses, phone numbers, dates, currencies, and units follow the user's locale; forms accept local formats (no forced first/last name split, no US-only postal validation).
- RTL locales mirror directional navigation: back points and swipes the other way, progress runs right to left; media timelines and phone numbers stay LTR.
- Copy that changes length never truncates the meaning of a CTA or an error; plurals use the locale's plural rules, not "item(s)".
- Verify by: running the flow with an RTL locale and one long-text locale (e.g. German); entering a non-Western name and address.

## Assistive-Technology Behavior

Extends the accessibility items in the audit checklist; the UI skill owns semantics and labels in code.

- Screen-reader focus order follows the task order; after navigation, focus lands on the new screen's title or first meaningful element.
- Async results are announced: validation errors, "Added to cart", status changes, and loading completion reach VoiceOver/TalkBack without the user hunting for them.
- Time limits (OTP expiry, reservation holds) can be extended or are long enough to complete with assistive tech.
- Verify by: completing the flow with VoiceOver and TalkBack only, including one error and one async success.

## Validating a Change (Experiments & Rollout)

- Ship behavior changes behind a flag or staged rollout when traffic allows; compare the primary metric and guardrail from `flow-design.md` against the control, not against last month.
- Decide the sample size and duration before starting; stop early only for guardrail harm.
- Low traffic: use a before/after comparison with the same window length and state its weakness, or run moderated usability sessions (about 5 users per segment is the common heuristic for surfacing major blockers, per Nielsen Norman Group) and report findings as qualitative.
- Verify by: the deliverable names the flag or rollout, the comparison group, the window, and the decision rule.
