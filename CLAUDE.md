# telosrg-site

The public marketing site for Telos Research Group — a static, multi-page site. The front
page is a hero: the org mark, the name, the tagline, a headline and a one-paragraph blurb.
Five interior pages carry the open reference implementation (Code), the long-form copy
(About, Platform) and the legal text (Privacy Policy, Terms of Service). No backend, no
build step, no framework.

**What the site is selling.** Telos is positioned as a firm that helps *other companies*
build fleets of coding agents — not as a holding company for a portfolio of side projects.
That distinction drives every page: the apps under `code.html` are framed as what the
fleet has been run against, evidence rather than inventory. No engagement model, pricing,
or service tier is named anywhere, deliberately — the LLC is not formed yet, so the copy
positions the thesis and stops short of promises. Don't add a services or pricing section
without that changing first.

## Running it

There's nothing to build. Serve the directory statically and open it:

```bash
python -m http.server 8934
# then open http://localhost:8934/
```

Any static file server works — this is plain HTML/CSS, no bundler, no dependencies.

## Testing it

No test suite. Verify by eye: serve the directory as above and check that the hero renders
on the front page, the card grid renders on `code.html` with each card pointing at the
right GitHub repo, the nav and footer links reach every interior page, and the layout holds
at 360px width with no horizontal overflow on **all six** pages — not just `index.html`.

## Deploying it

Deployed via GitHub Pages, serving directly off `main` at the repo root — no build step,
so a push to `main` is a deploy. Live at https://telosrg.github.io/telosrg-site/. Repo:
https://github.com/TelosRG/telosrg-site. A custom domain (`telosrg.com`, already owned) can
be attached later by adding a `CNAME` file and pointing DNS at GitHub Pages; not done yet.

## Architecture

Five hand-written HTML pages sharing one stylesheet:

| File | What it is |
|---|---|
| `index.html` | The front page: header, hero (headline + blurb + link to Code), footer. Deliberately sparse — it carries no card grid. |
| `code.html` | `graph_agents` as a featured card, then the projects it has been run against as the ordinary card grid. |
| `about.html` | What the group is and why the portfolio is structured the way it is. |
| `platform.html` | The strategy, as three numbered layers. Deliberately high-altitude. |
| `privacy.html` | Privacy Policy. |
| `terms.html` | Terms of Service. |
| `styles.css` | All styling for all five pages: the palette (CSS custom properties in `:root`) and the mobile-first layout rules (see `## UI targets` below). |

- Every page repeats the same header, nav and footer markup inline. **There is no
  templating and no include** — adding a nav link or changing the tagline means editing
  six files. That is the accepted cost of having no build step; don't introduce a
  generator to avoid it without deciding that trade deliberately.
- `index.html` is the one page whose header does *not* wrap the org mark in a link — it is
  already home. Every other page wraps it in `<a class="mark-link" href="index.html">`.
- The current page marks itself with `aria-current="page"` in the nav (and in the footer
  legal bar on the two legal pages).
- No JavaScript, anywhere. Cards are plain `<a>` elements — clicking one navigates to
  GitHub directly, `target="_blank"` — and the interior pages are real navigations, not
  overlays. If a future change seems to need JS, it probably needs a different design.

The org mark (the eye/spiral logo) is referenced directly from
`https://avatars.githubusercontent.com/u/323324518?v=4` rather than vendored into the repo —
it's the GitHub org's own avatar and stays in sync with it automatically.

## Constraints

- **The card list is hand-written, not generated.** There is no build step reading
  `graph_agents/portfolio/registry.json` — that would be an edge into the fleet's tooling,
  which this app must never depend on. When the portfolio changes (an app added, removed, or
  renamed), a human or an agent updates the card markup in `code.html` by hand, the same way
  the fleet's own `registry.json` gets updated. See `## The one invariant` in
  `../graph_agents/CLAUDE.md`.
- **The site describes `graph_agents`, but does not depend on it.** `platform.html` and
  `code.html` explain the fleet and link to `https://github.com/njcurtis3/graph_agents` by
  URL. That is a prose cross-reference, which the constitution explicitly allows — the
  mechanical test is that deleting `graph_agents/` from disk breaks nothing here. There is
  no build step reading it and there must never be one. Note the repo currently sits under
  a personal account rather than the `TelosRG` org; if it moves, the URL in both pages is
  what needs updating.
- **`platform.html` is strategy; `code.html` is mechanism.** Platform argues *why* the
  structure is worth adopting and names no internal machinery — no node names, no hook
  names, no `file:line`, no worktrees, no branch or state-file detail. Its pipeline strip
  is generic phases (establish facts → propose a plan → human decision → do the work →
  verify independently), not the fleet's own node roster. Implementation detail belongs on
  `code.html` or in the repo. If Platform starts naming machinery again, it has drifted.
- **Copy claims must stay true to the fleet.** The featured card on `code.html` describes
  real behavior in `graph_agents` — the guards that fail closed, the state audit, the close
  checked against git. If the fleet's behavior changes, this copy becomes a false claim
  about a real product; re-read `graph_agents/README.md` before editing that page.
- **Color palette is derived from the TelosRG GitHub org avatar** — a black background with
  a swirling rainbow-eye mark whose iris is teal/cyan/green. The palette deliberately does
  not reproduce the full rainbow: black/near-black as the base, teal as the primary accent,
  and a restrained teal-to-violet gradient as the secondary accent (used on hover states and
  the "View on GitHub" link text) rather than a literal rainbow everywhere. If the org avatar
  changes, the palette in `styles.css`'s `:root` should be revisited, not blindly kept.

Standalone app under the repos/ umbrella. Never import from a sibling app; see ../graph_agents/CLAUDE.md.

## UI targets

Copied from `graph_agents/conventions/mobile-first.md`, then owned locally.

| Tier | Width | What it means |
|---|---|---|
| **Floor** | **360px portrait** | Nothing may break or overflow horizontally at 360. |
| Tablet | `>= 768px` | Cards go from 1 column to 2; the nav moves from under the header onto the header row; the hero headline scales up. |
| Desktop | `>= 1024px` | Header type scales up. |
| Content cap | `>= 1280px` | Page content gets a max-width so it stops stretching. |

- Base CSS in `styles.css` is the 360px layout; every larger-screen rule is a `min-width`
  media query that adds to it, never a `max-width` override.
- Every interactive element (each card, each nav link, each footer link) has a >= 44px
  effective hit area. **Inline links inside body prose are the deliberate exception** —
  forcing 44px on a link mid-sentence wrecks the line height, so they inherit the text's
  own box.
- Body text is 16px; no fixed-px layout widths; `100dvh` used instead of `100vh`.
- Visible focus ring (`:focus-visible`) on every link — never removed without a replacement.
