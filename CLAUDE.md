# CLAUDE.md

## What this is

The one-page personal website for **Federico Dayan**, served at
**https://federicodayan.com**. Plain static HTML: no build step, no framework, no
dependencies. It runs by opening `index.html` directly.

**This repo is public, and every file is served on the live site.** Never commit
anything private: no credentials, rates, client details, or internal notes.

## Audience and goal

The page is aimed **only at founders, technical and non-technical, who have an idea
and need a first version (MVP) built**. The single goal is to get them to book a call.

It is **not** for recruiters, big companies, full-time roles, or engineering
managers. Don't add résumé-style content, long job histories, or corporate language.

Tone: plain English, no jargon, short. Federico prefers **visual, low-scroll**
layouts: icon tiles and step diagrams over paragraphs. When adding something, look
for something to cut.

## Content rules (confirmed by Federico; don't change without asking)

- **Never mention price, rates, or billing model** anywhere on the page, including
  "fixed price".
- **"12 MVPs built"**: some in code, some no-code, some joined at a very early stage.
  Don't say "from scratch" or "12 startups built".
- **14 years working remotely**, through Toptal and Arc.dev.
- **20 years** building software.
- **Just Federico, no team.** He takes on **only 1–2 projects at a time**.
- **The Setup:** U.S. citizen; U.S. company **Dayan Business Solutions** (Florida);
  invoices on a **1099** under the company's **EIN** (Employer Identification
  Number); works U.S. hours.
- Uses AI coding tools heavily for speed, with experienced review. No tool names and
  no speed figures.
- Works directly with technical and non-technical founders.
- Positive framing only: don't criticize agencies, freelancers, or other ways of hiring.

Links:
- Booking: https://calendar.app.google/jG6zRt51k41eSZWc6
- LinkedIn: https://www.linkedin.com/in/federico-dayan
- GitHub: https://github.com/fddayan

## Page structure (`index.html`)

1. **Nav:** name, LinkedIn and GitHub icons, dark-mode toggle, "Schedule a call".
2. **Hero:** headline (*trust*, *idea*, *product* highlighted), sub line, CTA, three
   stats; photo carousel with badges; full-width "From idea to launch" steps
   (Talk → Plan → Build → Launch).
3. **Working with me:** 6 icon tiles.
4. **Built with AI:** text on the left, a three-step flow card on the right.
5. **The Setup:** 4 icon tiles (citizen, company, 1099/EIN, U.S. hours).
6. **See my track record:** LinkedIn and GitHub cards.
7. **Final CTA**, then the footer.

Design: all CSS and JS live inline in `index.html`. Colors are tokens on `:root`,
with dark mode via `prefers-color-scheme` plus a `data-theme` override saved in
`localStorage`. Icons are an inline SVG sprite (`<symbol id="i-…">`, used with
`<use href="#i-…">`). Must work at phone width.

## Photos

- `federico.jpeg` and `federico2-bw.jpeg` are in the carousel. All photos are
  **black and white**.
- To add a photo, convert it, then add an `<img>` and a dot `<button>` inside
  `#carousel`:
  `ffmpeg -v error -y -i in.jpeg -vf "format=gray" -q:v 2 name-bw.jpeg`
- Unused or color originals go in `_originals/`, which is gitignored and never
  published. Every committed file in this repo is public.

## Working conventions

- **Validate after every change:** `npx -y html-validate@8.24.1 index.html` must pass.
- **Don't run `open`** to launch the browser after edits. Just tell Federico to refresh.
- Watch for broad `sed` replacements: the headline text also appears in the
  `<meta>` description and `og:` tags, and HTML in an attribute breaks the page.
  Scope edits to the exact line.
- Commit only when asked.
- **No plain-text email address anywhere**: not on the page, not in files, not in
  commit metadata. The only email contact is the "Tell me about your project" button
  (class `email-btn`, in the hero and the final CTA):
  it uses the alias `hello` at the site's domain (a DNSimple email forward to
  Federico's inbox), assembled in JS on click so it never appears in the HTML.
  Never use or mention the real inbox address. The booking link stays the primary
  CTA. This repo's local git config
  uses the GitHub no-reply address (`188936+fddayan@users.noreply.github.com`);
  check `git config user.email` before committing.

## Deploying to GitHub Pages

The site is hosted on **GitHub Pages** from this repo's `main` branch (repo root).
The `CNAME` file (containing `federicodayan.com`) must stay at the root. DNS is
managed at **DNSimple**.

### First-time setup (skip any step already done)

Check what exists first: `git remote -v`, `gh repo view fddayan/federicodayan.com`.
Creating the public repo and pushing are outward-facing, so **confirm with
Federico before doing them**.

```bash
# 1. Create the public repo and push
gh repo create fddayan/federicodayan.com --public --source . --remote origin --push

# 2. Enable Pages from main / root
gh api -X POST repos/fddayan/federicodayan.com/pages \
  -f "source[branch]=main" -f "source[path]=/"

# 3. Set the custom domain (the CNAME file also does this)
gh api -X PUT repos/fddayan/federicodayan.com/pages -f cname=federicodayan.com
```

**4. DNS at DNSimple.** Easiest: DNSimple → domain → *One-click services* →
**GitHub Pages**. Or add these records manually:

| Type  | Name   | Content                                         |
| ----- | ------ | ----------------------------------------------- |
| A     | (apex) | 185.199.108.153                                 |
| A     | (apex) | 185.199.109.153                                 |
| A     | (apex) | 185.199.110.153                                 |
| A     | (apex) | 185.199.111.153                                 |
| AAAA  | (apex) | 2606:50c0:8000::153                             |
| AAAA  | (apex) | 2606:50c0:8001::153                             |
| AAAA  | (apex) | 2606:50c0:8002::153                             |
| AAAA  | (apex) | 2606:50c0:8003::153                             |
| CNAME | www    | fddayan.github.io                               |

Remove any conflicting apex A/ALIAS/URL records first.

**5. Verify:** `dig +short federicodayan.com` should return the 185.199.x.153
addresses. Propagation takes minutes to about an hour.

**6. Enforce HTTPS** once GitHub has issued the certificate:
```bash
gh api -X PUT repos/fddayan/federicodayan.com/pages -F https_enforced=true
```
Check status with `gh api repos/fddayan/federicodayan.com/pages`.

**Recommended:** verify the domain in GitHub (Settings → Pages → *Add a verified
domain*, which adds a TXT record at DNSimple) so nobody else can claim it.

### Publishing updates

```bash
npx -y html-validate@8.24.1 index.html   # must pass
git add -A && git commit -m "…"
git push
```

GitHub Pages redeploys automatically within about a minute.
