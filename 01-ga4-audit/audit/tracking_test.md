# Live tracking test: is `add_to_cart` firing? (Defend-it Q1)
**Site:** shop.merch.google · **Date:** Sat 31 Oct 2026 · **Method:** browser network inspection (GA4 `/g/collect` hits + `dataLayer`) + João's HAR export of the home page
**Consent state during the test:** cookie banner not accepted; OneTrust cookie `groups=C0002:0` (performance cookies **off**), `C0001:1` (strictly necessary on)

## Result per funnel step
| Step | Event expected | Fired? | Item data (`pr1`) | currency + value | Notes |
|---|---|---|---|---|---|
| Home page | page_view | ✅ | n/a | n/a | Fires to 8 GA4 properties, plus `view_promotion` |
| Category page (Drinkware) | view_item_list | ✅ (dataLayer) | ✅ | | No quick-add button on listings: the only add-to-cart path is the product page |
| Product page (Google Mega Mug) | view_item | ✅ once per property | ✅ id, name, brand, category, price | USD 18 visible on 1 of 7 hits | dataLayer push complete (currency, value, items) |
| Product page → Add to Cart | add_to_cart | ✅ **exactly once per property, per click** | ✅ | USD 18 visible on 2 of 7 hits | dataLayer push complete; no duplicates |
| Cart drawer → Checkout | view_cart | ✅ | ✅ | | Fires on the cart page, plus `ads_conversion_Checkout_1` (Ads conversion) |
| Cart → checkout | begin_checkout | 🔒 not reached | | | **Checkout requires a Google sign-in.** I stopped at the login wall |

## Verdict on the 74% view_item → add_to_cart drop
**Behaviour, not tracking.** `add_to_cart` fires once per click, with full item data, from the only add-to-cart entry point (the product page). A tracking gap would show as a missing or partial hit, and none was found. Together with check 6 (counts fall at every step) and check 8 (no duplicate purchases), the drop is real user behaviour.
*Limit:* this was tested on one browser (desktop Chromium). A mobile-specific bug can't be ruled out without repeating the test on an iPhone and an Android phone.

## Extra findings (go into CS1)
1. **Tag sprawl:** every page sends hits to **8 GA4 properties** (G-XR6MYBBELZ, G-MH4QR9F4ZK, G-84QGGXTE9Y, G-PSW1MY7HB4, G-JPEV636QX5, G-BELJRQ5S8R, G-RZ7VVEX4ES, G-NS7M4ZTP59), a **Universal Analytics** tag (UA-54516992-1, which stopped processing data in July 2024) and **8 Google Ads / Floodlight tags**, loaded through 2 GTM containers. Currency and value are visible on only some of the hits, so the properties can disagree on revenue.
2. **Tags fire with performance cookies off:** hits were sent while the OneTrust performance group was not granted. The first hits carry no `gcs` consent parameter; later ones carry `gcs=G1--` (consent mode present, no granted/denied state). This links to check 12 and needs a legal review.
3. **Test events in production:** G-XR6MYBBELZ receives `TESTstreams`, `booking_confirmation` and `custom_event_with_a_name_over_40_charact…` on a merch store (governance, checks 9–10).
4. **Forced Google sign-in before checkout:** this is a known source of checkout friction, and a possible contributor to the mobile checkout leak (task 3). Hypothesis only; test it with a guest-checkout A/B test.
5. **An Ads "Checkout" conversion fires on the cart page view**, before sign-in, so Google Ads counts a checkout conversion for people who never start checkout.

**To confirm in GA4:** Admin → Data streams → Measurement ID, to find which of the 8 IDs is the audited demo property.
