# Tillett Film — Client Site Template

Personalized **"Opening Scene"** pre-call brochure sites for prospects: one polished,
single-page site per client, generated from your own copy of this template and
published free on GitHub Pages.

This repo is the **upstream template** — you don't use it directly. During a
one-time install you create your *own* independent copy under your GitHub account
(via GitHub's "Use this template" API — no fork, no upstream link). You customize
that copy once with your real showreel, work films, and testimonials, and from then
on you spin up a new client site in ~2 minutes with the `/new-client` skill inside
Claude Code.

## 👉 Get started

**[SETUP.md](SETUP.md)** — the operator's guide:

- **One-time install** (~5 min) — a single prompt you paste into Claude Code.
- **Customize your template** — drop in your real showreel, testimonial, and work films.
- **Every new client** (~2 min) — run `/new-client`, answer a few prompts, send the live URL.

## What's in here

| Path | What it is |
| --- | --- |
| [`SETUP.md`](SETUP.md) | Full operator guide — install, customize, and create client sites. |
| [`index.html`](index.html) | The single-page client site (the "Opening Scene"). |
| [`assets/`](assets) | Studio-wide placeholder media (showreel, testimonial, work films) you replace with your own. |
| [`.claude/skills/new-client/`](.claude/skills/new-client) | The `/new-client` skill that generates a fresh client site from your template. |
