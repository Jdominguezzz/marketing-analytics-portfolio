# Task 3 — Funnel Diagnosis (checkpoint ✋3)
**Property:** GA4 – Google Merchandise Store (demo) · **Analyst:** João Domingues · **Worked live:** Sat 17 Oct 2026
**Setup:** Explore → Funnel exploration · **closed funnel** · last 90 days · breakdown: Device category · unit = **active users** (not events)

## Headline
**35% of product viewers browse on mobile, but only 9% of purchasers buy on mobile. Mobile converts 5.5× worse than desktop (1.7% vs 9.4%), and the widest gap is begin_checkout → add_payment_info (44.9% vs 78.6%, ~34 points).**

## Why checkout → payment, not view → cart (leak choice, worked live 24 Oct)
| | Leak A: checkout → payment | Leak B: view → cart |
|---|---|---|
| Mobile users at the step | 1,369 | 19,447 |
| Points to close the full gap to desktop | 33.72 (78.64 − 44.92) | 15.93 (32.03 − 16.10) |
| Downstream mobile rate to purchase | 53.01% | 10.41% (326 ÷ 3,131) |
| Extra purchases, full gap (90 days) | ≈245 | ≈323 |
| Realistically recoverable share | ~50% | ~20% |
| **Realistic extra purchases (90 days)** | **≈122** | **≈65** |
| **Per year** (× mobile AOV $96 × 365/90) | **≈$48k** | **≈$25k** |

Closing the full mobile gap, view → cart is worth more (≈323 extra purchases per 90 days vs ≈245 at checkout → payment). But much of the view → cart gap is normal behaviour, not a fault: people browse on mobile and buy on desktop, so I estimate only ~20% of it is recoverable. At checkout → payment, users have already decided to buy, so the drop points to payment friction on mobile (card entry, missing wallet payments), and I estimate ~50% is recoverable. That gives ≈122 vs ≈65 extra purchases (≈$48k vs ≈$25k a year), so the checkout fix is the priority. I'd confirm it by A/B testing the payment-step fix, and check the cross-device share with User-ID reports.

**Sensitivity:** with 50% of the checkout gap recoverable, the product page only wins if more than ~38% of the cart gap is recoverable (322.6 × b > 122). *Client line:* "If more than about 38% of the cart gap turns out to be fixable, the product page wins; until we know, I'd start with checkout because it's a small, testable dev change, and measure the cart gap's real share in parallel."

## Funnel table (active users, step completion rate)
| Step | Total | Desktop | Mobile | Tablet |
|---|---|---|---|---|
| 1 view_item | 55,363 (→ 26.28%) | 35,525 (→ 32.03%) | 19,447 (→ 16.10%) | 518 (→ 16.41%) |
| 2 add_to_cart | 14,547 (→ 51.05%) | 11,379 (→ 53.15%) | 3,131 (→ 43.72%) | 85 (→ 35.29%) |
| 3 begin_checkout | 7,426 (→ 72.53%) | 6,048 (→ 78.64%) | 1,369 (→ 44.92%) | 30 (→ 50%) |
| 4 add_payment_info | 5,386 (→ 68.18%) | 4,756 (→ 70.19%) | 615 (→ 53.01%) | 15 (→ 53.33%) |
| 5 purchase | 3,672 (6.6% of step 1) | 3,338 (9.4%) | 326 (1.7%) | 8 (1.5%) |


## Sizing the leak (mobile, begin_checkout → add_payment_info)
Formula: mobile users at begin_checkout × uplift (pts) × mobile downstream rate (add_payment_info → purchase) × mobile AOV × 365/90

| Input | Value | Source |
|---|---|---|
| Mobile users at begin_checkout | 1,369 | funnel, step 3 |
| Current mobile rate | 44.92% | funnel |
| Downstream mobile rate | 53.01% | funnel, step 4 |
| Mobile AOV | $96 | Explore free-form, device × revenue ÷ purchases, 90 days |
| Desktop AOV (reference) | $187 | same |
| Annual revenue (reference) | ≈ $3.0M | $742,957.90 × 365/90 (check 2) |

| Scenario | Extra users | Extra purchases | $ / 90 days | **$ / year** | % of revenue |
|---|---|---|---|---|---|
| Conservative: +5 pts (44.9% → 49.9%) | 68.5 | 36.3 | $3,483 | **≈ $14k** | 0.5% |
| Target: close half the desktop gap (+16.86 pts) | 230.8 | 122.4 | $11,746 | **≈ $48k** | 1.6% |

**Range to present: ≈ $14k–$48k per year (0.5–1.6% of revenue).** Lead with the insight (5.5× gap at the payment step), use the range as the reason to act.

## Assumptions and corrections
- **Corrected input (B):** Initially assumed mobile AOV = overall AOV ($177.83); GA4 shows mobile AOV is $96, so the estimate was revised down by 46%. *(First estimate was $26k–$88k.)* Mobile AOV is about half of desktop's: either small impulse purchases on mobile, or large baskets researched on mobile and bought on desktop.
- **A — recovered users convert like current ones:** This assumes recovered mobile users buy at the current 53% rate. Users who abandon at payment are likely less motivated (selection effect), so the estimate is likely **too high**. *Test:* A/B test the payment-step fix and measure the real uplift.
- **Cross-device:** This assumes the mobile gap is real behaviour, not measurement. Users who browse on mobile and buy on desktop are counted as mobile drop-offs, so the real leak may be **smaller**. *Test:* User-ID / cross-device reports; device rows sum to more than the Total (55,490 vs 55,363).
- **C — seasonality:** This assumes the last 90 days (mid-July to mid-October) look like the rest of the year, but the best retail period is November–December, so the estimate is likely **too low**. *Test:* compare mobile users reaching begin_checkout in Jul–Oct 2025 vs Nov–Dec 2025 (possible thanks to 14-month retention, check 1).
- Net: A, B and cross-device push the number down; C pushes it up → present a **range**, not a point.

