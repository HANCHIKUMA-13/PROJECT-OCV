# Releasing OCV from the terminal

Faster than drag-and-drop. Once set up, every release becomes three commands.

---

## One-time setup (run once per machine)

### 1. Install the tools

| Tool      | Mac (Homebrew)        | Windows (winget)            | Linux (apt)        |
|-----------|-----------------------|-----------------------------|--------------------|
| `git`     | `brew install git`    | `winget install Git.Git`    | `sudo apt install git` |
| `gh`      | `brew install gh`     | `winget install GitHub.cli` | `sudo apt install gh`  |
| Node.js   | `brew install node`   | `winget install OpenJS.NodeJS` | `sudo apt install nodejs npm` |
| `vercel`  | `npm i -g vercel`     | `npm i -g vercel`           | `npm i -g vercel`  |

Verify everything:

```bash
git --version
gh --version
node --version
vercel --version
```

### 2. Authenticate

```bash
gh auth login              # opens browser → choose GitHub.com → HTTPS → device code
vercel login               # opens browser → continue with same email
```

### 3. Clone the existing repo locally (so you don't have to re-bootstrap)

```bash
cd ~/Documents              # or wherever you want the local copy
gh repo clone HANCHIKUMA-13/PROJECT-OCV
cd PROJECT-OCV
vercel link                 # answer "y" to "link to existing project?", pick project-ocv
```

That's the entire setup. From here on out every release is three commands.

---

## Routine release (after every change to OCV.html)

Open a terminal in the local clone (`~/Documents/PROJECT-OCV` or wherever you put it). Make your edit to `OCV.html` directly in that folder — or copy the updated file from Cowork's outputs folder.

### To copy from Cowork outputs to the repo (Windows PowerShell)

```powershell
$src = "$env:APPDATA\Claude\local-agent-mode-sessions\ad770300-9a23-4c77-b92d-4d0efa57afd9\fc04953e-8a0e-4c75-badd-b9c3ea0b58da\local_0aa324b8-b51a-440b-a405-ce02e6304d6b\outputs\OCV.html"
$dst = "$HOME\Documents\PROJECT-OCV\OCV.html"
Copy-Item $src $dst -Force
```

### To copy from Cowork outputs to the repo (Mac / Linux)

```bash
cp "$HOME/Library/Application Support/Claude/local-agent-mode-sessions/.../outputs/OCV.html" \
   "$HOME/Documents/PROJECT-OCV/OCV.html"
```

### Then push

```bash
cd ~/Documents/PROJECT-OCV
git add OCV.html
git commit -m "v2.0.0 — your message here"
git push                    # triggers Vercel auto-deploy in ~20 s
```

That's it. Open https://project-ocv.vercel.app a few seconds later and your change is live.

---

## Tagging a milestone release (e.g. v2.0.0)

```bash
git tag -a v2.0.0 -m "v2.0.0 — cyberpunk + motorsport overhaul"
git push origin v2.0.0
gh release create v2.0.0 \
  --title "v2.0.0 — Cyberpunk + Motorsport" \
  --notes-file RELEASE_NOTES.md \
  --latest
```

`RELEASE_NOTES.md` can be a quick markdown file you write next to OCV.html. Or omit `--notes-file` and pass `--notes "..."` inline.

---

## A useful one-liner

Save this as `~/.local/bin/ocv-ship.sh` (chmod +x) for an opinionated single-command release:

```bash
#!/usr/bin/env bash
set -euo pipefail
cd "$(dirname "$0")/../../Documents/PROJECT-OCV" || exit 1
MSG="${1:-routine update}"
git add -A
git commit -m "$MSG"
git push
echo
echo "Pushed. Watching Vercel deploy…"
sleep 8
vercel ls project-ocv --scope han-6947s-projects | head -3
```

Then: `ocv-ship.sh "fix typo in dashboard"` and walk away.

---

## What about the demo zip?

The demo zip (`LES_RL9-10_v100ms.zip`) lives in the repo root. You only need to update it if the case itself changes — which is rare. If you do, just `git add LES_RL9-10_v100ms.zip` along with the HTML.

---

## Rolling back

```bash
gh release list                      # see what's tagged
git checkout v1.0.0                  # local checkout of the v1 prototype
```

Or, on Vercel directly:
1. https://vercel.com/han-6947s-projects/project-ocv/deployments
2. Find the deployment you want to revert to
3. Click the three-dot menu → **Promote to Production**

The prod URL switches in seconds.

---

## Common snags

**`fatal: not a git repository`** — you're in the wrong folder. `cd` into `PROJECT-OCV`.

**`error: failed to push some refs to ...`** — someone (or you, from a different machine) pushed since your last pull. Run `git pull --rebase` then `git push`.

**`vercel: command not found`** — `npm i -g vercel` again. If npm complains about permissions, prefix with `sudo` (Mac/Linux) or run PowerShell as Administrator (Windows).

**`gh auth status` says "not logged in"** — run `gh auth login` again. Tokens occasionally expire on inactive accounts.

**Vercel deploys but the page shows the old version** — the browser cached the old HTML. Hard refresh: Ctrl+Shift+R (Windows/Linux), Cmd+Shift+R (Mac).
