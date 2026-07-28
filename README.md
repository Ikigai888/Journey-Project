# journey-code

Static site + design system for Journey's waitlist landing page and product
concept prototype. See `DESIGN-SYSTEM.md` for the tokens.

- `journey-landing.html` — the live waitlist page (form posts to Formspree).
- `journey-prototype.html` — interview walkthrough artifact, not shipped.
- `design-tokens.css`, `base.css` — shared design system.
- `founder.jpg` — photo used in the landing page's founder section.

## Move this out of iCloud first — then push to GitHub

**Do this before `git init`, not after.** This folder currently sits inside
iCloud Drive, and iCloud Drive on this Mac has Optimize Storage on — as of
2026-07-26, roughly 99% of the surrounding `Tad-OS` folder is evicted to
cloud-only (26 GB apparent, 34 MB actually on disk). A `.git/` directory is
hundreds of small files that git expects to read instantly; eviction and
sync races are exactly the conditions that corrupt an index. Initializing a
repo here invites a problem that's annoying to unwind later.

Move the folder to a normal local path first:

```bash
mkdir -p ~/Developer
mv ~/Library/Mobile\ Documents/com~apple~CloudDocs/Tad-OS/Project-Journey/journey-code ~/Developer/journey-code
cd ~/Developer/journey-code
```

Then treat **GitHub**, not iCloud, as the sync and backup mechanism for this
code. The rest of `Project-Journey/` (the markdown, the strategy docs) stays
in iCloud — that material is small, text-only, and benefits from sync.

## Push to GitHub (run locally, via Claude Code)

This folder was built in Cowork, which doesn't have GitHub access from its
sandbox. Claude Code, running on your Mac, does — so finish the connection
from there. From a terminal in the moved folder (or tell Claude Code to do it):

```bash
git init
git add .
git commit -m "Journey landing page + design system"

# if you have the GitHub CLI (gh) authenticated:
gh repo create journey-code --private --source=. --remote=origin --push

# otherwise: create an empty repo at github.com/new (no README/license),
# then:
git remote add origin https://github.com/<your-username>/journey-code.git
git branch -M main
git push -u origin main
```

After that, this folder is a normal git repo — Claude Code can branch,
commit, and push from it like any other project, and you can clone it on
any machine.

## Current status

**Not a git repo yet.** This folder has a `.gitignore` but no `.git/`
directory — the steps above have not been run. `Project-Journey/CLAUDE.md`
previously described it as a standalone repo; that line has been corrected.

## Suggested `.gitignore`

```
.DS_Store
node_modules/
```

(Nothing else to ignore yet — this is still plain static HTML/CSS.)
