# journey-code

Static site + design system for Journey's waitlist landing page and product
concept prototype. See `DESIGN-SYSTEM.md` for the tokens.

- `index.html` — the live page. Primary CTA is a paid $49 seat
  reservation via a Stripe Payment Link; the free Formspree waitlist is kept as
  a de-emphasized fallback so you still capture the soft signal.
- `thanks.html` — post-payment landing page. Restates the refund promise and
  pushes the buyer straight into booking their 45-minute call.
- `terms.html` — refund policy and terms. Stripe's account review expects this
  to exist for a pre-sale.
- `journey-prototype.html` — interview walkthrough artifact, not shipped.
- `design-tokens.css`, `base.css` — shared design system.
- `founder.jpg` — photo used in the landing page's founder section.
- `STRIPE-SETUP.md` — **read this before deploying.** Three placeholders and one
  date must be replaced or the pay button stays disabled by design.

## Before you deploy

| Placeholder | File |
|---|---|
| `REPLACE_WITH_PAYMENT_LINK` | `index.html` |
| `REPLACE_WITH_BOOKING_LINK` | `thanks.html` |
| `REPLACE_WITH_CONTACT_EMAIL` | `terms.html` |
| `October 31, 2026` (refund deadline — confirm or change) | all three |

A guard script disables the pay button and shows a red warning if the Stripe
placeholder is still in place, so a half-configured page cannot take money or
send anyone to a 404.

## Where this lives

Repo: <https://github.com/Ikigai888/Journey-Project>

**Cowork cannot push to it.** The Cowork sandbox has no GitHub credentials, so
anything Claude builds here has to be moved across by you, one of two ways:

1. **GitHub web upload** (what the July 28 commit used). Go to the repo, Add
   file → Upload files, drag them in, commit. Uploading a file at a path that
   already exists replaces it. Fastest for a handful of files.
2. **Claude Code on your Mac**, which does have git access. Point it at the
   local clone and tell it what to commit.

## A note on iCloud

If you keep a local clone, put it outside iCloud Drive. As of 2026-07-26 this
Mac has Optimize Storage on and roughly 99% of the surrounding `Tad-OS` folder
is evicted to cloud-only. A `.git/` directory is hundreds of small files that
git expects to read instantly; eviction and sync races are how an index gets
corrupted.

```bash
mkdir -p ~/Developer
git clone https://github.com/Ikigai888/Journey-Project.git ~/Developer/journey-code
cd ~/Developer/journey-code
```

Treat **GitHub**, not iCloud, as the sync mechanism for this code. The rest of
`Project-Journey/` (the markdown, the strategy docs) stays in iCloud — that
material is small, text-only, and benefits from sync.

## Suggested `.gitignore`

```
.DS_Store
node_modules/
```

(Nothing else to ignore yet — this is still plain static HTML/CSS.)
