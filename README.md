# 🚀 Deploy Flutter Web App Using GitHub Actions

Automate your Flutter Web deployment with GitHub Actions and GitHub Pages — no manual uploads, no extra tools. Just push to `master` and your app goes live.

---

## ✨ What This Does

Every push to the `master` branch automatically:

1. 🔨 **Builds** your Flutter web app in release mode
2. 📦 **Uploads** the build as a GitHub Pages artifact
3. 🌍 **Deploys** it live to GitHub Pages

---

## 📋 Prerequisites

Before you start, make sure you have:

| Requirement | Check |
|---|---|
| Flutter SDK installed | `flutter --version` |
| Web support enabled | `flutter config --enable-web` |
| A GitHub repository created | — |

Enable web support if you haven't already:

```bash
flutter config --enable-web
```

---

## 🥇 Step 1 — Prepare Your Flutter Web Project

### 1️⃣ Update `web/index.html`

Open `web/index.html` and find:

```html
<base href="/">
```

Change it to:

```html
<base href="/<your-repo-name>/">
```

> **Example:** If your repo is `my_flutter_website`, use:
> ```html
> <base href="/my_flutter_website/">
> ```

> ⚠️ **Why?** GitHub Pages serves project repos under a subpath (`/repo-name/`). Without this change, assets won't load and you'll see a blank page.

---

### 2️⃣ Improve the Loading Experience *(Recommended)*

Flutter apps can show a blank white screen while the engine loads. Improve first impressions by customizing `web/index.html`:

- Add your app logo or branded splash screen
- Match the background color to your app theme
- Add a CSS loading spinner or animation

This small effort significantly improves perceived performance.

---

## 🥈 Step 2 — Push Your Code to GitHub

```bash
git add .
git commit -m "Prepare project for deployment"
git push origin master
```

---

## 🥉 Step 3 — Configure GitHub Pages

1. Go to your repository on GitHub
2. Click **Settings** → **Pages**
3. Under **Build and deployment**, set **Source** to **GitHub Actions**
4. Save

> ℹ️ You don't need to create a `gh-pages` branch — the workflow handles everything.

---

## 🏗️ Step 4 — Create the GitHub Actions Workflow

### Option A: Via the GitHub UI

1. Go to your repository → **Actions** → **New workflow**
2. Click **Set up a workflow yourself**
3. Name the file `deploy.yml`
4. Paste the workflow below and commit

### Option B: Manually in your project

Create the file at:

```
.github/workflows/deploy.yml
```

### Workflow File

```yaml
name: Deploy Flutter Web App

on:
  push:
    branches:
      - master  # Triggers on every push to master

jobs:
  build:
    name: 🔨 Build Flutter Web
    runs-on: ubuntu-latest

    steps:
      - name: 📥 Checkout Repository
        uses: actions/checkout@v4

      - name: 🐦 Setup Flutter (Stable Channel)
        uses: subosito/flutter-action@v2
        with:
          channel: 'stable'

      - name: 📦 Install Dependencies
        run: flutter pub get

      - name: 🌐 Build Flutter Web (Release Mode)
        run: flutter build web --release

      - name: 📤 Upload GitHub Pages Artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: 'build/web'

  deploy:
    name: 🚀 Deploy to GitHub Pages
    needs: build
    runs-on: ubuntu-latest

    permissions:
      pages: write
      id-token: write

    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}

    steps:
      - name: 🌍 Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

---

## ⚙️ What Happens After You Push?

```
git push origin master
        │
        ▼
┌───────────────────────────────┐
│  GitHub Actions triggered     │
│                               │
│  1. ✅ Checkout code          │
│  2. ✅ Install Flutter        │
│  3. ✅ flutter pub get        │
│  4. ✅ flutter build web      │
│  5. ✅ Upload artifact        │
│  6. ✅ Deploy to Pages        │
└───────────────────────────────┘
        │
        ▼
  🎉 Your site is live!
```

---

## 🌍 Your Live Website URL

```
https://<your-username>.github.io/<your-repo-name>/
```

**Example:**

```
https://johndoe.github.io/my_flutter_website/
```

---

## 🧠 Troubleshooting

### Blank page after deployment?

- ✅ Double-check `<base href="/<repo-name>/">` in `web/index.html`
- ✅ Confirm GitHub Pages source is set to **GitHub Actions**
- ✅ Check the **Actions** tab to make sure the workflow completed without errors

### Workflow not triggering?

- ✅ Make sure the file is at `.github/workflows/deploy.yml`
- ✅ Confirm you're pushing to `master` (not `main` or another branch)
- ✅ Check that GitHub Actions is enabled in your repository settings

### After renaming your repository

Update `web/index.html`:

```html
<base href="/new-repo-name/">
```

Then commit and push — the workflow will redeploy automatically.

### Using a custom domain?

If you've configured a custom domain in GitHub Pages settings, update your base href:

```html
<base href="/">
```

---

## 🏆 Why This Approach?

This setup uses GitHub's **official, modern deployment method** — no third-party deploy actions, no `gh-pages` branch management.

| Feature | This Setup | Manual `gh-pages` branch |
|---|---|---|
| Security | ✅ OIDC token auth | ❌ Requires PAT token |
| Maintenance | ✅ No branch cleanup | ❌ Branch grows over time |
| GitHub recommended | ✅ Yes | ❌ Legacy approach |
| Deployment visibility | ✅ Tracked in Pages UI | ⚠️ Limited visibility |

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).
