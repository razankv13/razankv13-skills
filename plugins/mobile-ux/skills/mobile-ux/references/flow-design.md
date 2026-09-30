# Flow Design Reference

How to pick and baseline the metric, and a worked sample showing the required flow-design output shape.

## Contents

- Choosing The Metric
- Getting A Baseline
- Sample Flow Design (output shape)

## Choosing The Metric

One flow, one primary metric, chosen before any step is drawn. Pick from the layer the flow lives in:

| Flow type | Primary metric | Guardrail metric |
|-|-|-|
| Onboarding / first run | Launch → first value moment conversion | Time to first value; day-1 return |
| Signup / paywall | Value moment → account or paid conversion | Refunds; decline-path use |
| Sign-in / recovery | App open → authenticated, per method | Recovery starts; "can't sign in" tickets |
| Task flow (checkout, booking, posting) | Task completion rate from flow entry | Error rate; median duration |
| Search | Search → result tap (or zero-result rate) | Query refinements per session |
| Status / tracking | Support contacts per order (lower) | Screen re-opens per order |
| Retention mechanic | D7 / D30 return among users who saw it | Opt-outs; notification disables |
| Settings / account | Task success on top complaints | Support tickets tagged "can't find" |

A guardrail metric catches a win that costs something (conversion up, refunds up). Name both, and give each its own baseline source below; one can be baselined (e.g. support tickets) while the other is pending.

## Getting A Baseline

No target without a baseline. In order of preference:

1. **Product analytics** on the current flow: funnel between the two events that bound the metric, last 4 weeks, segmented by platform. Ask for the export or the dashboard link.
2. **Support and review data**: count tickets or reviews naming the flow; use as a proxy for a "lower" metric.
3. **Manual funnel**: if no instrumentation, define the two events now, ship instrumentation with the flow, and set the target after 2 weeks of data. Label the target *pending baseline* in the deliverable.
4. **Benchmarks** (industry or prior product): allowed only as a sanity range, never as the target. State the source.

Write the target as: `metric: baseline → target, measured over window, segment`. Example: `checkout completion: 61% → 68%, 4 weeks post-release, iOS + Android`. Without step 1-3 data, write `target: pending baseline (instrumentation shipped with flow)`.

## Sample Flow Design (output shape)

Condensed. Real deliverables carry one flow, applicable states per step (N/A with a reason where needed), interruption/resume, a primary metric and guardrail, and assumptions.

> **Flow:** Grocery app — product detail → order placed (returning user, item in stock).
> **Metric:** detail-view → order-placed conversion: 14% → 16% (target set by product owner), 4 weeks post-release, iOS + Android combined (no platform split available).
> **Guardrail:** orders edited or cancelled within 10 min of placing: 3.2% from order-event logs (last 4 weeks) → must not rise.
>
> 1. **Product detail** (entry from search result or category). Information order: what it is → trust evidence (rating, if reviews exist) → unit price → one-tap presets from purchase data (0.5/1/2 kg, each with its price) → custom quantity → commit action stating the total ("Add to cart · $X"), reachable without losing the quantity choice.
>    Loading: price and presets pending, commit unavailable until price is known. Empty: n/a (entry requires a product). Error: price fetch fails → stale price labeled, retry available; adding to cart may continue only if checkout revalidates price and obtains consent to any change before charging. Success: item added with immediate acknowledgment; commit action becomes "View cart · 1 item · $X".
> 2. **Cart** (from the commit action or cart destination). Line items with editable quantities, delivery slot pre-selected to the user's last slot, total with fees itemized, commit action "Place order · $X".
>    Loading: totals pending, place-order unavailable. Empty: states the cart is empty, one action: "Browse categories". Error: slot no longer available → preserve the cart, explain the change, require selection or explicit acceptance of a replacement before ordering. Success: goes to 3.
> 3. **Payment** (saved method pre-selected; adding a method is one step). Amount repeated in the commit action.
>    Loading: pending status on the commit action, no optimistic success, duplicate submission blocked. Empty: no saved method → add-method step opens directly. Error: declined → processor reason in plain words, retry with same or other method, cart intact. Success: authoritative confirmation → 4.
> 4. **Order status** (value moment). Answers in order: state ("Order placed") → delivery window → address → courier ("assigning") → stage timeline: Placed ✓ → Preparing → Out for delivery → Delivered. Primary action: Track. Secondary: Edit (until Preparing), Contact.
>    Loading: state shows immediately from the local order, details follow. Empty: n/a. Error: status fetch fails → last known state marked stale, retry. Success: state updates in place; a push on each change lands here.
>
> **Interruption / resume:** cart persists across kill; payment interrupted after authorization but before confirmation → on relaunch, poll order status before showing cart; never double-charge. System back from step 4 goes to Home, not Payment.
>
> **Assumptions:** analytics access limited to overall conversion, no platform split; delivery slots assumed pre-fillable from last order; courier assignment is asynchronous.

Every step names applicable states. Interruption/resume is explicit. The primary metric has a baseline and a labeled-pending target, alongside a guardrail. Steps name information order, actions, and states only; layout, positioning, and loading-indicator style belong to the UI skill.
