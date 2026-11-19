---
name: premium-flutter-ui
description: "Use for Flutter iOS/Android UI: screens, components, ThemeData/ThemeExtension tokens, motion, accessibility, dark mode, M3 migration, UI review, anti-generic critique. Not web UI or UX flows."
metadata:
  author: Raza Abbas
  tags: [flutter, ui, ios, android, theming]
---

# Premium Flutter UI

## Purpose

Make Flutter mobile UI read **product-authored** — every visual decision traceable to this product's content, brand, or audience — instead of template-generated. Default target is iOS and Android mobile; include web, desktop, or pricing strategy only when the user asks.

This skill owns implementation, review, refactor, and polish of Flutter UI code. For a packaged art-direction concept (divergent directions, tokens JSON, motion spec from a screenshot or brief), use `mobile-art-director` if installed; otherwise develop the concept here using the project's design guidance. Behavioral UX (flows, onboarding, retention, friction) belongs to `mobile-ux`; this skill implements the screens those flows specify. HTML/CSS/JS/React web UI is out of scope.

## Prerequisites

- The target Flutter code, or a standalone snippet or screenshot for a general review.
- For validation: a Flutter SDK able to run `flutter analyze` and `flutter test` in the target repo.
- For rendered inspection: a simulator, emulator, or screenshot harness. Without one, rendered checks are reported as unverified.

## First Move

Classify the request:

- **Generate** — build a screen, flow, component, theme, or UI system.
- **Review** — find UI, accessibility, state, token, or generic-look issues.
- **Refine** — adjust one layer: copy, layout, type, color, motion, or states.
- **Audit** — findings and a prioritized plan; change code only if asked.

Scale the census, state coverage, and validation to the requested change. For each affected state or constraint, record **checked**, **not applicable** (the surface lacks that behavior), or **unverified** (name the blocker). Absence of evidence is unverified, not a pass. A copy or spacing refinement checks the affected component and its relevant states; a new screen or shared component needs broader coverage. Preserve accessibility and recovery behavior in the affected surface; do not expand a small edit into a screen redesign.

Then run a **token census** — locate the design vocabulary that already exists before inventing any:

- Project instructions: the target repo's agent instruction files (AGENTS or CLAUDE), nearest feature instructions, design/product docs.
- Existing UI system: `ThemeExtension` subclasses, design system or theme directories, context extensions (a `colorScheme` getter on `BuildContext`), spacing/radius/motion constants, branded components, icon/asset wrappers, localization setup.
- Target code: the screens, widgets, state objects, and tests the request touches.
- Visual evidence: screenshots, goldens, Widgetbook stories when available.

Census done when: every visual value you will emit either names the token it came from or carries a one-line justification for a new token. An unexplained raw hex, radius, or font literal in a tokened repo forks the design system and reads as generic output. Project instructions beat this skill; reuse existing primitives before adding wrappers, packages, or vocabulary.

Standalone snippet or general review with no named repo → skip repo-specific checks, mark them not applicable, review against Flutter/mobile fundamentals.

## Reference Routing

Read the branch the task needs:

- `references/craft-rubric.md` — critique, premium polish, dark mode, hierarchy, motion, accessibility thresholds, visual direction, anti-generic detector.
- `references/review-calibration.md` — every numerical screen rating: fixed scoring anchors, severity, evidence limits, and a worked example.
- `references/flutter-delivery-contract.md` — before implementing: theming, layout, packages, localization, validation commands.
- `references/product-modes.md` — when persona, scope, output depth, or greenfield-vs-existing expectations are unclear.
- `references/screen-patterns.md` — Flutter structure for detail-and-commit screens, status timelines, paywall plan pickers, verification code fields, empty/error/offline status views, choice question screens, search surfaces, stage-adaptive home, category tiles, controls over imagery, numeric input choice, dense lists, form screens, bottom sheets, and the navigation shell. Read when the task is one of these screens.
- `references/async-state.md` — submission, search, pagination, retries, partial/offline results, cancellation and navigation during work.
- `references/platform-edges.md` — system bars and edge-to-edge, orientation and foldables, RTL, keyboard focus for custom controls, bold text / high contrast / screen-reader settings, dynamic color, remote image failure, permission-denied state, M2→M3 migration. Read when the change touches any of these.
- `references/example_test.dart` — reference widget tests for `example.dart`: real semantics-tree labels/actions, stable busy geometry, localization, text scaling/RTL, keyboard activation, reduced motion, token interpolation, and optional light/dark goldens. Choose the checks that exercise the changed behavior; a static component needs no motion test, and goldens are optional when focused layout/state assertions and rendered inspection cover the risk.
- `references/example.dart` — adaptable reference: `ThemeExtension` token layer with `lerp`, context accessor, light/dark wiring, one branded `FilledButton` with framework activation, localized announcements, stable busy layout, and focus styling. Both files are validated together on Flutter 3.47.5; runnable setup is in `references/flutter-delivery-contract.md` Validation. Read before building a token layer or branded component from scratch; adapt names and values to the product.

- `references/skill-evaluation.md` — only when maintaining this skill: repeatable form, search, and narrow-refinement scenarios with acceptance criteria.

## Shared UX → UI Handoff

For mixed work, carry one compact contract: **user goal → flow and applicable states → constraints → acceptance checks**. Reuse the contract from `mobile-ux`; fill only missing implementation details. Keep observed evidence, assumptions, and unverified behavior distinct. If `mobile-ux` is unavailable, derive the contract from the brief and existing behavior; ask only for a missing decision that blocks the requested change.

Optional external design references: use a library supplied by the user or named in project instructions. Without one, use the bundled references. Examples inform choices; product requirements come from the brief.

## Build Order

For generation or refactor:

1. Define the primary job, audience, platform assumptions, and real content. Use representative, privacy-safe content and realistic edge cases (`USD 12,847.32`, `Alexandra Konstantinidis`) and, where the screen holds photos, real imagery variance (bright, dark, busy, clean) — the places real content breaks are where design decisions are needed.
2. Census results in hand: existing tokens, components, navigation, state, localization patterns.
3. Shape hierarchy in grayscale first — spacing, size, weight, contrast. Color is the last layer.
4. Style through `ThemeData`, `ColorScheme`, `TextTheme`, `ThemeExtension`.
5. Smallest useful branded component layer for repeated controls.
6. Cover applicable loading, empty, error, disabled, pressed, selected, focus, success, text-scale, and reduced-motion states for the changed surface; plus search pre-query and lifecycle-stage variants where the surface has them.
7. Motion only where it clarifies state, navigation, progress, or completion.
8. Accessibility and localization hooks inside the implementation, not appended after.
9. Run the smallest validation that proves the change; report skipped checks with reasons.

## Review Order

Lead with findings. Check:

- Real hierarchy: what dominates at a squint, what action is thumb-reachable, what can wait.
- Token compliance: every visual value traces to a token or a justified exception.
- Generic tells: inspect against the Anti-Generic Detector in `references/craft-rubric.md`.
- State quality: off-happy-path screens, button states, form errors, undo/retry, loading stability.
- Mobile constraints: narrow-phone width, safe areas and edge-to-edge insets, status-bar icon contrast, keyboard insets, bottom navigation, landscape, and scaled text. Use `references/craft-rubric.md` Accessibility Thresholds for the test baseline.
- Media resilience: every control drawn over imagery survives a bright, a dark, and a busy image; imagery in grids shares one treatment.
- Accessibility: contrast, semantics, hit targets, keyboard focus and activation, focus order, screen-reader labels, reduced motion, RTL mirroring.
- Performance: lazy lists, stable subtrees, image sizing, animation scope, frame-budget risks.

Audit output: findings sorted P0/P1/P2 (defined in `references/review-calibration.md`) with evidence (file/screen + observation) and a minimal fix. Contrast findings show the computed ratio as `fg #HEX on bg #HEX = N.N:1` — computed, not estimated.

## Identity Contract

The positive moves that make output product-authored. Prioritize clarity, consistency, and care off the happy path; add distinctive details where they serve the product.

- **Choose the three identity carriers deliberately: primary color, corner radius, type.** Each traces to the product's brand or audience. Target: the screen expresses the product while preserving familiar platform controls and behavior.
- **Add a signature detail when it earns its place.** A distinctive interaction, micro-animation, or composition should reinforce the product or clarify the task. When added, name it and why it fits. Settings, forms, and confirmations can be complete through clarity and restraint; do not add novelty to satisfy a quota.
- **Explain consequential visual decisions.** Cite a brand token, content need, audience evidence, or platform convention. Label an untested design hypothesis as an assumption; a plausible color story is not user research.
- **Spend craft on trust moments.** Onboarding, empty states, errors, loading, confirmation — reputation is built off the happy path.
- **One coherent visual direction per app** (see `references/craft-rubric.md` Visual Direction); borrow accents sparingly.

Guardrail: the Anti-Generic Detector in `references/craft-rubric.md` enumerates the patterns that read as template output (gradient-identity clichés, glass-on-everything, card grids, framework-default styling). Generation and review both inspect against it; repo already uses one of those patterns deliberately and the user keeps that direction → follow the repo.

## Package Rules

Climb this ladder; stop at the first rung that holds:

1. Existing project primitive or established dependency usage.
2. Flutter framework or platform-adaptive widget.
3. Already-installed dependency that covers the behavior.
4. Tiny local wrapper.
5. New dependency, only when it removes repeated work.

Prefer built-in `HapticFeedback`, Flutter animations, `LayoutBuilder`, `MediaQuery` size lookups, `SafeArea`, `Semantics`, lazy builders, project tokens. Package specifics and shortlist: `references/flutter-delivery-contract.md`.

## Completion Bar

Done means the output:

- Matches existing architecture and project instructions.
- Names the tokens/primitives reused, or the justification for each new one.
- Handles the applicable states and mobile constraints for the changed surface from Build Order step 6 and Review Order.
- Explains any signature detail added; none is required.
- Ran focused validation (analyzer/test/golden/screenshot/a11y/performance as relevant). Use applicable checks from `references/example_test.dart`; motion tests must assert the reduced-motion behavior, not merely that a widget renders.
- For rendered UI changes, inspect the affected output using an available simulator/emulator or screenshot harness. Use `references/review-calibration.md` scoring and the full craft rubric for new screens, redesigns, or requested audits; for a small refinement, check the affected layout and states. No visual check possible → say so explicitly.
- Names residual risks, skipped checks, and any package or licensing concerns.

## Examples

Review finding:

> **P1 · Low-contrast secondary label** — Wallet screen, balance card: `TextStyle(color: Color(0xFF9E9E9E))` on the card surface. fg #9E9E9E on bg #FFFFFF = 2.7:1, below 4.5:1 for body text. Fix: use `colorScheme.onSurfaceVariant` and recheck the ratio in light and dark.

Generate request, "build a settings screen": census the theme first, reuse the existing list-tile and switch primitives, cover loading, error, disabled, and 200% text scale, then report which tokens were reused and which checks ran.

## Limitations

- Mobile Flutter only; web, desktop, and pricing strategy are covered only when asked.
- Analyzer and widget tests prove code, layout, and semantics behavior, not appearance; without a simulator or screenshot, visual quality stays unverified.
- Screen reader output (VoiceOver/TalkBack) needs a device or simulator pass; semantics-tree tests are a proxy.
- The reference `example.dart` and `example_test.dart` are validated on Flutter 3.47.5; check the installed SDK for API differences.
- Mobbin citations come from Mobbin's iOS catalog; Android patterns are mapped equivalents.

## Troubleshooting

| Symptom | Cause | Fix |
|-|-|-|
| Output uses raw hex, radius, or font literals | Token census skipped | Rerun the census; map each value to a token or justify a new one |
| Screen looks like a template | Framework defaults or Anti-Generic Detector patterns | Check `references/craft-rubric.md` and set the three identity carriers |
| Small copy fix grows into a redesign | Scope not held | Check only the affected component and its states |
| No way to inspect the rendered UI | No simulator or screenshot harness | Mark visual checks unverified and name the blocker |
