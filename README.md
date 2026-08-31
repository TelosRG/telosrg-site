# telosrg-site

The public marketing site for Telos Research Group.

A single static page: an org mark, a card grid — one card per app in the portfolio, each
linking out to that app's GitHub repo — and a footer link to the GitHub org. No backend, no
build step, no framework.

## Run it

```bash
python -m http.server 8934
```

Then open `http://localhost:8934/`.

See `CLAUDE.md` for architecture, constraints, and the mobile-first UI targets.
