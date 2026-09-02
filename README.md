# ekhenry.com

Personal academic website, built with [Quarto](https://quarto.org) and hosted free on GitHub Pages.

**You do not need to install git, use a terminal, or learn any git commands.** Everything below
happens in your web browser, on github.com. GitHub builds and publishes the site for you every
time you save a change.

---

## Part 1 — Getting the site online

### 1. Sign in to GitHub

Go to <https://github.com> and sign in. (If you need an account, it's free — click **Sign up**.)

Note your username; you'll need it in a moment. It's in the top-right avatar menu.

### 2. Create the repository

1. Go to <https://github.com/new>.
2. **Repository name:** type `YOUR-USERNAME.github.io`, using your actual username.
   For example, if your username is `ekhenry`, name it `ekhenry.github.io`.
3. Set it to **Public**. GitHub Pages is only free on public repositories.
4. Leave "Add a README file" and everything else **unchecked**.
5. Click **Create repository**.

> **Why that exact name?** A repo named `username.github.io` gets served from the root of the
> domain, which avoids a folder-path prefix in your URLs. Any other name works too, but this
> one is the least fiddly.

### 3. Upload the site files

On your new empty repository page, click the **uploading an existing file** link (in the
"Quick setup" box).

1. Unzip the site folder on your computer.
2. Open the unzipped folder so you can see the files inside it (`index.qmd`, `research.qmd`,
   `assets`, and so on).
3. **Select all of those files and folders and drag them onto the GitHub upload page.**
   Drag the *contents*, not the folder itself — otherwise everything ends up one level too deep.
4. Scroll down and click the green **Commit changes** button.

Wait for the upload to finish. You should see `index.qmd`, `research.qmd`, `teaching.qmd`,
`cv.qmd`, `personal.qmd`, `_quarto.yml`, `_variables.yml`, `styles.scss`, `CNAME`, `README.md`,
and an `assets` folder listed.

### 4. Add the build instructions

Browsers often skip folders whose name starts with a dot, so the `.github` folder probably
didn't upload. You'll create it directly on GitHub instead — this takes one minute.

1. On your repository page, click **Add file** → **Create new file**.
2. In the filename box, type exactly:

   ```
   .github/workflows/publish.yml
   ```

   (As you type each `/`, GitHub turns it into a folder automatically. That's expected.)
3. Paste this into the large text box below it:

   ```yaml
   name: Publish site

   on:
     push:
       branches: [main]
     workflow_dispatch:

   permissions:
     contents: read
     pages: write
     id-token: write

   concurrency:
     group: pages
     cancel-in-progress: true

   jobs:
     build:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v4

         - name: Set up Quarto
           uses: quarto-dev/quarto-actions/setup@v2

         - name: Render site
           run: quarto render

         - name: Upload artifact
           uses: actions/upload-pages-artifact@v3
           with:
             path: _site

     deploy:
       needs: build
       runs-on: ubuntu-latest
       environment:
         name: github-pages
         url: ${{ steps.deployment.outputs.page_url }}
       steps:
         - name: Deploy to GitHub Pages
           id: deployment
           uses: actions/deploy-pages@v4
   ```

4. Click **Commit changes...**, then **Commit changes** again in the popup.

> This file is also in your unzipped folder at `.github/workflows/publish.yml` if you'd rather
> open it and copy from there.

### 5. Turn on GitHub Pages

1. In your repository, click **Settings** (top row of tabs).
2. In the left sidebar, click **Pages**.
3. Under **Build and deployment → Source**, choose **GitHub Actions** from the dropdown.

That's the whole setup. Click the **Actions** tab to watch it build — the first run takes about
two minutes. A green checkmark means it worked.

Your site is now live at `https://YOUR-USERNAME.github.io`. Open it and click through all five
pages before moving on.

> **If the build fails** (red X), click the failed run and then the red step to see why. The
> most common cause is a typo in `publish.yml` — YAML is picky about indentation. Re-paste it
> carefully. Nothing is broken; just fix the file and it rebuilds.

---

## Part 2 — Pointing ekhenry.com at your new site

Right now `ekhenry.com` redirects to Google Sites. You'll change that wherever you registered
the domain — likely Squarespace Domains (which absorbed Google Domains), or wherever you set up
the current redirect.

**Do this only after Part 1 works.** Once you switch, the Google Sites version is no longer
reachable at ekhenry.com.

### 1. Remove the old redirect

In your registrar's DNS settings, delete the forwarding rule sending `ekhenry.com` to
`sites.google.com`. Also delete any existing `A` or `CNAME` records for the root domain and for
`www`.

### 2. Add these records

For the root domain, four `A` records — these are GitHub's servers:

| Type | Name / Host | Value           |
|------|-------------|-----------------|
| A    | `@`         | 185.199.108.153 |
| A    | `@`         | 185.199.109.153 |
| A    | `@`         | 185.199.110.153 |
| A    | `@`         | 185.199.111.153 |

And one `CNAME` record for `www`:

| Type  | Name / Host | Value                     |
|-------|-------------|---------------------------|
| CNAME | `www`       | `YOUR-USERNAME.github.io` |

### 3. Tell GitHub about the domain

1. Repository → **Settings** → **Pages**.
2. Under **Custom domain**, type `ekhenry.com` and click **Save**.
3. Wait for the DNS check to pass — usually minutes, occasionally a few hours.
4. Once it passes, tick **Enforce HTTPS**.

The `CNAME` file already in your repository contains `ekhenry.com`, so this should be recognized
right away. **Don't delete that file** — the site needs it.

### 4. Retire the Google Site

Leave it up a couple of weeks in case anyone has it bookmarked, then delete it.

---

## Part 3 — Editing the site from your browser

This is the part you'll actually use. No terminal, no git.

### Changing text

1. Go to your repository on github.com.
2. Click the file you want to edit — say `research.qmd`.
3. Click the **pencil icon** (top right of the file view).
4. Make your changes.
5. Scroll down, click the green **Commit changes...** button, then **Commit changes**.

That's it. GitHub rebuilds and republishes within a minute or two. Refresh your site to see it.

Nothing you do here can permanently break anything — every version is saved, and you can always
restore an earlier one from the **History** button on any file.

### Adding photos

1. Navigate into `assets` → `photos` in your repository.
2. Click **Add file** → **Upload files**.
3. Drag your images in. Name them exactly as `personal.qmd` expects:
   `ec471-lecture.jpg`, `bryant-denny.jpg`, `naval-academy.jpg`, `tailgate.jpg`,
   `alcatraz.jpg`, `first-year-finals.jpg`.
4. Click **Commit changes**. This replaces the gray placeholders.

### The two Dropbox links

Your **CV** and **job market paper** are Dropbox links, not files in this repository, so you can
update those documents without touching the site at all. Both live in `_variables.yml`:

```yaml
cv_url: "https://www.dropbox.com/.../EmmaKateHenry_CV.pdf?dl=1"
jmp_url: "https://www.dropbox.com/.../henry_jmp.pdf?dl=1"
jmp_public: false
```

To get a link: right-click the file in Dropbox → **Copy link** → change the trailing `dl=0` to
`dl=1` so it downloads rather than opening Dropbox's preview page. Then edit `_variables.yml`
on GitHub with the pencil icon and paste it in.

**To update the CV later: overwrite the file in Dropbox in place** — same filename, replace the
existing file. The link keeps working and serves the new version, and you never touch the
website. Don't delete and re-upload; that creates a new URL and breaks the link.

When the JMP is ready to circulate, open `research.qmd`, replace the "available on request" line
with the commented-out PDF link just below it, and commit.

### Where things live

| What you want to change            | File             |
|------------------------------------|------------------|
| Bio, education, awards, contact    | `index.qmd`      |
| Papers, presentations, references  | `research.qmd`   |
| Courses, evaluations               | `teaching.qmd`   |
| CV page                            | `cv.qmd`         |
| Photos page                        | `personal.qmd`   |
| CV / JMP links                     | `_variables.yml` |
| Navigation, site title             | `_quarto.yml`    |
| Colors, fonts, spacing             | `styles.scss`    |

Search the `.qmd` files for `TODO(lily)` — those mark everything left for you to fill in.

---

## Optional: previewing before you publish

You don't need this. Committing on GitHub and refreshing the live site works fine, and mistakes
are trivially reversible.

But if you'd rather see changes before they go live: **you already have Quarto** if you use
RStudio — it's bundled. Open the site folder in RStudio, open any `.qmd`, and click **Render**.
There's also a **Build → Render Website** option. This renders locally without publishing
anything.

If you go this route, note that the `_site` folder Quarto generates locally is throwaway output —
don't upload it to GitHub. The `.gitignore` file handles this automatically when using git, but
since you're uploading through the browser, just don't drag `_site` in.

---

## Notes

- **Cost: $0/year.** GitHub Pages is free on public repositories, including custom domains and
  HTTPS. Limits are 1 GB site size and a soft 100 GB/month bandwidth cap — an academic site uses
  a rounding error of that. Your only ongoing cost is the domain registration you already pay.
- **These files are portable.** If you ever want to move to Cloudflare Pages, Netlify, or
  anywhere else, the same folder works unchanged — only the deploy step differs. You're not
  locked in.
- **A note on Netlify**, since you asked: its free plan is now credit-based — 300 credits a
  month, and each published deploy costs 15. That caps you at roughly 20 updates per month, and
  when credits run out the site *pauses* until the next billing cycle. For a site you'll be
  editing through job market season, that's a bad failure mode. GitHub Pages has no equivalent
  deploy cap. ([Netlify pricing](https://www.netlify.com/pricing/),
  [credit rates](https://docs.netlify.com/manage/accounts-and-billing/billing/billing-for-credit-based-plans/credit-based-pricing-plans/))
