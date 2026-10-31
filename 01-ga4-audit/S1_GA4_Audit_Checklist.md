# GA4 Measurement Audit: 20-point checklist
*Can you trust your Google Analytics 4 numbers? Run these checks before you spend another euro on traffic.*
João Domingues · Marketing Analytics · linkedin.com/in/joaopedrodominguesin · joao.pedrodomingues@hotmail.com

**How to use it:** mark each check ✅ Pass · ⚠️ Partial · ❌ Fail · 🔒 Can't verify, and write down the evidence (a number or a screenshot). Any ❌ in sections A–C means a KPI on your dashboard is probably wrong.

## A. Data collection settings
| # | Check | Where to look | Red flag |
|---|---|---|---|
| 1 | Data retention | Admin → Data collection and modification → Data retention | Left at 2 months: no year-on-year comparisons in Explorations |
| 2 | Internal & developer traffic filters | Admin → Data filters, then test them in Explore with the *Test data filter name* dimension | Filters in *Testing*, or catching 0% of traffic: staff visits inflate every KPI |
| 3 | Unwanted referrals | Admin → Data streams → Configure tag settings → List unwanted referrals | Payment gateways (PayPal, Stripe, bank 3-D Secure) or sign-in domains appear as *Referral* sources |
| 4 | Cross-domain measurement | Same menu → Configure your domains | Your own domains missing: one customer becomes two users and sales go to *Direct* |
| 5 | Enhanced measurement | Data streams → Enhanced measurement | Site search off (you lose what customers look for and can't find) |

## B. Events and conversions
| # | Check | Where to look | Red flag |
|---|---|---|---|
| 6 | E-commerce events present | Reports → Engagement → Events | Missing funnel steps, or `refund` = 0 when you do issue refunds |
| 7 | `purchase` parameters | Explore → Free form: Transaction ID × Purchase revenue | `(not set)` transaction IDs, $0 orders, wrong currency on foreign orders |
| 8 | Duplicate purchases | Ecommerce purchases ÷ Transactions | Ratio above 1.00: revenue and ROAS are overstated |
| 9 | Key events | Admin → Key events | Page views or test events marked as key events: "conversions" inflated, Ads bids on browsers |
| 10 | Custom definitions documented | Admin → Custom definitions + your tracking plan | Dimensions nobody can explain; no tracking plan |
| 11 | Live event test | Browser DevTools → Network → filter `collect`; click through the whole funnel | An event missing, firing twice, or sent without item / value / currency |

## C. Attribution and consent
| # | Check | Where to look | Red flag |
|---|---|---|---|
| 12 | Channel grouping / UTMs | Reports → Traffic acquisition | *Unassigned* above ~2%; campaign names in the *source* field |
| 13 | Consent Mode v2 | Admin → Data collection → consent settings; live test with cookies rejected | Signals inactive, or analytics hits sent after a visitor refused cookies (GDPR risk) |
| 14 | Tag inventory | DevTools → Network: count the GA4 (G-), UA- and Ads (AW-/DC-) IDs on one page | Several GA4 properties, or legacy Universal Analytics still loading: which number is the truth? |
| 15 | Ads conversions | Google Ads conversion actions vs where they fire | A "checkout" or "purchase" conversion that fires before that step happens |

## D. Integrations and identity
| # | Check | Where to look | Red flag |
|---|---|---|---|
| 16 | Google Ads link | Admin → Product links | Not linked (no cost data), or linked while key events are wrong (check 9) |
| 17 | Search Console link | Admin → Product links | Not linked: organic queries invisible |
| 18 | BigQuery export | Admin → Product links → BigQuery | Off: no raw data, history lost after the retention period |
| 19 | Reporting identity | Admin → Reporting identity | *Blended* without working consent mode, or no User-ID for logged-in customers |
| 20 | Back-end reconciliation | GA4 revenue vs your shop's order system, same 30 days | A gap above ~5–10% with no explanation |

## What the full audit adds
Running the checklist tells you *what* is broken. The paid audit (5 working days) adds:
- the evidence for each check
- a See-Think-Do-Care KPI framework
- a funnel diagnosis by device with the leak sized in €
- a one-page "So what?" plan with 3 actions, an owner and an expected impact for each
- a 30-minute walkthrough call

**What I need from you:** Viewer access to GA4 (Editor for the filter checks), your order totals for the last 90 days, and 15 minutes with whoever manages the website tags.

*Case study: [Auditing the Google Merchandise Store](./README.md)*
