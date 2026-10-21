# Screen Patterns

Flutter structure for recurring mobile screens. Each pattern names the widget skeleton, the states to cover, and the rule it exists to serve. Flow behavior (what the screen must answer, in what order) is set by `mobile-ux` playbooks; this file implements it. Token names below are roles — map them to the project's own tokens from the census. Target sizes and scaled-text checks use `craft-rubric.md` Accessibility Thresholds.

Skeletons are conditional examples: use only the parts the flow needs, reuse project components, and preserve accessibility and recovery. Do not add a hero, sticky bar, stage enum, or image grid solely to match this reference.

`(Mobbin: …)` names the shipping apps where a pattern was observed (links in `mobbin-evidence.md`). Mobbin's catalog is iOS; the Flutter skeletons serve both platforms.

## Contents

- Detail-Then-Commit Screen
- Status Timeline
- Paywall Plan Picker
- Verification Code Field
- Status View (Empty, Error, Offline)
- Choice Question Screen
- Search Surface
- Stage-Adaptive Home
- Category And Collection Tiles
- Controls Over Imagery
- Numeric Input Choice
- Lists And Data Density
- Form Screen
- Bottom Sheet
- Navigation Shell

## Detail-Then-Commit Screen

Product detail, plan selection, booking summary. For simple purchases, what → relevant trust evidence → price → action is a useful order. Show cost and terms before commitment; keep the control reachable when scrolling is part of the decision. Complex decisions may need explanation first.

Skeleton:

- `Scaffold` body is a `CustomScrollView`. A hero can use a pinned `SliverAppBar` with `FlexibleSpaceBar`. Its title is visible while expanded by default; if the brief requires a collapsed-only title, tie its opacity to collapse progress through a `SliverPersistentHeader` delegate or the existing scroll pattern.
- Content uses slivers or a `SliverToBoxAdapter`. If the design calls for overlap, use `Transform.translate` and account for its unchanged layout footprint and hit bounds; Flutter margins are non-negative. Example content order: title → genuine rating → unit price → description → options → related items. Omit sections absent from the product.
- Sticky action bar in `Scaffold.bottomNavigationBar` (or `persistentFooterButtons`), wrapped in `SafeArea` and padded by `MediaQuery.viewInsetsOf(context).bottom` when a text field can open a keyboard. Contents: quantity stepper with the unit inside its label ("0.5 kg") and a `FilledButton` whose child is the outcome text ("Add to cart · $3.10"). Stepper and button share one row; the button takes the remaining width.
- Preset chips above the stepper: `ChoiceChip` (or `SegmentedButton` when ≤4 fixed options) with a label, sub-label, and per-option price stacked in the chip. Selecting a preset updates the stepper and the button total; editing the stepper deselects the preset.
- Title text never carries a mutable value; the quantity is only in the stepper/chip.

States: loading price (button disabled, total skeleton), unavailable / out of stock (button label explains, presets disabled), quantity at min/max (stepper arrow disabled with a semantic hint), commit pending (button shows progress, stays the same size), commit failed (inline message, input preserved). Scaled text: the button label may wrap to two lines; the bar grows, it does not clip.

## Status Timeline

Order, booking, application, upload. Rule: show the current stage and the timing, location, or contact information relevant to this process. Use steps for a staged process; an event log can better serve detailed history. Omit inapplicable sections.

Skeleton:

- Header block: icon or thumbnail + headline (`titleLarge`), two icon-led rows (time window, address). Then one primary action (`FilledButton`, e.g. Track) and one or two secondary (`OutlinedButton`, e.g. Reschedule; overflow in a `MenuAnchor`).
- Contact row: `CircleAvatar` (photo or initials fallback), name, role; trailing `IconButton`s for call and message, each meeting the platform target-size threshold with `Semantics(label:)` and `tooltip`.
- Items: use thumbnails with a "+N" tile when recognition helps; tap opens the full list. Use readable text rows when names, quantities, or exact comparison matter.
- Timeline: a `Column` of step rows. Each row = leading indicator column (dot + connector line via a small `CustomPainter` or a `Container` with a `border`) + label + date/time. Token roles: done → `primary` dot, check icon, connector filled; current → `primary` dot with outer ring; upcoming → `outlineVariant` dot, dashed or faded connector, `onSurfaceVariant` text.
- The current step animates once on first build (scale or ring fade, ease-out, ≤300 ms) and never on rebuild; skip when `MediaQuery.disableAnimationsOf(context)` is true.
- `Semantics` on each step: "Out for delivery, step 2 of 3, current". Group the header with `MergeSemantics` so a screen reader reads it as one sentence.

States: no courier assigned yet (contact row shows "Assigning" placeholder, not empty space), delayed (headline changes and the window updates; do not just add a red badge), failed/cancelled (headline + recovery action), offline (last known state with a stale marker). Push notification for a state change deep-links here with a synthesized back stack.

## Paywall Plan Picker

Rule (from `mobile-ux` Paywall & Upgrade Placement): the billed price is the most prominent price; decline, restore, and terms are visible on the first frame.

Skeleton:

- Plans are one `RadioGroup<Plan>` of selectable cards, so selection state reaches screen readers. Selected card: 2 px `primary` border plus a filled radio or check; unselected: 1 px `outlineVariant` (Mobbin: Bevel, Ahead, Public, Givingli, Lifesum, Stardust).
- Each card: plan name (`titleMedium`), billed amount with its period ("$89.98 / year", tabular figures) as the strongest price, and the per-period equivalent ("$7.50 / month") in `bodySmall` `onSurfaceVariant` (Mobbin: Bevel, Lifesum, Copilot). Stardust and Givingli lead with the monthly equivalent; that breaks the billed-price rule, so keep the billed amount strongest.
- Badge: one fact ("Save 37%", "Most popular", "7 days free") in `labelSmall` on a small container overlapping the card's top edge (Mobbin: Bevel, Ahead, Lifesum, Stardust, Copilot). Overlap with a `Stack` (`clipBehavior: Clip.none`) and reserve top padding in the list so the badge never clips.
- Layout: stacked full-width cards by default (Mobbin: Ahead, Joi, Public, Givingli). Side by side only for 2–3 plans whose name and price fit their column on one line each (Mobbin: Bevel, Stardust, Lifesum); decide with `LayoutBuilder` and `MediaQuery.textScalerOf`, falling back to stacked before any price wraps.
- Purchase button pinned in `Scaffold.bottomNavigationBar` inside `SafeArea`; the plan list scrolls. Directly under it, one row of `TextButton`s: Restore purchases, Terms, Privacy (Mobbin: Joi, Lifesum, Copilot, Stardust).
- Trial timeline: the Status Timeline skeleton above with three steps — today, reminder, charge (date and amount) (Mobbin: pillowtalk, Copilot, Stardust).

States: prices loading (card skeletons at final size; purchase button disabled and labeled "Loading prices"), store unavailable (inline error with retry; close still works), purchase pending (button progress at the same size, cards locked), purchase cancelled or failed (inline message, selection kept), restore finished (snackbar "Purchases restored" or "No purchases to restore"), scaled text (stacked layout, cards grow, button label wraps).

## Verification Code Field

Rule (from `mobile-ux` Sign-Up, Sign-In & Account Recovery, Code entry): keypad up on open, submit on the last digit, countdown then Resend.

Skeleton:

- One `TextField` owns the code: `autofocus: true`, `keyboardType: TextInputType.number`, `autofillHints: const [AutofillHints.oneTimeCode]` (iOS `oneTimeCode`, Android `AUTOFILL_HINT_SMS_OTP`), `inputFormatters` = `FilteringTextInputFormatter.digitsOnly` + `LengthLimitingTextInputFormatter(length)`, and `onChanged` submits when the text reaches `length`. With a single field, paste and SMS autofill fill every digit at once.
- Boxes, one per digit, render the controller's text, and the box at the next empty index is highlighted (Mobbin: Vivid, Shelf, Bolt, Zomato). Stack them under the field and make the field's own text, cursor, and border transparent; the field still covers the boxes, so taps focus it and long-press offers Paste. Highlight with the `primary` border.
- Use one plain field instead of boxes when the code length varies by channel (Mobbin: GoPay).
- Semantics: wrap the boxes in `ExcludeSemantics`; the field carries the label ("Verification code, 6 digits"), so a screen reader reads one field instead of six boxes.
- Countdown directly under the boxes (Mobbin: Shelf, Bolt, Zomato) in `bodyMedium` with `FontFeature.tabularFigures()` so the digits hold still. Compute it from a stored deadline, so backgrounding cannot freeze it; at zero it becomes a "Resend code" `TextButton`.

States: verifying (field read-only, inline progress), wrong or expired code (error text under the boxes, announced as a live region, focus kept in the field), resent (countdown restarts; a snackbar names where the new code went), scaled text (boxes keep a fixed square size; the row scrolls horizontally or the boxes shrink to their minimum target before any digit clips).

## Status View (Empty, Error, Offline)

Rule (from `mobile-ux` Empty States and Errors and Recovery): one message, one next action, and only the failed region is replaced.

Skeleton:

- One shared widget, such as `StatusView(icon:, title:, body:, action:)`, renders every empty, error, and offline state (Mobbin: komoot, timespent, Bluesky Social, Lex, Zomato, Cherrypick, Origin, Fresha, Noom, HYPE). Reuse the project's equivalent when it has one.
- It fills the region whose data is missing — a list body, a sheet, a tab's content — and leaves the `Scaffold`'s app bar, navigation bar, and filters in place. Center a `mainAxisSize: MainAxisSize.min` `Column` in that region; center alignment is correct for status views.
- Icon at most 48 logical px in `onSurfaceVariant` (optionally on a tinted circle), or no icon (Mobbin: timespent, Bluesky Social, Lex, Zomato, Origin; no icon: komoot, Nike Run Club, Cherrypick). A large illustration suits only a project that already owns an illustration set (Mobbin: Starbucks, Lloyds Mobile Banking).
- Title `titleMedium`; body `bodyMedium` `onSurfaceVariant`, two lines at 1.0 text scale; action a compact `FilledButton.tonal` (or `FilledButton`) sized to its label, directly under the body (Mobbin: komoot, timespent, Bluesky Social, Lex, Cherrypick, Origin, Fresha, Noom, HYPE). Nike Run Club pins a full-width button at the bottom instead; match that only when the project already places primary buttons there.
- Errors that replace content after load use `Semantics(liveRegion: true)` on the title so screen readers announce them.

States: scaled text (body wraps past two lines; the view scrolls inside its region instead of clipping), retry pending (the action shows progress at the same size), RTL (centered layout needs no mirroring; directional icons still mirror).

## Choice Question Screen

Onboarding questions, surveys, intake. Rule (from `mobile-ux` Onboarding and Forms and Input, choice screens): one question per screen; `mobile-ux` decides Skip and whether the screen advances on tap or on Continue.

Skeleton:

- App bar: back as leading, progress as the title area — a `LinearProgressIndicator` with `semanticsLabel` "Question 2 of 5", or "2 / 5" text (Mobbin: Life Reset, Nibble, UNIQLO, pliability, LinkedIn) — and Skip as a trailing `TextButton` when the question is optional (Mobbin: UNIQLO, pliability, LinkedIn). Progress counts answered questions.
- Question in `headlineSmall`, left-aligned; the why-line under it in `bodyMedium` `onSurfaceVariant`.
- Options are full-width rows at least 56 dp tall (Mobbin: Neo Financial, Ahead, LinkedIn, Fresha, pliability, Insight Timer). Selected: 2 px `primary` border plus a trailing radio or check; unselected: `surfaceContainer` fill or 1 px `outlineVariant`. Single choice uses a `RadioGroup` of radio rows; multi-select uses checkbox rows, so the selection state reaches screen readers.
- Continue, when the question has one, is pinned in `Scaffold.bottomNavigationBar` inside `SafeArea`; a multi-select Continue stays disabled until the first pick (the exception named in Form Screen).

States: returning via back (previous answer shown selected), scaled text (rows grow and wrap; nothing clips), RTL (`LinearProgressIndicator` fills from the right on its own; trailing controls mirror).

## Search Surface

Rule: pre-query guidance fits the search task; suggestions are passive and based on available data. A clear hint can suffice for private or exact-identifier search. No-results offers recovery; preserve query state where appropriate without retaining sensitive data unnecessarily.

Skeleton:

- Material 3 `SearchAnchor` + `SearchBar`, or a plain `TextField` in the app bar when the project already has one. `autofocus` on the search screen; `textInputAction: TextInputAction.search`.
- Pre-query body (`query.isEmpty`): a `ListView` of sections — Recent (each row `Dismissible` or with a trailing clear icon; a "Clear all" text button), Popular, "You might like" (only when the recommendation data exists). Tapping a row fills the field and runs the search.
- Typing: debounce 200–300 ms before fetching suggestions; retain last good results with a loading/stale marker. Apply `async-state.md` for stale responses, cancellation, disposal and retry.
- Results: `ListView.builder` / `SliverList`; keep the query in the field and the filter chips row pinned above results.
- No results: message states the query, then recovery actions — "Search all categories", "Clear filters", spelling alternative when available, and popular items below.
- When the product's privacy and retention rules permit, preserve the last query and filters in route state for back and resume; omit or clear sensitive query state as those rules require.

States: empty history (browse or a useful input hint; popular content only with real data), offline (recents still work; results show a retry), error (retry in place, query preserved), loading (skeleton rows at result height, not a centered spinner).

## Stage-Adaptive Home

Use when usage evidence establishes different needs across stages. Retire completed onboarding; keep a shared home when the core task stays the same. Never fabricate stats.

Skeleton:

- If distinct stages are needed, reuse existing state outside `HomeScreen`; add an enum only when it simplifies real variants. The following three variants illustrate a fitness app, not a minimum requirement.
- The build method `switch`es on stage to assemble a list of section widgets, then renders them in one `CustomScrollView`/`ListView`. Sections are shared widgets (`GoalBanner`, `TodayPlanCard`, `StatsRow`, `ProgramList`); only their order, presence, and content change.
  - `newcomer`: welcome + one setup CTA, easy starting content, no stats.
  - `returning`: today's task card first, setup banner removed.
  - `power`: `StatsRow` first (real numbers; a stat with no data renders as "—" with a hint on how to earn it), then today's task, then optimization content.
- Apply stage changes according to the UX contract. Preserve familiar navigation and expose moved controls; choose transition timing deliberately rather than silently rearranging a workflow mid-task.

States: cover supported variants and applicable section loading/empty states with focused assertions; use representative goldens when they add visual regression coverage.

## Category And Collection Tiles

Use a tinted block with isolated imagery when a catalog needs consistent visual recognition. A text list or photo tile may be better for another task; choose based on content, scanability, and contrast.

Skeleton:

- `SliverGrid` with a fixed `childAspectRatio` (roughly 1.6-1.8 for two columns) and 12-16 gutters on the spacing scale.
- Remote images follow `platform-edges.md` Remote Images (placeholder and error state at final size).
- Tile: `Material` + `InkWell` (ripple, accessible target size, `Semantics` button with "Fruits, 32 items") over a `Container` with a per-category tint. Tints come from a token map (a `ThemeExtension` field such as `categoryTones`), never per-tile raw hex; derive each tone from the brand hue family so the grid reads as one palette in light and dark.
- Contents: label (`titleMedium`, `onSurface`), count (`bodySmall`, `onSurfaceVariant`), directional chevron, and a trailing cut-out image. Apply `flutter-delivery-contract.md` Image Decode Sizing to raster assets.
- Images share one treatment (same angle, lighting, and cut-out style). One mismatched photo breaks the grid; when a consistent asset is missing, fall back to an icon on the tint, not a stock photo.

States: count loading (skeleton number), empty category (count reads "0 items", tile still tappable to an empty state with a next action), scaled text (label wraps, image shrinks or hides before text clips).

## Controls Over Imagery

Rule: app bar and overlay controls must survive bright, dark, and busy images.

Skeleton:

- `Stack` with the image at the base and a `SafeArea` row of `IconButton`s at the top. Each button sits in a pill: `Container` with `surface` at 70-80% alpha (`.withValues(alpha: 0.75)`), a 1px `outlineVariant` border, `shape: BoxShape.circle` or a `StadiumBorder`; icon color `onSurface`. In dark mode use the same roles; the pill adapts.
- Alternative for a full-width bar: a short vertical gradient scrim (`onSurface` at ~35% → transparent) behind the controls only, never over the whole image.
- Never use `foregroundColor: Colors.white` on a bare image; that is the "works only because this photo is dark" failure.
- Verify with three images in a widget test or golden: a bright/white product shot, a dark shot, and a busy lifestyle photo.

## Numeric Input Choice

Rule: match the control to entry frequency and precision, not the data type.

| Situation | Control | Notes |
|-|-|-|
| One-time, bounded range, low precision (height, weight, age at setup) | `CupertinoPicker` / `ListWheelScrollView`, `Slider`, or a ruler picker | Fast, no keyboard; pre-select the median value; show the unit next to the value |
| Repeated or precise entry (daily grams, amounts, quantities) | `TextField(keyboardType: TextInputType.numberWithOptions(decimal: true))` + `inputFormatters`, or a stepper with long-press repeat | Keyboard opens immediately; submit reachable above the keyboard; the last value is remembered |
| Usage clusters on a few values | Preset chips ahead of the free-form control | Presets from real usage data, each with its outcome (price, calories); custom entry stays available |
| Date / time | `showDatePicker` / `showTimePicker` for Material; `CupertinoDatePicker` in the project's Cupertino sheet | A dialog route alone is not a picker. Preserve current value; handle cancel without committing. |

Choose using range, precision, entry frequency, and accessibility. One-time entry alone does not justify a wheel; compare actual effort for the intended value range.

Treat a numeric keyboard as an input aid, not validation. Parse with the project locale/number formatter; handle decimal/group separators and pasted input, allow temporary edits such as an empty value or trailing separator, and validate requiredness, range, sign, precision, and finite values before commit. Preserve invalid text with a specific inline error. Reuse the domain amount type and currency rounding rules; do not invent money arithmetic in the widget.

## Lists And Data Density

Operational lists (orders, transactions, jobs, messages) are scanned and compared, not read. Rule: one row = one decision; density serves comparison, decoration does not.

Skeleton:

- Use `ListView.builder` or `SliverList` for variable-height rows. For uniform rows, use `itemExtent`/`prototypeItem` on `ListView`, or `SliverFixedExtentList`/`SliverPrototypeExtentList`. Reassess fixed extents with text scaling. Group rows with spacing or a subtle divider when that preserves scanning density.
- Row anatomy, leading to trailing: identity (avatar/icon, 40dp) → primary text (`bodyLarge`, one line, ellipsis) + secondary line (`bodyMedium`, `onSurfaceVariant`) → trailing value (`bodyLarge`, tabular numerals via `FontFeature.tabularFigures()`) + status chip or timestamp (`labelSmall`). Trailing column right-aligned so numbers line up down the screen.
- Status is never color-only: chip carries text or icon. Semantic tokens for success/warning/error.
- Grouping: sticky section headers (`SliverPersistentHeader` pinned, or a `sticky_headers`-style package only if already installed) by date or state. Use the project section-heading style; add a container only when it clarifies a real group boundary.
- Swipe actions (`Dismissible`) always have a visible equivalent (visible overflow menu) and undo via `SnackBar`, placed per `flutter-delivery-contract.md` Layout And Navigation.
- Filters as a horizontal `ChoiceChip` row pinned above the list; active count shown on the filter button.

States: skeleton rows matching row height (not spinner), empty per filter ("No unpaid invoices" + clear filter), error inline with retry above the last good list, pagination loading row at the end, scaled text (secondary line wraps to two lines, row grows, trailing value never clips: give it flexible space or move it below the label; preserve the requested text scale).

## Form Screen

Rule (from `mobile-ux` Forms): every field justified, pre-filled, inline validation, input preserved, submit reachable above the keyboard.

Skeleton:

- `Form` + `GlobalKey<FormState>` with `AutovalidateMode.onUserInteraction`; a `Scaffold` whose body is a `ListView` (not `Column`) so the keyboard never causes overflow, `resizeToAvoidBottomInset: true`.
- Field order matches the mental order of the task; related short fields share a `Row` (city + postcode), everything else full width. Field spacing 16, section spacing 24-32 with a `titleMedium` section header.
- Each `TextFormField`: label as `labelText` (floats), helper text for format hints, `keyboardType` and `textInputAction` set (`next` for all but last, `done`/`send` on last), `autofillHints` set, `textCapitalization` for names. Error text is plain language and says what to do.
- Submit lives in `bottomNavigationBar` inside `SafeArea` with keyboard-inset padding, so it stays visible while typing. Label states the outcome ("Save address", "Create account"), disabled only while submitting, never for "form not yet valid" (users cannot see why). One exception: a multi-select Choice Question Screen disables Continue until the first pick, because an empty selection is visible.
- Progress for multi-step forms: `LinearProgressIndicator` or step text ("Step 2 of 3") at the top; back preserves entered values via the step's state object.
- Unsaved changes: use `PopScope(canPop: !dirty)` and the project's save/discard flow for system back and explicit close controls. After confirmation, permit the intended pop; check mounted state after the await. Test Android back and iOS explicit close (a blocked Cupertino back gesture may not call the pop callback).

States: submitting (button progress, fields read-only, not disabled-gray), server validation error (mapped back to the field, scroll to it with `Scrollable.ensureVisible`, focus it), offline (use an existing durable queue only when the operation contract permits it; otherwise explain the offline state and preserve input), success (navigate to the result, not back to an empty form).

## Bottom Sheet

Secondary tasks (filters, pick a slot, quick edit, share). Rule: a sheet is for tasks shorter than a screen; if it needs a scroll and a submit and a back stack, it is a screen.

Skeleton:

- `showModalBottomSheet(useSafeArea: true, isScrollControlled: true)` protects top and side insets. Use a scrollable body for tall content. A disposable read-only sheet may use `DraggableScrollableSheet` with its controller attached to the inner list; editable sheets use the dismissal policy below.
- Anatomy: title, optional close control, scrollable content, and pinned actions. Wrap actions in a bottom `SafeArea` plus keyboard padding from `MediaQuery.viewInsetsOf(context).bottom`; route-level `useSafeArea` excludes the bottom inset.
- Actions: one primary outcome and a secondary cancel/reset. For unsaved input, open with `enableDrag: false`, `showDragHandle: false`, and `isDismissible: false`; use explicit close/save controls and `PopScope` for back confirmation. `PopScope` alone does not protect drag dismissal, which directly calls `Navigator.pop`. Avoid a min-extent-dismissable `DraggableScrollableSheet` in this editor. Verify close, back, barrier, and drag paths preserve input.
- For local draft filters/edits, return with `Navigator.pop(context, result)` and let the caller apply it. If the project uses shared state, follow its ownership and commit/cancel contract instead.
- Semantics: give the visible title `Semantics(namesRoute: true, header: true, child: Text(title))`; the route supplies scope, not your title label. Verify the actual announcement, initial focus, traversal, and return focus with assistive technology.

States: loading options (skeleton inside the sheet, not a blank sheet), empty options (message + close), error (retry inside the sheet), scaled text (sheet grows to `maxChildSize`, content scrolls, action row stays pinned).

## Navigation Shell

Rule (from `mobile-ux` Navigation & IA): 3-5 primary destinations, state preserved per tab, system back honored, deep links land with a synthesized stack.

Skeleton:

- Reuse the existing router. With `go_router`, `StatefulShellRoute.indexedStack` preserves branch navigators. Navigation layout follows `flutter-delivery-contract.md` Layout And Navigation; width is a constraint, not a device-type check.
- Destinations: icon + label always (no icon-only bar), selected icon filled and unselected outlined from the same icon family, label `labelMedium`. Badges for counts via `Badge` on the icon; count, not a bare dot, when the number matters.
- Active-tab taps follow the product contract: preserve position, pop to root, or scroll to top. If root reset is specified, `goBranch(index, initialLocation: true)` handles that part; implement scrolling separately.
- Android back first honors the active branch navigator. At branch root, follow the existing app policy rather than inventing a first-tab redirect. Intercept with `PopScope` only when required: `canPop: false` affects predictive back, and `onPopInvokedWithResult` reports a pop attempt rather than retroactively vetoing it.
- Deep links: route definitions declare parents so a link to `/orders/123` builds Orders → Detail; use `redirect` for auth gating, returning to the intended location after sign-in.
- Full-screen flows (checkout, composer) push above the shell (`parentNavigatorKey: rootNavigatorKey`) so the bar hides and back returns to the originating tab.

States: unauthenticated (redirect, remember target), tab content loading (per-branch skeleton, bar stays interactive), badge count unknown (no badge, never "?"), reduced motion (tab switch without cross-fade), scaled text (test the baseline in `craft-rubric.md` Accessibility Thresholds; labels must remain distinguishable. Use shorter localized labels or an adaptive layout when the framework bar cannot fit them, and preserve full semantic names).
