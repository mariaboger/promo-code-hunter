---
name: promo-code-hunter
description: "Find working promo codes and discounts for any shop, brand or product link: creator/influencer codes, checkout-tested codes, and for big retailers (Amazon, H&M, MediaMarkt) gift-card deals, gifts/samples, app/first-order offers and account perks. Use whenever the user asks how to pay less or mentions: promo code, coupon, voucher, discount code, deal, sale, cashback, gift card, free samples, Gutschein, Gutscheincode, Rabattcode, Rabatt, Aktionscode, Neukunden, Angebot, Aktion, Geschenkkarte, Gratisprobe."
---

# Promo code hunter

The user wants to know **what actually works today on their basket and what it really gives**. Collecting a list of codes is easy. The value is in knowing which ones work.

Lessons from testing on real shops (October 2026):
- **Coupon sites state wrong amounts.** On HOLY, three codes listed as "15%" gave 10% at checkout.
- **Coupon sites list dead codes.** The top pick of two research-only runs was rejected at checkout.
- **Banner codes can be scoped.** The shop's own banner code (ESN) was accepted but gave 0 € on protein, because it only covered one category.
- **The checkout is the source of truth,** and the browser can show things search can't: hidden mydealz codes, and the shop's own discount config.

Reply in the user's language. Default market: Germany (.de shop, EUR) unless the link or the user says otherwise.

## 0. Mode and budget

**Browser mode.** Use it if the built-in browser is available (otherwise Claude in Chrome). Run the full flow, steps 1–5. A request for working codes is consent to try codes in the voucher field (step 4). Nothing past the voucher field.

**No-browser mode.** Use it if there is no browser. Do steps 1–3 with WebSearch/WebFetch, then hand the user an ordered "try these" list instead of step 4. Skip guesses (2F): they are only worth anything when tested. Say in one line that the codes weren't checked at checkout, and offer to test them if a browser becomes available.

**Budget:** about 25 research calls (searches + fetches), about 35 for big retailers (2J), plus up to 20 code attempts in browser mode. Run independent searches in parallel.
- The call budget is the hard cap. The stop rules below apply to code hunting only:
  - stop when you have about 5 credible candidates;
  - stop after about 6 searches in a row that add nothing new.
- Keep part of the budget for the price check (step 1) and the non-code levers (2G–2J).
- Don't guess URLs on third-party sites. Each guess cost a 404 in testing. Find pages through search or the site's own search box.

## 1. Target and basket

- **Shop:** the official shop for the user's country. de.brand.com and brand.com often differ, and codes are often country-specific: a code posted for fr.holy.com may fail on de.holy.com.
- **Basket:** the user's product, or a typical full-size product from the category they named. With no product given, test two items: one full-price item and the shop's main entry bundle or starter set.
  - Codes behave differently on them. On HOLY, the 5 € codes made the starter set 20% cheaper, while the 10% creator codes gave only 2,49 € there.
  - Note whether the item is already reduced or part of a campaign.
  - Add items with the shop's own "add to cart" button; bundle builders reject direct cart API calls.
- **Ambiguous basket** ("a jacket for ~80 €", or several models matching "~350 €"): pick a representative item, say which one you assumed, and ask for the exact model in one line at the end. Don't stop to ask first.
- **Price and seller.** Find today's price and **who sells it**.
  - On MediaMarkt, Otto, Kaufland and Amazon, items from marketplace sellers usually get no points, no cashback and no price match. Warn the user when that's the case.
  - Without a browser: WebFetch the product page; this worked for MediaMarkt, but Amazon blocks it. If that fails, anchor on the latest *dated* price from deal articles and say so.
  - With a browser: also check geizhals.de or idealo.de for competitor prices and price history.
  - Without a browser these are blocked (robots.txt). Try competitors' product pages via search and WebFetch (otto.de, galaxus.de, cyberport.de). Otherwise make "check idealo / Keepa" a 👤 step for the user.
- **Multi-brand retailers** (ABOUT YOU, Zalando, Otto, Douglas, Flaconi, etc.): what decides is the code's **brand exclusion list** (luxury and niche perfume brands are typically excluded at Douglas), the minimum order value (MBW), and the rule for reduced items. Find the current conditions and check the user's brand against them.
  - Example: ABOUT YOU codes don't apply to "DEAL" prices but do apply on top of SALE prices.
  - If the brand is unknown, end the reply with one line asking for it.
- **Platform:** in the browser, check `!!window.Shopify`. Shopify shops accept codes from guests. Big custom shops (ABOUT YOU, Zalando, Otto…) often need a login before the voucher field appears.
- **Timing:** campaigns that end or start soon decide whether to buy now or wait. Check shop countdown banners, and "ab morgen" / "Vorschau" announcements on deal and drop sites.
  - **Always check whether a store-wide event is running today or this week.** Search `"<store>" Aktion <month year>` and `mydealz "<store>"`. Examples: Prime Deal Days, Black Week, Singles Day, "Mehrwertsteuer geschenkt", Mid Season Sale. In testing, this was the single most important finding for Amazon and MediaMarkt.
  - Label a third-party announcement as unconfirmed until the shop shows it.
  - Check the **year** of event articles. Prime Days, Black Week and similar recur every year, and old articles look current.
  - Briefly note a campaign that **just ended**. Stores repeat them, so it helps the user decide whether to wait.

## 2. Collect candidates

**How to rank evidence:**
1. The shop shows it today.
2. A third party shows a recent timestamped check (a mydealz editor check on a *code* card, droptime "geprüft am …"). "Zuletzt genutzt vor X Min." on offer cards without a code looks automated; don't count it.
3. A creator posted it in the last ~60 days.
4. A single aggregator lists it, or it is old.

Rules for counting and trusting sources:
- One code copied onto many aggregators counts as one source.
- A coupon or review site's *own* partner code (e.g. ALUCARE from alucare.fr) ranks as aggregator-level.
- Ignore auto-generated "verified today" stamps and success rates.
- Never trust the claimed discount amount. If sources disagree, show the range ("10–15%, sources disagree").

**A. The shop**
- Look at the announcement bar, banners, the sale/promo page and the newsletter popup.
- Look for first-order, newsletter, **app** (first app order, app-only codes), refer-a-friend, member/loyalty, birthday, subscription and bundle offers. Automatic offers ("Kaufe 2, spare 10%") are often the best deal. List them too.
- Report first-order, newsletter and app offers **even if you can't verify them.** Usually the code only arrives after an email sign-up or inside the app, and that step is the user's to take if it's worth it to them. Say where the info comes from and what the user needs to do ("−10% on the first app order, per the shop's FAQ: install the app and enter the code at checkout").
- Search: `"<store>" Neukunden Rabatt`, `"<store>" App Rabatt erste Bestellung`, `"<store>" Newsletter Gutschein`, `"<store>" Member Rabatt`.
- **Browser only:** read the shop's discount config.
  - Many Shopify themes ship a JS object with campaigns, scopes and end dates (see the snippet in step 4).
  - It tells you what a code covers before you test it. On ESN it showed that every creator code gave the same 10%, so hunting for more creator codes was pointless.
  - **Hands off anything the config marks private/internal,** and off staff, employee, corporate, partner-event or test codes (e.g. `"type":"private"`, "Corporate", "…test…"). Don't test them and don't report them, even if they give 40%. They aren't meant for the public, and using them can get orders cancelled.

**B. Deal communities**
- **mydealz.de**: find the shop's voucher page via search (`site:mydealz.de/gutscheine <brand>`), or with the browser's on-site search.
  - For big shops, the slug is usually the shop domain with a dash (`amazon-de`, `mediamarkt-de`, `douglas-de`, `hm-com`, `aboutyou-de`). One try is fine; for small brands it often 404s, so don't guess further.
  - Editors check the top codes, so mydealz is often the best freshness signal.
  - Codes are hidden behind "Code anzeigen". In the browser: click it (the tab jumps to the shop), then reopen the mydealz page with the `#voucher-<id>` hash from that URL. A popup shows the code and its "Gutscheindetails".
  - Without a browser:
    - Search the card title in quotes (`"<brand>" "15% auf alles" code`). Other sites sometimes print the same code in plain text.
    - Otherwise, link the exact page and tell the user which voucher card to open ("15% auf alles", checked today).
- Deal threads and their comments ("geht nicht mehr", "nur Neukunden") are strong signals.

**C. Creator and influencer codes** (the reason this skill exists)
- WebSearch barely indexes Instagram or TikTok, and WebFetch is blocked there by robots.txt. Use **one** `site:instagram.com` query at most.
- These searches worked. They surface creator-code pages such as influencercodes.de (`/marken/<brand>/`), **influencercodes.hotdeals.com** (`/<brand>`), de.hotdeals.com and droptime.de (fitness and supplement brands):
  - `<brand> Rabattcode Streamer Code <year> <shop domain>`. Use **extended** search mode for this one; it was the only productive query for HOLY.
  - `"<brand>" rabattcode influencer`
  - `"<brand>" influencer code liste`
  - `"<brand>" "mit dem code"`
  - `"<brand>" code youtube`, since YouTube video descriptions are sometimes indexed.
- Expect gaps: each of these sites covers only some brands.
- If you know who the brand's partners are (CreatorDB, or a shop "Partner" page), run **one** combined query: `"<brand>" code (creatorA OR creatorB OR creatorC)`. Don't search names one by one.
- **Browser only:** if the user is logged in to Instagram, open the brand's **tagged** posts and scan recent captions. Read only: never like, follow, comment or send messages.
- **Learn the brand's code format,** then use it to judge leads and to build guesses. Examples: HOLY uses `CREATOR` for 10% and `CREATOR5` for 5 € off a first order. ESN has one rotating campaign code plus creator codes. ABOUT YOU uses `15NAME` / `NAME15`.

**D. Stackable extras**
- Cashback portals (Shoop, iGraal, TopCashback) and Payback / DeutschlandCard. Cashback may not be credited when a code from elsewhere is used.
- Outlet sections (gift-card promotions: see 2H).
- Price history: is today's price good?

**E. Skip** personal referral IDs (`LL10-QPW12BRW`-style), other people's single-use newsletter codes and expired offers. List expired codes the user is likely to stumble upon in one line ("don't bother: …").

**F. Guesses** (browser mode only, at most 10). Build them from:
- the brand's format (SUMMER20 → HERBST20, BF2025 → BF2026);
- the current event (BLACKFRIDAY, SINGLESDAY, XMAS, with 10/15/20 and the year);
- a few welcome codes (WELCOME10, NEW10, `<BRAND>10`).

**G. Free gifts and samples.** These are often the only "code" a big beauty or drugstore shop has. Look for:
- gift-with-purchase codes ("Geschenk ab 49 €", "GRATIS" codes);
- free samples you can pick at checkout;
- welcome or birthday gifts in the loyalty program;
- seasonal gift sets.

Search `"<store>" Gratisprobe Code`, `"<store>" Geschenk zur Bestellung Gutschein` and `"<store>" Gratis Zugabe`. List them in their own line, with the minimum order and the date.

**H. Discounted gift cards for this store.** Buying the store's gift card cheaper is a real, stackable discount, and often the only one at big retailers. Look for:
- the store's own gift-card bonus ("Geschenkkarte kaufen, 10 € Bonus");
- supermarket and drugstore promos on this store's cards (Rewe, Edeka, Penny, dm, Kaufland, Lidl, Aral), such as "10% zurück als Einkaufsgutschein" or Payback multiplier points (10×/20×);
- Amazon top-up bonuses ("Guthaben aufladen, Bonus erhalten");
- perk portals selling cards at 3–16% off: Telekom Magenta app, O2 Priority, Corporate Benefits, etc. (2I).

Search `"<store>" Geschenkkarte Rabatt <month year>`, `"<store>" Gutschein günstiger kaufen` and `mydealz "<store>" Geschenkkarte`. Report the promo, the place, the end date and the effective saving. Gift cards usually stack with codes and member discounts. Say so when the store's conditions confirm it.

**I. Perks behind the user's accounts.** You can't check these, but the user can in a minute. Search which programs currently list *this* store, e.g. `"<store>" Magenta Moments Telekom`, `"<store>" Geschenkkarte corporate benefits`, `"<store>" studentenrabatt` (also UNiDAYS, Student Beans, iamstudent). Add "Telekom", "Geschenkkarte" or "mydealz" to the query: a bare `"H&M" Magenta` returns colour and fashion noise. Then tell the user which ones to check. Typical programs:
- **Mobile and internet providers:** Telekom **Magenta Moments** in the Magenta app (personal codes, often 10–25%, plus discounted gift cards), O2 **Priority**, Vodafone.
- **Employer portals:** Corporate Benefits, Mitarbeiterangebote.de, BENEFITS.me and similar (if the employer offers one).
- **Student platforms:** UNiDAYS, Student Beans, iamstudent, ISIC.
- **Loyalty and payment:** Payback eCoupons (coupons activated in the app), DeutschlandCard, Amex Offers, bank or credit-card shopping portals, ADAC Vorteilswelt, Miles & More.
- **The store's own membership** (member price, member-only code, app coupon), Amazon Prime / Prime Student.

Name a program only if it fits this store, or if you found evidence that it currently lists the store or has listed it before. A perk that recurs but isn't active now gets one clause ("Magenta Moments had H&M cards at −5…14% until 31.07; check now and then"). Give the exact click path where known ("Magenta-App → Magenta Moments → search 'H&M'"). Use the user's known circumstances (e.g. a Telekom contract, student status) only to rank this list. Don't speculate about accounts the user may not have.

**J. Big retailers** (Amazon, MediaMarkt/Saturn, H&M, Zara, Zalando, Otto, IKEA, Douglas, Lidl/Aldi online, etc.). Public codes are rare. Many coupon-site "codes" are fake, generic or newsletter-only. So:
- **Start with the store's mydealz voucher page.** It usually lists the store's member, app, newsletter, cashback and gift-card offers in one place.
- Spend at most ~5 calls on code hunting. **Don't guess codes at big retailers:** their real codes are personal (newsletter, app, account), and guesses only burn attempts.
- Spend the rest on the levers that do exist:
  - member, app and newsletter offers (2A);
  - discounted gift cards (2H);
  - account perks (2I);
  - free gifts and samples (2G);
  - cashback;
  - running campaigns and price history;
  - **price match** (MediaMarkt/Saturn "Preisversprechen", etc.). Read its current terms: the competitor list, delivery time and stock rules, in-store only, once per customer. Then name the cheapest competitor that actually **qualifies**. In testing, a cheaper shop with 7–14 days delivery did not qualify;
  - **manufacturer promotions:** brand cashback, a free gift with purchase, a registration bonus or an extended warranty (`"<brand>" Cashback Aktion <year>`, `"<brand>" Gratiszugabe`). For appliances and electronics, these are often the biggest lever.
- **Amazon:**
  - the product page's own **coupon checkbox** ("Coupon anwenden");
  - "Spar-Abo";
  - "Spare X% beim Kauf von 2";
  - Amazon Warehouse / Second Chance;
  - Prime Student;
  - the gift-card top-up bonus;
  - price history (Keepa or camelcamelcamel);
  - promotions on Amazon gift cards at supermarkets.

  There is no Amazon code box worth guessing into.
- **Electronics** (MediaMarkt/Saturn, Otto, Cyberport): app and newsletter vouchers, the membership account (myMediaMarkt etc.), weekly campaigns ("Mehrwertsteuer geschenkt", "Tiefpreisspätschicht"), price match, gift-card promos, Payback where the store is a partner.
- **Fashion** (H&M, Zara, Zalando, About You): member discounts (with minimum order), first app order, birthday codes, student discounts, and gift cards bought cheaper through Magenta, Corporate Benefits or Payback.
- **Beauty and drugstores** (Douglas, Flaconi, dm, Rossmann): gift-with-purchase codes, free samples, loyalty-card coupons and app coupons.
- **If the store's site is JavaScript-heavy** (hm.com and others return almost nothing to WebFetch), read its help/FAQ pages ("Hilfe", "FAQ Gutschein", "Mitgliedschaft") and the app's store listing instead. Label offers found only on deal sites 🟡.
- In browser mode on big retailers, testing usually stops at the login wall. Don't log in on the user's behalf. Instead, read what's visible: coupon checkboxes, member prices, campaign banners.

## 3. Shortlist

Order the candidates by evidence. Drop codes that have clearly expired. If the config or the conditions already show that a code can't apply to the basket, don't waste an attempt on it; just note it.

## 4. Checkout test (browser mode)

1. **Cookie banner:** decline optional cookies. Some banners appear only after the first click and silently block add-to-cart. If a click does nothing, take a screenshot and check for one. If `find` can't see a banner or popup button, click by screenshot coordinates.
2. **Add the basket item** from step 1.
3. **Find the voucher field.** It can be in the cart drawer, on the cart page or at checkout ("Rabattcode", "Gutschein", "Discount code").
   - On narrow layouts it sits inside the collapsed order summary ("Bestellübersicht"). If `find` returns a voucher ref that is "outside the viewport", the summary is collapsed. Expand it with a click at screenshot coordinates, then run `find` again, because the expanded field gets new refs.
   - On Shopify, `/checkout` works as a guest. No email or address is needed to apply a code.
   - Some conditions, such as "first order only", are only checked after an email is entered. Don't enter one. Report these conditions as "condition not verified".
4. **Enter codes one at a time,** at least 3 s apart. Record for each code:
   - accepted or rejected, and the shop's message;
   - **the real discount** on the basket: the discount line divided by the subtotal;
   - any conditions the shop reveals.

   An accepted code with a 0 € discount means the code is scoped to other products. Report it as ⚠️.
5. **Limits:**
   - At most **20 attempts** per shop and session.
   - Only through the normal voucher field. No discount-API calls, no permutations, no brute force.
   - Stop at the first CAPTCHA or rate-limit message.
   - **Never** go past the voucher step: no account creation, no address or payment details, and never place an order.
   - If the field needs a login, ask the user to log in in the browser themselves, or fall back to the "try these" list.

   Mass guessing is effectively an attack on the shop's checkout. Shops detect it and may cancel orders or block accounts.
6. **Clean up:** remove the code and empty the test cart. On Shopify: `fetch('/cart/clear.js',{method:'POST'})`.

**How to enter a code safely.** Do it step by step with the browser tools, one code per step. A monolithic JS helper misfired in testing: it typed a code into the postcode field and submitted the address form.
1. Use `find` to get the voucher **textbox** and its **apply button**. The textbox is labelled e.g. "Rabattcode oder Gutschein" or "Discount code"; the button "Anwenden", "Apply", "Einlösen" or "Rabattcode nutzen". Check that the textbox is not an address, postcode (PLZ/zip) or email field.
2. Remove the previously applied code (its "entfernen" / "remove" button) and wait about 2 s.
3. Click the textbox and `type` the code. Typing works more reliably than `form_input` on React checkouts. Then click the apply button and `wait` 3 s. A `browser_batch` can do steps 2–4 in one call.
4. Read the result with this read-only snippet:

```js
const t = document.body.innerText;
({msg: (t.match(/[^\n]*(gültig|valid|Mindest|minimum|nicht anwendbar|not applicable|nur für)[^\n]*/i) || [])[0],
  summary: (t.match(/(Zwischensumme|Subtotal)[\s\S]{0,250}/) || [])[0]})
```

Never press Enter in, or submit, any form other than the voucher form. Never click "Weiter", "Absenden", "Continue" or "Pay".

Find the shop's discount config. Run it on the homepage and read only the public campaign parts; skip anything marked private:

```js
const html = document.documentElement.outerHTML;
[...html.matchAll(/["']?(campaigns?|discounts?|influencers?|promotions?|coupons?)["']?\s*[:=]\s*[\[{]/gi)]
  .slice(0, 10).map(m => html.slice(m.index, m.index + 600));
```

## 5. Reply

Keep it short. **First line:** the best option and what it gives on their basket ("DROPTIME: −10%, checked at checkout: 47,90 € → 43,11 €"). If there is no public code (typical at big retailers), say so in a few words, then name the best lever with an estimated price ("No public codes; best: price match in the store with the Galaxus price, ≈ 350 €"). Fill the table with levers instead of codes.

| Code / offer | Discount (real) | Conditions | Source (date) | Status |
|---|---|---|---|---|

Status values:
- ✅ **works**: accepted at checkout today, with the real amount; or the shop itself shows it today.
- ⚠️ **accepted, but 0 € on your basket**: say what the code does cover.
- 🟡 **likely**: a recent timestamped third-party check, or a creator post from the last ~60 days.
- ⚪ **unchecked**: an older post or a single aggregator listing.
- 👤 **your step**: needs the user's own account or action, such as a newsletter or app code, a member welcome offer, a personal Payback/app coupon, a perk portal, or a discount the shop says exists but whose amount only shows after login. Also anything only visible in a browser when you have none (e.g. an Amazon coupon checkbox). Say exactly what to do.
- ❌ **rejected**: a *found* code that failed at checkout. One line under the table, so the user doesn't retry it. Rejected guesses are not shown.

Automatic offers (bundle, "Kaufe 2", sale) can be rows too. Use "—" as the code. Never show a guessed code that wasn't accepted. Mark a working guess "guessed, checked at checkout".

Under the table:
- **Best combination** in 1–2 lines, with the estimated final price incl. shipping. Say what doesn't stack.
- **Timing:** buy now, or wait for a sale that starts soon or ends soon?
- **No-code options,** briefly: first-order, app and newsletter offers, membership, bundles. Say what the user has to do for each.
- **Free gift or samples** (if found): one line with the code or condition and the minimum order.
- **Discounted gift cards** (if found): where, how much and until when, plus whether they stack with the code.
- **Check your accounts:** a short checklist of 2–5 programs that fit this store (2I), each with its click path, e.g. "Magenta-App → Magenta Moments → 'H&M'". Phrase it as "if you have an account there, check…", so the user doesn't have to search manually.
- **No-browser mode only:** "Try in this order: …" with only the real candidates. One or two codes is fine; don't pad the list. If there are no codes at all (typical at big retailers), turn it into "What to do, in order: …", a list of the 👤 steps.
- **If nothing current exists,** say so plainly and point to the best no-code option.
- End with "Sources:" links.

Mention the user's personal circumstances only when they change which discount applies (e.g. student status for a student discount). Say it neutrally, in one clause.

## Known limits (mention only when relevant)

- Codes that appeared only in Instagram stories (24 h) or closed Telegram or WhatsApp groups aren't indexed.
- Personal referral and one-time codes belong to their owner. They aren't public codes.