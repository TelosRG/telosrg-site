# telosrg-site

The public marketing site for Telos Research Group — a single page with a card grid, one
card per app in the portfolio, each linking out to that app's GitHub repo. No backend, no
build step, no framework.

## Running it

There's nothing to build. Serve the directory statically and open it:

```bash
python -m http.server 8934
# then open http://localhost:8934/
```

Any static file server works — this is plain HTML/CSS, no bundler, no dependencies.

## Testing it

No test suite. Verify by eye: open `index.html` in a browser (or serve it as above) and
check the card grid renders, each card links to the right GitHub repo, and the layout holds
at 360px width with no horizontal overflow.

## Deploying it

Deployed via GitHub Pages, serving directly off `main` at the repo root — no build step,
so a push to `main` is a deploy. Live at https://telosrg.github.io/telosrg-site/. Repo:
https://github.com/TelosRG/telosrg-site. A custom domain (`telosrg.com`, already owned) can
be attached later by adding a `CNAME` file and pointing DNS at GitHub Pages; not done yet.

## Architecture

- `index.html` — the entire page: header (org mark + name + tagline), a card grid, footer.
- `styles.css` — all styling, including the color palette (CSS custom properties in
  `:root`) and the mobile-first layout rules (see `## UI targets` below).
- No JavaScript. Cards are plain `<a>` elements — clicking one navigates to GitHub directly,
  `target="_blank"`.

The org mark (the eye/spiral logo) is referenced directly from
`https://avatars.githubusercontent.com/u/323324518?v=4` rather than vendored into the repo —
it's the GitHub org's own avatar and stays in sync with it automatically.

## Constraints

- **The card list is hand-written, not generated.** There is no build step reading
  `graph_agents/portfolio/registry.json` — that would be an edge into the fleet's tooling,
  which this app must never depend on. When the portfolio changes (an app added, removed, or
  renamed), a human or an agent updates the card markup in `index.html` by hand, the same way
  the fleet's own `registry.json` gets updated. See `## The one invariant` in
  `../graph_agents/CLAUDE.md`.
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
| Tablet | `>= 768px` | Cards go from 1 column to 2. |
| Desktop | `>= 1024px` | Header type scales up. |
| Content cap | `>= 1280px` | Page content gets a max-width so it stops stretching. |

- Base CSS in `styles.css` is the 360px layout; every larger-screen rule is a `min-width`
  media query that adds to it, never a `max-width` override.
- Every interactive element (each card, the footer link) has a >= 44px effective hit area.
- Body text is 16px; no fixed-px layout widths; `100dvh` used instead of `100vh`.
- Visible focus ring (`:focus-visible`) on every link — never removed without a replacement.
