# promo-code-hunter

A Claude skill that finds **discount codes and savings that actually work today**. It works for any online shop, brand or product link. When a browser is available, it tests the codes at checkout.

## What it does

- **Creator and influencer codes.** It finds codes from Instagram, TikTok, YouTube and podcasts that coupon sites usually miss.
- **Checkout testing.** With a browser, the skill adds the item to the cart and enters codes one at a time. This shows the real discount instead of the number a coupon site claims. It never goes past the voucher field and never places an order.
- **Honest status labels:**
  - ✅ works;
  - 🟡 likely works;
  - ⚪ unchecked;
  - ⚠️ accepted, but 0 € on your item;
  - 👤 needs your action;
  - ❌ rejected.
- **Big retailers without public codes** (Amazon, H&M, MediaMarkt, Douglas, etc.):
  - sales running today;
  - discounted gift cards;
  - free gifts and samples;
  - first-order and app discounts;
  - price match;
  - manufacturer promotions.
- **"Check your accounts" checklist.** Points to perks that only the user can see after logging in: Telekom Magenta Moments, O2 Priority, Corporate Benefits, student platforms, Payback coupons and similar. Each comes with where to look.
- **Tuned for Germany** (.de shops, EUR, mydealz, Shoop, Payback). It works in other countries too, with fewer sources (see Limitations).

## What it won't do

- Mass-guess codes. It makes at most 20 attempts per shop and does not bypass CAPTCHAs.
- Use internal, staff or corporate codes, even if they appear in a site's source code.
- Log into accounts, enter personal data or place orders.

## Installation

**Claude app / claude.ai:** Settings → Capabilities → Skills → upload `promo-code-hunter.zip`. The archive contains the `promo-code-hunter/` folder with `SKILL.md`.

**Claude Code:** copy the `promo-code-hunter/` folder to `~/.claude/skills/`.

## Usage

The skill triggers automatically on questions like:

- "Any promo code for Douglas?"
- "How can I get the Sony WH-1000XM5 cheaper on Amazon?"
- "Gutschein für H&M?"
- a product link plus "any discounts?"

German terms such as Gutschein or Rabattcode also trigger it. You can also call it explicitly: "use promo-code-hunter: …".

It works best in the Claude desktop app with the built-in browser or Claude in Chrome, because only then can it test codes at checkout. Without a browser it searches the web and gives you an ordered list of what to try.

## How it was tested

The skill was run against 8 shops: ESN, ABOUT YOU, HOLY, SNOCKS, Amazon, H&M, MediaMarkt and Douglas. Each run was compared with a baseline without the skill or with the previous version. The test prompts are in `evals/evals.json`.

## Limitations

- Codes shared only in Instagram stories (24 h) or closed chats can't be found.
- Without a browser, current Amazon prices and the codes mydealz hides behind "Code anzeigen" aren't visible.
- Specific sites and tricks change over time, so the skill needs occasional updates.
- Outside Germany it relies on general web search. Local deal sites such as Slickdeals, RetailMeNot and HotUKDeals aren't built in yet.

## License

MIT
