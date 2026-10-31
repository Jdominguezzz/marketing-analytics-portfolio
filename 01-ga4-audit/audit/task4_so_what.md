# Task 4 — So what? (checkpoint ✋4)
**Property:** GA4 – Google Merchandise Store (demo) · **Analyst:** João Domingues · **Worked live:** Sat 24 Oct 2026
**In one line:** fix what the data measures first (1–2), then fix the mobile payment step, worth ≈$48k a year (3).

| # | Action | Owner | Effort | Expected impact | How we measure it |
|---|---|---|---|---|---|
| **1** | **Clean key events:** unmark `view_item` and the test event `xyz`; keep `purchase` as the primary key event and `add_to_cart` as secondary (check 9) | Analytics | **S** (5-min toggle) | "Conversions" stop being ~70× purchases, so reports and Google Ads bidding (linked, check 13) optimise for buyers, not browsers | Key events per day drop to the purchase + add_to_cart level (**expected: a big drop, that's the fix working**); Google Ads cost per purchase over the next 4–6 weeks |
| **2** | **Make internal traffic visible:** define the internal-traffic rule, flag staff orders with a user property (`customer_type = employee`), then report customer KPIs with staff excluded (check 2) | Analytics (rule) + Dev (user property) | **M** | Removes an unmeasured error from every customer KPI and gives action 3 a clean baseline | Share of sessions tagged internal > 0% in Testing before activating; `art-analytics.appspot.com` referral sessions (2,998 in 90 days) fall towards 0 |
| **3** | **Fix the mobile payment step:** add wallet payments (Apple/Google Pay), simplify the card form; ship as a 50/50 A/B test (task 3) | Dev (+ Analytics for the test) | **M** | ≈ +122 purchases per 90 days, **≈$48k a year** (range $14k–48k; 50% of the mobile gap recoverable) | Mobile checkout → payment 44.9% → ~61.8%; mobile purchase rate 1.7% → **2.3%**; guardrail: mobile AOV stays ≥ $96 |

**Why this order:** 1 costs nothing and stops wasted ad spend today. 2 must come before 3: if staff visits sit inside the mobile conversion rate, a 1.7% → 2.3% uplift can't be proven. 3 is where the money is.

**Also flagged (not in the sprint):**
- **Consent Mode v2 incomplete (check 12):** analytics and ads cookie signals are inactive. A **GDPR risk** for EU traffic and a data gap. Owner: Dev + Legal, effort L. Escalate to legal now, rather than plan it as a marketing task.
- Refunds not tracked (check 6): revenue likely overstated by ~$15–37k (assumption). Unassigned traffic 5.78% with UTM errors (check 11).
