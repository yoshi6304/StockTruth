# StockTruth: a stock reliability layer for NOVA CART

PromptWars Business Rescue Challenge. **Live demo:** [ADD LINK] | **Code:** this repo

## Problem diagnosis
NOVA CART is growing in volume, not in quality. Orders are up 23% but promo spend is up 79%, so each ₹1 of promo now returns ₹1.54 of revenue (was ₹2.29). Repeat purchase fell from 41% to 27%.

Evidence that the cause is unreliable stock, not weak marketing:
- 61% of customers who stopped ordering had rated the app 4★ or higher. They had a bad order, not a bad app.
- Only 31% of customers place a second order, but those who reach a third have a 72% chance of staying.
- Product unavailable (35%) plus store rejection (18%) is 53% of cancellations. That is about 5.8% of all orders.
- 29% of customers saw items become unavailable after ordering, and 19% of support tickets are missing products.
- 39% of partner stores say updating stock online is too much work, and 18% may leave.

The loop: stale stock data, then cancellations, substitutions and refunds, then a ruined first or second order, then churn, then more paid acquisition to replace the lost users.

## What it does
0. **How the simulation works:** a clock advances in the top bar. Customers buy items, so hidden shelf stock falls while the app's information gets older. A store "Check shelf" reveals the truth. A customer order on an item that is really gone is cancelled after payment, as it would be in real life.
1. **Diagnosis:** the evidence charts, plus each executive's claim tested against the case data, ending in the root cause.
2. **Store dashboard:** an operations view ranks stores by lost-sales risk (computed live, not invented), and every item has a confidence score for being in stock. Stores confirm with one tap, or confirm all at-risk items at once. A "fix these first" queue ranks items by lost-sales risk, so a few taps remove most of the damage. A demand box shows what customers searched for and could not find.
3. **Customer cart:** each item shows a reliability badge, and one click on "Rescue my order" swaps every risky item to a safer store and shows the before and after. Risky items offer a switch to a more reliable nearby store. First-time customers get stricter checks. The cart shows the chance the whole order arrives complete.
4. **Live day:** the same 600 orders run side by side, without and with StockTruth, so the effect is visible in seconds.
5. **Business impact:** an adjustable simulator using the case's own numbers, with payback and 12-month return on the rollout cost.
6. **Data proof:** the score is tested on 5,000 orders. It never sees the cancellation outcome.

## Score
`confidence = 99 x exp(-0.03 x hours_since_update x (1 + sales_per_day/20)) x (1 - store_rejection_rate)`, clamped to 5-99.
Fast-selling items go stale sooner, and stores with a rejection history are penalised. The weights are prototype assumptions to be tuned on real data.

## Data proof (synthetic, calibrated to the case)
| Band | Orders | Cancelled for stock |
|---|---|---|
| High (75%+) | 3,686 | 2.1% |
| Medium | 963 | 9.8% |
| Risky (<50%) | 351 | 35.0% |

The data is synthetic: 5.8% overall cancellation (as in the case), stores updating every 4 to 30 hours, stock-outs simulated from shelf units and sales speed. It proves the scoring logic and sizes the impact. It does **not** prove real-world results. The Data proof tab accepts any CSV with the columns `hrs_since_update, velocity_per_day, store_rejection_rate, cancelled_for_stock`, so NOVA CART can test it on its own orders.

## Business impact (defaults: 60% of stores adopt, 60% of stock cancellations avoided)
About 800 orders rescued per month, about ₹3.9L of order value protected, about 400 fewer support tickets. NOVA CART's revenue is about 14% of order value, so the net revenue gain is smaller than the order value protected. The simulator shows both.
Cost: software only. At an assumed ₹10L rollout cost against the ₹25L cap, payback is about 7.5 months (adjustable in the app). Promo savings and the retention effect beyond the repeat-order assumption are not counted.

## Run it
Open `index.html` in any browser. There is nothing to install.
To deploy: push to GitHub, then Settings, Pages, Deploy from branch `main` (root).
To regenerate the data: `pip install numpy && python generate_data.py`

## Demo (90 seconds)
1. Diagnosis tab: show the cancellation reasons and the executive scorecard (20 seconds).
2. Store tab: pick Sri Lakshmi Provisions and show the risky items.
3. Customer tab: add Toned milk from that store with "First-time customer" on, then show the warning and the store switch.
4. Store tab: tap "Confirm all", go back to the cart, and show the score has risen.
5. Data proof tab: show the three bands and move the slider.
6. Business impact tab: change the assumptions.

## Pilot plan
Phase 1: 4 weeks, 100 stores (the same number NOVA CART interviewed), 50 using StockTruth and 50 as a control group, using the same CSV columns from NOVA CART's order database. Phase 2: all 620 stores. Phase 3: all three cities. Measure stock-related cancellation rate, refund tickets per 1,000 orders, second-order rate within 30 days, and store update effort.
