# Setup — make a new client site in 2 minutes

This is the operator's guide. You'll never need to open Terminal — everything runs inside Claude Code, which you already have.

The model: **your own template, your own client sites.** During install you'll get an independent copy of `don1989/tillettfilm-client-template` under your GitHub account (via the GitHub "Use this template" API — no fork, no upstream relationship). From then on, *your copy* is the source for every new client site. You customize it once with your real showreel, work films, testimonials, etc. — and every future site inherits them. Updates to the upstream template don't touch you unless you ask for them.

---

## One-time install (~5 minutes, once ever)

Open Claude Code. Paste this single prompt and let it run:

````
Install the Tillett Film client-site maker for me. You should:

1. If `brew` (Homebrew) isn't installed on this Mac, install it from https://brew.sh/. This step needs my Mac password — you'll be prompted in the terminal pane, and I'll type it in.

2. Use brew to install `gh`, `git`, and `jq` if they're not already present.

3. Run `gh auth login --web --git-protocol https` so I'm logged in to GitHub under my own account. Show me the 8-character device code and the URL — I'll paste the code into the browser and authorize.

4. Capture my GitHub login: `LOGIN=$(gh api user --jq .login)`.

5. Use the GitHub template-generate API to create an independent copy of the upstream template under my account:
   `gh api -X POST repos/don1989/tillettfilm-client-template/generate -f owner="$LOGIN" -f name=tillettfilm-client-template -f description="My personalized opening-scene client-site template" -F include_all_branches=false -F private=false`

   This is the API equivalent of clicking "Use this template" on GitHub — it creates a brand-new repo at `$LOGIN/tillettfilm-client-template` with no fork relationship to the upstream. I cannot push to don1989's repo and don1989's repo cannot push to mine.

6. Mark my new copy as a template repository so the `/new-client` skill can later generate client sites from it:
   `gh api -X PATCH repos/$LOGIN/tillettfilm-client-template -F is_template=true`

7. Clone my copy locally so I can update the studio assets (showreel, testimonial, work films, etc.):
   `gh repo clone $LOGIN/tillettfilm-client-template ~/Documents/tillettfilm-client-template`

8. Create the directory `~/.claude/skills/new-client/` (mkdir -p).

9. Download the skill file from
   https://raw.githubusercontent.com/don1989/tillettfilm-client-template/main/.claude/skills/new-client/SKILL.md
   and save it as `~/.claude/skills/new-client/SKILL.md`. (Use `curl -fsSL <url> -o <path>`.)

10. Pre-approve the Bash commands the skill will need so I don't have to click Allow on every run. Use this exact `jq` invocation — do NOT use Python or any other JSON library — it preserves any existing rules and dedupes:

    ```bash
    SETTINGS="$HOME/.claude/settings.json"
    mkdir -p "$HOME/.claude"
    [ -f "$SETTINGS" ] || echo '{}' > "$SETTINGS"
    NEW_RULES='["Bash(gh:*)","Bash(git:*)","Bash(brew install:*)","Bash(perl -i:*)","Bash(cp:*)","Bash(mkdir:*)","Bash(rsync:*)","Bash(test:*)","Bash([:*)","Bash(curl -fsSL:*)","Bash(jq:*)"]'
    jq --argjson rules "$NEW_RULES" '
      .permissions = (.permissions // {}) |
      .permissions.allow = ((.permissions.allow // []) + $rules | unique)
    ' "$SETTINGS" > "$SETTINGS.tmp" && mv "$SETTINGS.tmp" "$SETTINGS"
    ```

11. Verify everything: `gh auth status`, list `~/.claude/skills/new-client/`, list `~/Documents/tillettfilm-client-template/`, and confirm my template's `is_template` flag with `gh api repos/$LOGIN/tillettfilm-client-template --jq .is_template`. Tell me everything is green and `/new-client` is ready to use.
````

You'll be asked twice during this run, both one-time-ever:
- **Your Mac password** when Homebrew installs itself
- **An 8-character GitHub code** that you paste into the browser tab Claude opens

After this completes, restart Claude Code (quit and re-open) so it picks up the new skill.

---

## Customize your template (one-time, recommended)

Open `~/Documents/tillettfilm-client-template/` in Finder. The `assets/` folder has placeholder videos and images — replace them with your real ones:

- `showreel.mp4` — your closing reel (currently missing; the closing scene will be a black box until you add one)
- `testimonial.mp4` + `testimonial-thumbnail.jpg` — a client testimonial
- `work-brand-film.{mp4,jpg}`, `work-event-film.{mp4,jpg}`, `work-founder-film.{mp4,jpg}` — three example pieces

Once you've dropped your real files in, commit and push. Easiest way is to ask Claude in any session:

> "Commit the updated assets in ~/Documents/tillettfilm-client-template and push to GitHub."

Every future client site will inherit your real assets automatically.

You can also edit text in `index.html` if your studio name, director name, email, or brand colours differ from the defaults — but you don't have to, you can also override per-client at the `/new-client` prompts.

---

## Every new client (~2 minutes)

Open Claude Code from anywhere — your home folder is fine, you don't need to be in any particular project.

Type:

```
/new-client
```

Claude asks you, one at a time:

1. **Prospect's full name** — e.g. *Sarah Johnson*
2. **Meeting day, date, time** — e.g. *Friday, 16 May 2026, 2:30 PM EST*
3. **Repo name** — Claude suggests `sarah-johnson-precall-brochure`; accept or change.
4. **Intro video path** — wherever the 90-second personal video is on your Mac. Any filename, any common format (`.mp4`, `.mov`, `.m4v`). For example `~/Desktop/sarah-intro.mov` straight off your phone via AirDrop. Or say *skip* to leave the placeholder.
5. **Anything custom?** — by default Claude uses your template's defaults (the assets and text you committed above). Only answer if you want to override for *this prospect only*.

Then Claude:
- Generates a fresh repo under your GitHub account from *your* template via the template-generate API
- Clones it locally to `~/Documents/client-sites/<repo-name>/` for editing
- Swaps every name and date in `index.html`
- Copies your intro video in and wires up scene 02
- Commits + pushes
- Turns on GitHub Pages
- Prints the live URL

Send the URL to your prospect. Allow ~1 minute on first deploy.

> All client-site repos are created **public** — free GitHub Pages only serves public repos. The URL has no inbound links, so it's only discoverable by people you send it to.

---

## How assets work

Two layers:

- **Studio-wide** (testimonial, work films, showreel) — live in your template at `~/Documents/tillettfilm-client-template/assets/` and on GitHub at `<your-username>/tillettfilm-client-template`. Every new client site inherits them automatically when generated.
- **Per-client** — just the intro video. The only file Claude asks you for at `/new-client` time.

If you ever want to update studio assets, edit them in your local template, commit, push. Claude can do this for you in any session — just describe what you want updated.

---

## Troubleshooting

**`/new-client` doesn't appear** — quit Claude Code and re-open. Skills are picked up on launch. If still missing, paste the installer prompt above again.

**The site shows 404 after Claude finishes** — Pages takes about a minute to build on first deploy. Refresh in 60 seconds. If still broken after 5 minutes, ask Claude: *"Check the Pages status on this repo."*

**Intro video doesn't play on the client's site** — most likely the file path you gave Claude was wrong, or the file is in an obscure format. Ask Claude to re-run with a different file.

**Closing-scene reel is a black box** — you haven't added your showreel to your template yet. Drop a `showreel.mp4` into `~/Documents/tillettfilm-client-template/assets/`, then ask Claude: *"Commit and push the new showreel to my template."* Every future client site will have it.

**404 from the generate API at `/new-client` time** — your template's "is_template" flag was unset somehow. Re-set it: ask Claude to run `gh api -X PATCH repos/<your-login>/tillettfilm-client-template -F is_template=true`.

**Want fresh updates from the upstream template** — your template is independent, so changes don't auto-propagate. If you want to pull a specific upstream change (e.g. a fix to the skill), ask Claude to fetch the specific file from `https://raw.githubusercontent.com/don1989/tillettfilm-client-template/main/...` and commit it to your template.
