# Auditing the Google Merchandise Store: a $14k–48k/year mobile payment leak, and the tracking errors hiding it

**Service:** S1 GA4 Measurement & Funnel Audit · **Context:** solo rebuild of an MSc group project (NOVA IMS, Digital Analytics) · **My role:** the whole audit, done solo (configuration review, KPI framework, funnel analysis, leak sizing, live tag test, recommendations) · **Team:** solo
**Data:** Google's public GA4 demo property (Google Merchandise Store), last 90 days (mid-July to mid-October 2026), plus a live tag inspection of shop.merch.google on 31 Oct 2026 · **Tools:** GA4 (Admin, Reports, Explorations), browser network inspection

---

## 1. The business problem
The Google Merchandise Store sells branded merchandise online, at about **$743k of revenue per quarter** (≈$3.0M a year). Its team needs to know two things before spending more on traffic: **can the analytics be trusted, and where does the funnel lose money?** Every channel budget, ROAS figure and Google Ads bid depends on the answer.

## 2. Headline results
- **Mobile converts 5.5× worse than desktop (1.7% vs 9.4%).** The widest gap is at the **payment step** (44.9% vs 78.6%). Closing half of it is worth **≈ +122 purchases per quarter, a range of $14k–48k a year**.
- **The data overstates success:** 1 of 15 configuration checks failed and 7 passed only partly. Product views and a test event are counted as "key events", so "conversions" run at **~70× real purchases**, and Google Ads is linked to them.
- **A live tag test shows the 74% product-view → cart drop is real behaviour, not broken tracking.** It also found **8 GA4 properties and a deprecated Universal Analytics tag** firing on every page.

## 3. Approach
| Step | What I did | Why this and not the alternative |
|---|---|---|
| Data understanding | 15-point configuration audit: retention, filters, referrals, cross-domain, events, key events, consent, links ([full audit](./audit/task1_config_audit.md)) | Measurement validity comes before analysis. A clean funnel on broken tracking is still wrong |
| KPI framework | See-Think-Do-Care, with one objective and two KPIs per stage, each with a GA4 source and a baseline | Ties every metric to a decision. KPIs that map to nothing are vanity metrics |
| Analysis | Closed funnel exploration (view_item → purchase), active users, split by device | Users, not events, so repeat clicks don't inflate the steps. The device split exposes a gap that the total funnel hides |
| Leak sizing | Users at the step × points recovered × downstream rate × device AOV × 365/90, as a conservative and a target scenario | **Absolute** points × users, not relative gaps: money follows volume. Ranges, because every input is an assumption |
| Validation | Duplicate check (purchase events ÷ unique transaction IDs = 1.00), and a live tag test of every funnel step in the browser | Rules out double counting and broken tags before blaming user behaviour |

## 4. Key findings

### Finding 1: The money leaks at the mobile payment step
| Step (active users, 90 days) | Desktop | Mobile |
|---|---|---|
| view_item | 35,525 | 19,447 |
| → add_to_cart | 32.0% | 16.1% |
| → begin_checkout | 53.2% | 43.7% |
| → add_payment_info | **78.6%** | **44.9%** |
| → purchase | 70.2% | 53.0% |
| **Purchase rate** | **9.4%** | **1.7%** |

35% of product viewers browse on mobile, but only 9% of purchasers buy on mobile. Mobile also has a larger gap at product → cart, but much of that is normal "browse on the phone, buy on the laptop" behaviour, while a user who has already started checkout has decided to buy. Assuming 50% of the payment gap is recoverable (vs ~20% at product → cart), **the payment step is worth ≈$48k a year against ≈$25k**. The product page only becomes the bigger prize if more than ~38% of its gap turns out to be fixable. A forced Google sign-in before checkout, found in the live test, is one likely source of mobile friction.

### Finding 2: The KPIs are inflated in ways nobody can see
- **Key events:** `view_item` and an undocumented test event (`xyz`) are marked as key events, so "conversions" are dominated by browsing (~70× purchases). With Google Ads linked, automated bidding can optimise towards browsers instead of buyers.
- **Internal traffic:** the internal and developer filters are left in *Testing* and tag **0%** of traffic, yet signs of internal visits exist (`art-analytics.appspot.com`, 2,998 sessions). Staff sit inside every customer KPI by an amount nobody can measure.
- **Revenue:** refunds are never sent to GA4 (0 refunds on 4,178 orders), so revenue is likely overstated by **~$15–37k per quarter** (assuming a 2–5% refund rate). 5.8% of sessions are *Unassigned* because of UTM errors.

### Finding 3: Tag sprawl and consent gaps (live test, 31 Oct 2026)
Every page sends hits to **8 GA4 properties**, a **Universal Analytics** tag (UA stopped processing data in 2024) and **8 Google Ads / Floodlight tags**. Hits were sent while performance cookies were not granted, and GA4's consent settings show analytics consent signals as inactive. That is both a data-completeness gap and a **GDPR risk** for EU visitors. An Ads "Checkout" conversion also fires on the cart page, before checkout starts. On the positive side, `add_to_cart` fired exactly once per click with full item data. **The funnel numbers in Finding 1 reflect real behaviour.** ([test log](./audit/tracking_test.md))

## 5. So what? Recommendations
| # | Action | Owner | Effort | Expected impact | How to measure |
|---|---|---|---|---|---|
| 1 | Unmark `view_item` and `xyz` as key events; keep `purchase` primary and `add_to_cart` secondary | Analytics | S (5 min) | Reports and Ads bidding optimise for buyers, not browsers | Key events per day drop to the purchase + cart level; Ads cost per purchase over 4–6 weeks |
| 2 | Define the internal-traffic rule, flag staff orders with a user property, report customer KPIs without staff | Analytics + Dev | M | Removes an unmeasured error from every KPI, and gives action 3 a clean baseline | Share of sessions tagged internal > 0%; the appspot referral falls towards 0 |
| 3 | Fix the mobile payment step (wallet payments, simpler card form, test guest checkout) as a 50/50 A/B test | Dev + Analytics | M | ≈ +122 purchases per quarter, **$14k–48k a year** | Mobile checkout → payment 44.9% → ~62%; mobile purchase rate 1.7% → 2.3%; guardrail: mobile AOV ≥ $96 |

**Order matters:** 1 is free and stops wasted ad spend today. 2 comes before 3, because a conversion uplift can't be proven while staff traffic sits inside the baseline. 3 is where the money is.
**Escalate separately:** Consent Mode v2 and the tag clean-up (8 properties, legacy UA) go to Dev + Legal, not to a marketing sprint.

## 6. Limitations and what I'd do with more data
- **Demo store bias:** this is a brand fan store with Google staff among its visitors. The method transfers to other shops; the benchmarks don't.
- **Read-only access:** the internal-traffic rules, the refund volume and the meaning of the 16 $0 orders could not be verified. In a client project these are the first questions on the intake call.
- **Cross-device:** GA4 credits the device where the purchase happens, so the mobile leak may be **smaller** than measured. User-ID or BigQuery would size it.
- **Assumptions push in both directions:** the recoverable share and the selection effect (users who drop at payment are less motivated) push the estimate down; seasonality (Jul–Oct excludes Black Friday and Christmas) pushes it up. Hence a range, to be confirmed by an A/B test.
- **Live test scope:** one desktop browser, and stopped at the sign-in wall. Next I'd repeat it on iOS and Android and test the steps after sign-in.

## 7. Links
- Full 15-point audit: [`task1_config_audit.md`](./audit/task1_config_audit.md) · KPI framework: [`task2_kpi_framework.md`](./audit/task2_kpi_framework.md) · Funnel and leak sizing: [`task3_funnel_diagnosis.md`](./audit/task3_funnel_diagnosis.md) · Live tag test: [`tracking_test.md`](./audit/tracking_test.md)
- Reusable checklist (service S1): [`S1_GA4_Audit_Checklist.md`](./S1_GA4_Audit_Checklist.md)
- Screenshots: [`screens/`](./screens)

---
*All GA4 analysis is my own. I used an AI assistant (Claude) as a reviewer and to help draft the write-up and run the live tag inspection.*
