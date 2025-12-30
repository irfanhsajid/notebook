# Deployment Guide

Your code has been successfully pushed to GitHub! Now follow these steps to enable GitHub Pages.

## ✅ Code Pushed Successfully

Repository: https://github.com/irfanhsajid/irfans-notebook

## 📝 Enable GitHub Pages

### Step 1: Go to Repository Settings

1. Visit: https://github.com/irfanhsajid/irfans-notebook/settings
2. Click on **Pages** in the left sidebar

### Step 2: Configure GitHub Pages

1. Under **Source**, select:

   - Branch: `gh-pages`
   - Folder: `/ (root)`

2. Click **Save**

> **⚠️ Why `gh-pages` branch and not `main`?**
>
> - **`main` branch** contains your **source files** (`.md` files, `mkdocs.yml`, etc.) - raw Markdown that needs to be compiled
> - **`gh-pages` branch** contains the **built website** (HTML, CSS, JavaScript) - the actual website files that browsers can display
>
> When you use `main` branch, GitHub Pages tries to serve raw Markdown files, which results in:
>
> - ❌ Broken layout (no CSS styling)
> - ❌ No navigation menu
> - ❌ Raw Markdown text instead of formatted HTML
> - ❌ Missing JavaScript functionality (search, theme toggle, etc.)
>
> The `mkdocs gh-deploy` command automatically:
>
> 1. Builds your site from source files (`main` branch)
> 2. Converts Markdown → HTML with all styling and features
> 3. Pushes the built files to `gh-pages` branch
> 4. GitHub Pages serves the `gh-pages` branch → **Correct UI!** ✅

### Step 3: Wait for Deployment

The GitHub Actions workflow will automatically:

1. Build your MkDocs site
2. Create/update the `gh-pages` branch
3. Deploy to GitHub Pages

This usually takes 1-2 minutes.

### Step 4: Check Deployment Status

1. Go to the **Actions** tab: https://github.com/irfanhsajid/irfans-notebook/actions
2. You should see a "Deploy Documentation" workflow running
3. Wait for it to complete (green checkmark ✓)

### Step 5: Access Your Site

Once deployed, your site will be available at:

**https://irfanhsajid.github.io/irfans-notebook/**

## 🔄 Future Updates

Every time you push to the `main` branch:

```bash
git add .
git commit -m "Update documentation"
git push origin main
```

The site will automatically rebuild and redeploy!

## 🔄 How `main` and `gh-pages` Stay in Sync

**Important:** `gh-pages` doesn't merge with `main` - it's completely regenerated each time!

**The Workflow:**

1. **You push changes to `main`** (e.g., update a `.md` file)
2. **GitHub Actions triggers** (automatically runs on every push to `main`)
3. **The workflow:**
   - Checks out the latest `main` branch
   - Builds the site from current `main` source files
   - Uses `--clean` flag to completely remove old `gh-pages` content
   - Creates a fresh `gh-pages` branch with the new build
   - Pushes it to GitHub

**Why they look "diverged" in Git graph:**

- `main` branch: Has commit history (your source file changes)
- `gh-pages` branch: Only has deployment commits (one per deployment)
- They don't share commit history because `gh-pages` is regenerated, not merged

**Example timeline:**

```
main branch:     A → B → C → D → E  (your commits)
                                    ↓
gh-pages branch:                    [Deploy E]  (built from E)
                                    ↓
main branch:     A → B → C → D → E → F  (new commit)
                                    ↓
gh-pages branch:                    [Deploy E] → [Deploy F]  (rebuilt from F)
```

**Key point:** Every time you push to `main`, `gh-pages` is completely rebuilt from the current state of `main`. The old `gh-pages` content is replaced, not merged!

**What the `--clean` flag does:**

Looking at your workflow file (`.github/workflows/deploy.yml`), it uses:

```yaml
mkdocs gh-deploy --force --clean --verbose
```

The `--clean` flag ensures:

- Old files in `gh-pages` are deleted
- Only the newly built files are pushed
- No leftover files from previous builds
- Clean, consistent deployments every time

## 🎨 Site Features

Your deployed site will have:

- ✅ **Kebab-case URLs**: `/frontend-development/react/`
- ✅ **Accordion Navigation**: Collapsible sections on the left
- ✅ **Table of Contents**: Auto-generated on the right
- ✅ **Light/Dark Mode**: Theme toggle
- ✅ **Search**: Full-text search functionality
- ✅ **Mobile Responsive**: Works on all devices

## 📍 Site Map

```
https://irfanhsajid.github.io/irfans-notebook/
├── getting-started/
│   └── notes/intro/
├── frontend-development/
│   ├── react/
│   └── nextjs/
├── databases/
│   └── database/
│       ├── mongodb/
│       ├── postgresql/
│       └── mysql/
└── devops/
    └── docker/
```

## 🐛 Troubleshooting

### If GitHub Pages isn't enabled:

1. Go to Settings → Pages
2. Make sure **Source** is set to `gh-pages` branch
3. If `gh-pages` branch doesn't exist yet, wait for the Actions workflow to run

### If the workflow fails:

1. Check the Actions tab for error messages
2. Ensure `requirements.txt` is in the repository
3. Verify `mkdocs.yml` is properly formatted

### If pages show 404:

1. Wait a few minutes for DNS propagation
2. Clear browser cache
3. Check that the Actions workflow completed successfully

### If website shows wrong layout/broken UI:

**This happens when GitHub Pages is set to `main` branch instead of `gh-pages`!**

**Symptoms:**

- ❌ Raw Markdown text visible
- ❌ No navigation menu
- ❌ No CSS styling
- ❌ Missing JavaScript features (search, theme toggle)

**Fix:**

1. Go to: https://github.com/irfanhsajid/irfans-notebook/settings/pages
2. Change **Source** from `main` → `gh-pages`
3. Click **Save**
4. Wait 1-2 minutes for GitHub Pages to update
5. Refresh your site

**Why?** `main` has source files (`.md`), `gh-pages` has built website files (`.html`, `.css`, `.js`)

### If repository visibility was changed (private ↔ public):

**This is a common issue!** When you change repository visibility, GitHub Pages settings may be reset.

**Fix Steps:**

1. **Re-enable GitHub Pages:**

   - Go to: https://github.com/irfanhsajid/irfans-notebook/settings/pages
   - Under **Source**, select:
     - Branch: `gh-pages`
     - Folder: `/ (root)`
   - Click **Save**

2. **Trigger a new deployment:**

   - Option A: Push any change to `main` branch (triggers automatic deployment)
   - Option B: Manually trigger workflow:
     - Go to: https://github.com/irfanhsajid/irfans-notebook/actions
     - Click "Deploy Documentation" workflow
     - Click "Run workflow" → "Run workflow" button

3. **Wait 2-5 minutes** for GitHub Pages to rebuild

4. **Clear browser cache** or try incognito mode

5. **Verify the site** at: https://irfanhsajid.github.io/irfans-notebook/

## 📧 Need Help?

Check the workflow logs at:
https://github.com/irfanhsajid/irfans-notebook/actions

---

**Happy documenting!** 🚀
