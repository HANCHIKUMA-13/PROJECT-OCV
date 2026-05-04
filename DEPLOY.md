# Deploy OCV — GitHub + Vercel walkthrough

This folder is ready to ship. Two routes below.

> Project assumptions
> - Repo name: **PROJECT-OCV** (public)
> - Bundled demo: **LES_RL9-10_v100ms.zip** included
> - Stack: static HTML, no build step

---

## Files in this deploy

```
OCV.html                     # the app
index.html                   # redirect to OCV.html
LES_RL9-10_v100ms.zip        # bundled demo case (8.7 MB)
README.md
LICENSE                      # MIT
.gitignore
vercel.json                  # static-site headers + cleanUrls
DEPLOY.md                    # this file
```

---

## Route A — GitHub + Vercel via CLI

### 1. One-time install (skip if already installed)

| Tool | Mac | Windows | Linux |
|---|---|---|---|
| git | `brew install git` | `winget install Git.Git` | `apt install git` |
| GitHub CLI | `brew install gh` | `winget install GitHub.cli` | `apt install gh` |
| Vercel CLI | `npm i -g vercel` | `npm i -g vercel` | `npm i -g vercel` |

### 2. Authenticate

```bash
gh auth login            # opens browser, choose GitHub.com → HTTPS
vercel login             # opens browser, choose your provider
```

### 3. Initialise the repo and push

Run all of these from inside this folder:

```bash
cd "<this folder>"

git init -b main
git add .
git commit -m "Initial OCV v0.0.2 release"

# create the public repo on GitHub and push in one go
gh repo create PROJECT-OCV --public --source=. --remote=origin --push
```

You'll get back a URL like `https://github.com/<your-handle>/PROJECT-OCV`.

### 4. Deploy to Vercel

```bash
vercel link             # answer "n" to "link to existing?", confirm new project
vercel --prod           # deploys this folder
```

Vercel will print the live URL — typically `https://project-ocv-<hash>.vercel.app`. The first prod deploy also locks in `https://project-ocv.vercel.app` if the slug is free.

### 5. Auto-deploy on every push (optional but recommended)

```bash
vercel git connect      # links the GitHub repo, enables auto-deploys on push
```

After this, every `git push` triggers a fresh deployment.

---

## Route B — GitHub web UI + Vercel web UI (no CLI)

### 1. Create the GitHub repo

1. Go to https://github.com/new
2. **Repository name**: `PROJECT-OCV`
3. **Public**
4. Leave "Initialize with README" **unchecked** (we already have one)
5. Click **Create repository**

### 2. Upload files

On the new empty repo page, click **uploading an existing file**, then drag-drop **all** these files from this folder:

- `OCV.html`
- `index.html`
- `LES_RL9-10_v100ms.zip`
- `README.md`
- `LICENSE`
- `.gitignore`
- `vercel.json`
- `DEPLOY.md`

Commit message: `Initial OCV v0.0.2 release`. Click **Commit changes**.

> The 8.7 MB zip will take ~30 s on a typical connection. GitHub's web upload limit is 25 MB per file in batch upload but supports up to 100 MB via drag-drop. If the upload UI complains, drag the zip on its own as a second commit.

### 3. Deploy on Vercel

1. Go to https://vercel.com/new
2. **Import Git Repository** → pick `PROJECT-OCV`
3. Vercel auto-detects "Other" framework — leave the build settings as **default** (no build command, no output directory)
4. Click **Deploy**

About 20 seconds later you'll get the live URL. Visit it — `index.html` redirects to `OCV.html`, click **Load Demo**, you should see the motorbike geometry render.

---

## Verifying the deployment

Open the live URL and confirm:

- [ ] Page loads in dark mode
- [ ] "Load Demo" button populates Strips 1, 2, 3
- [ ] Motorbike OBJ renders in the 3D viewer
- [ ] Geometry/Layers tree shows ~7 named groups with color/visibility/opacity controls
- [ ] Boundary Patches table lists inlet, outlet, lowerWall, upperWall, front, back, motorBike_.*
- [ ] Browser console is free of errors

If the demo button only shows metadata text (no 3D bike), the zip didn't make it into the repo — re-check the upload.

---

## After the first deploy

| Want to… | Do this |
|---|---|
| Update the app | Edit `OCV.html` → `git add OCV.html && git commit -m "..." && git push` (auto-deploy) |
| Roll back | Vercel dashboard → Deployments → click any older one → **Promote to Production** |
| Custom domain | Vercel dashboard → Project → Settings → Domains → Add |
| See traffic | Vercel dashboard → Project → Analytics |

---

## Troubleshooting

**`gh: command not found`** — install via the table at the top.

**`fatal: not a git repository`** — make sure you `cd` into this folder before `git init`.

**Vercel deploys an empty page** — open the deployment's **Source** tab. If `index.html` is missing, your `git push` left it out (case-sensitivity on Mac can hide files); push again.

**Demo button shows "demo zip not co-located"** — the zip didn't make it into `/public` of the deploy. For a static deploy, the zip just needs to sit at repo root, no `/public` needed. Verify it's listed in the GitHub file tree.
