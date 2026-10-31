# Task 2 — KPI Framework, See-Think-Do-Care (checkpoint ✋2)
**Property:** GA4 – Google Merchandise Store (demo) · **Analyst:** João Domingues · **Worked live:** Sat 24 Oct 2026
Baselines = last 90 days from `task3_funnel_diagnosis.md`. Targets come from the leak sizing, not from guesses.

| Stage | Business objective | KPI 1 | KPI 2 | GA4 source | Baseline → target (source) |
|---|---|---|---|---|---|
| **Do** | Convert ready-to-buy visitors into paying customers, especially on mobile | **Purchase rate by device** = purchasers ÷ users who viewed a product | **AOV by device** = purchase revenue ÷ purchases | KPI 1: Explore → Funnel exploration (closed, users, breakdown Device category). KPI 2: Explore → Free-form (device × purchase revenue ÷ purchases) | KPI 1: mobile 1.7% → **2.3%** if the checkout fix lands ((326 + 122) ÷ 19,447); desktop hold ≥ 9.4%. KPI 2: mobile $96 / desktop $187 → **guardrail**: must not fall while conversion rises (own baseline) |
| **Care** | Turn first-time buyers into repeat customers | **Month 1 repeat purchase rate** = buyers who purchase again in the month after their first purchase ÷ cohort size | **Average 120-day value per user** (purchase revenue per acquired user, first 120 days) | KPI 1: Explore → Cohort exploration (inclusion: Any transaction; return: Any transaction; monthly; Active users). KPI 2: Reports → Retention → Average 120d value card | KPI 1: **6.4%** overall (371 ÷ 5,768; Oct cohort excluded, no Month 1 yet), range 4.6–9.6% across May–Sep 2026 cohorts. Target: hold the baseline, then +1 pt once a post-purchase retention action runs (own baseline, no external benchmark). KPI 2: **$2.03** per acquired user (120 days ending 23 Oct 2026). Track the trend, no target yet |

**Do — what these KPIs can't tell you:** whether a device's customers really buy less, because GA4 credits the device where the purchase happened, not where the shopping started, so mobile looks worse and desktop better than reality (cross-device journeys are split into two "users").

**Care — what these KPIs can't tell you:** GA4 counts devices/cookies, not people: a customer who buys again on a new device or after clearing cookies looks like a new buyer, so repeat purchase is **undercounted**. The $2.03 is averaged over **all** acquired users, most of whom never buy, so it is not customer lifetime value. Adding columns of the cohort table double-counts people who buy in several months. Customer-level LTV needs User-ID, BigQuery or the order system.

Screens: [cohort repeat purchase](../screens/cohort-repeat-purchase.png), [120-day value](../screens/ltv-120d.png)

