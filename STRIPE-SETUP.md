# Stripe pre-sale setup

Twenty minutes of dashboard work. No backend, no serverless function, no npm.
The page stays a static file on Vercel and the pay button is a plain link.

**Nothing here works until you replace three placeholders.** They are listed at
the bottom. The page will refuse to ship a dead pay button — the guard script in
`index.html` disables it and shows a red warning if the Stripe link is
still `REPLACE_WITH_PAYMENT_LINK`.

---

## 1. Create the Stripe account

<https://dashboard.stripe.com/register>

You are a **sole proprietor / individual** in British Columbia, Canada. You need
a SIN, a Canadian bank account for payouts, and your address. There is no
incorporation requirement and no monthly fee.

Set the account currency to **CAD**. Everything downstream inherits it.

While you're in there, fill in **Settings → Business → Public details**: business
name (Journey), support email, and the URL of your terms page. Stripe reviews
new accounts and a pre-sale with no visible refund policy is the single most
common reason a new account gets a hold. `terms.html` exists for this.

## 2. Create the product

**Product catalogue → Add product**

| Field | Value |
|---|---|
| Name | Journey — Cohort One, Month One |
| Description | Reserved seat in cohort one, plus the first month of the program. Fully refundable. |
| Price | 49.00 CAD |
| Type | **One-time** |

**One-time, not recurring.** Do not create a subscription. A subscription would
start billing on a schedule for a program that does not exist yet, which is both
a support problem and a chargeback problem. Month two gets sold separately, by
you, in an email, after month one has actually happened.

## 3. Create the Payment Link

**Payment Links → New → select the product above.**

Then, in Options:

- **Collect customer email** — on. This is your list.
- **Collect billing address** — on. Stripe needs it for card verification and
  you need it for GST thresholds later.
- **Limit the number of payments to 12.** This is not a marketing gimmick — the
  page says twelve seats, so the link must actually stop at twelve. Stripe will
  close it automatically.
- **Custom fields** — add one, so paid signups give you the same data the free
  waitlist form does:
  - Type: Dropdown
  - Label: `How long since your layoff?`
  - Options: `Less than 2 weeks` / `2–8 weeks` / `2–4 months` / `More than 4 months` / `Prefer not to say`
  - Optional: yes

  Answers appear on the payment detail page in the dashboard.

- **After the payment → Redirect customers to your website:**
  `https://YOUR-DOMAIN/thanks.html?session={CHECKOUT_SESSION_ID}`

  `{CHECKOUT_SESSION_ID}` is a literal Stripe token, typed exactly like that.
  Stripe substitutes it. `thanks.html` ignores it today, but having it in the
  URL means you can read it later without recreating the link.

Copy the resulting `https://buy.stripe.com/...` URL.

## 4. Wire it up

In `index.html`, find `id="reserve-btn"` and replace the placeholder
href with your live link. Deploy. Click your own button and pay yourself $49 in
**test mode first** (toggle Test mode in the dashboard, use card `4242 4242 4242
4242`, any future expiry, any CVC). Confirm you land on `thanks.html`. Then
switch to live and create the link again — **test links do not work in live
mode.**

---

## What this costs you

Roughly **2.9% + $0.30** on a domestic Canadian card, so about **$1.72 on a $49
charge** — you net around **$47.28**. Confirm the current rate at
<https://stripe.com/en-ca/pricing>.

**Stripe does not return the processing fee when you refund.** If cohort one
doesn't run and you refund all twelve seats, you are out roughly **$21 total**
and the customers are made whole. That is a cheap price for a clean promise, but
know that you are paying it, and don't be surprised by it.

## The tax question, briefly

You do not have to register for GST/HST until you cross $30,000 in revenue over
four consecutive quarters. Twelve seats at $49 is $588. You are nowhere near it.
Revisit if this becomes real. This is not tax advice — talk to an accountant
before cohort three.

## The consumer-protection question, briefly

BC's Business Practices and Consumer Protection Act governs prepayments for
future services. Your exposure here is low because the refund terms are
unconditional, in writing, and more generous than the statutory floor. Keeping
that promise literally — refunding automatically on October 31 without being
asked — is what keeps it low. This is not legal advice; if this scales past a
handful of cohorts, get a lawyer to read `terms.html`.

---

## Test mode vs live mode — the one that can silently ruin the experiment

A Stripe link that looks like `buy.stripe.com/**test_**...` is a **test link**.
It renders a real-looking checkout, accepts test cards, and collects **zero
dollars**. Real customer cards are declined.

The link currently wired into `index.html` is a test link:

```
https://buy.stripe.com/test_00w9AS9tj4TZ2Z7fjpcV200
```

This is the correct thing to have right now — use it to walk the whole flow
end to end. But if the page were deployed with it, the smoke test would score
zero paid seats no matter how many people tried to buy, and the result would be
indistinguishable from nobody wanting it. That is the worst possible failure
mode: a false negative on the criterion the whole project turns on.

So the page defends against it. While a `test_` link is wired in, a red
**STRIPE TEST MODE** bar is pinned to the top of every page view and a note
appears under the button. You cannot deploy it and not notice.

**To go live:** flip the dashboard toggle from Test mode to Live mode and create
the product and Payment Link again. Test-mode objects do not carry over — a
live link is a different URL with no `test_` in it. Paste it over the test link
and the red bar disappears on its own.

## Pre-launch checklist

- [ ] Walked the full flow in test mode with card `4242 4242 4242 4242`
- [ ] Landed on `thanks.html` after paying
- [ ] Stripe account approved for live payments
- [ ] Live Payment Link created (**no `test_` in the URL**) and pasted in
- [ ] Red TEST MODE bar is gone
- [ ] Booking link set in `thanks.html`
- [ ] Refund date confirmed in all three files
- [ ] Made one real $49 purchase yourself, then refunded it, to prove refunds work

## Placeholders you must replace before deploying

| File | Placeholder | Replace with |
|---|---|---|
| `index.html` | `REPLACE_WITH_PAYMENT_LINK` | Your live `buy.stripe.com` URL |
| `thanks.html` | `REPLACE_WITH_BOOKING_LINK` | Your Cal.com or Calendly link |
| `terms.html` | `REPLACE_WITH_CONTACT_EMAIL` | The email you'll actually answer |

`thanks.html` hides the booking button entirely if its placeholder is still
there, so the page degrades quietly rather than shipping a broken link.

## Dates you must confirm before deploying

**October 31, 2026** appears in `index.html`, `thanks.html`, and
`terms.html` as the automatic-refund deadline. It is a placeholder chosen to sit
comfortably after your August 29 kill-criteria decision. Pick the real date and
change it in all three files, or don't ship. A refund deadline you haven't
actually committed to is worse than no deadline at all.

## Changing the price later

You mentioned $30 may test better than $49. When you want to change it:

1. Create a **new** price in Stripe and a **new** Payment Link. Do not edit the
   old one — you want the old link intact so the two cohorts stay comparable.
2. Update the four places the price appears in `index.html`
   (`grep '\$49' index.html`). The comment above the pricing section
   lists them.
3. Update the `$49 CAD` line in the "Price and billing" section of `terms.html`.

Do not change it mid-test. If you move the price after four sales and before
eight, you have two half-tests instead of one result.
