# How to Build a Technical Writing Portfolio Site
**Author:** Jane Doe

**Purpose:** Step-by-step guide for building a professional technical writing portfolio using MkDocs and GitHub Pages.

> **Before you start:** Replace all instances of `jane-doe` and `Jane Doe` in this guide with your own name and GitHub username.

**Estimated time:** 1–2 hours

---

## Quick Reference

| Step | What You're Doing |
|---|---|
| Step 1 | Install Python |
| Step 2 | Install MkDocs and Material theme |
| Step 3 | Create the project and preview it locally |
| Step 4 | Configure your site settings |
| Step 5 | Add custom styling |
| Step 6 | Create your pages and writing samples |
| Step 7 | Add a .gitignore file |
| Step 8 | Set up Git and push to GitHub |
| Step 9 | Deploy to GitHub Pages |
| Step 10 | Update the site whenever you make changes |

---

## Prerequisites

Before starting, make sure you have:

- A Mac or Windows computer
- An internet connection
- A GitHub account (free) — sign up at https://github.com
- VS Code installed — download at https://code.visualstudio.com
- Your writing samples ready (as text or Word documents)

---

## Tools and Technologies

| Tool | Purpose |
|---|---|
| Python 3 | Required to run MkDocs |
| MkDocs 1.6.1 | Static site generator for documentation |
| Material for MkDocs | Theme for professional styling |
| VS Code | Text editor for writing and editing files |
| Git | Version control — tracks changes to your files |
| GitHub | Remote repository — stores your files online |
| GitHub Pages | Free static site hosting |

---

## Project Structure

When complete, your project folder will look like this:

```
my-portfolio/
├── mkdocs.yml                  # Site configuration file
├── .gitignore                  # Tells Git to ignore the site/ folder
└── docs/
    ├── index.md                # Home page
    ├── about.md                # About me page
    ├── portfolio.md            # Portfolio overview page
    ├── work-with-me.md         # Hire me / contact page
    ├── stylesheets/
    │   └── extra.css           # Custom styling
    └── samples/
        ├── kb-article.md       # KB / Troubleshooting sample
        ├── integration-guide.md # Integration guide sample
        └── api-reference.md    # API reference sample
```

---

## Step 1 of 10 — Install Python

Python is the programming language that MkDocs runs on. You need to install it before anything else.

Download and install Python 3 from https://www.python.org/downloads/

Once installed, open your terminal and verify it worked:

```
python3 --version
```

**What this command does:** Asks Python to tell you which version is installed. If it prints something like `Python 3.14.3`, the installation was successful. If it says "command not found", Python did not install correctly.

---

## Step 2 of 10 — Install MkDocs and Material Theme

MkDocs is the tool that turns your Markdown files into a website. The Material theme makes it look polished and professional.

```
pip3 install mkdocs mkdocs-material
```

**What this command does:** `pip3` is Python's package manager — it installs software packages. This command installs two packages at once: `mkdocs` (the site generator) and `mkdocs-material` (the theme). Think of it like installing two apps in one go.

Verify MkDocs installed correctly:

```
mkdocs --version
```

**What this command does:** Asks MkDocs to print its version number. If it responds with something like `mkdocs, version 1.6.1`, the installation was successful.

---

## Step 3 of 10 — Create the Project

```
mkdocs new my-portfolio
```

**What this command does:** Creates a new folder called `my-portfolio` on your computer and populates it with two things MkDocs needs to get started — a `mkdocs.yml` configuration file and a `docs/` folder with a starter `index.md` page.

```
cd my-portfolio
```

**What this command does:** `cd` stands for "change directory." This moves your terminal inside the `my-portfolio` folder so all subsequent commands run in the right place. If you skip this step, the following commands won't find your project.

Now open the project in VS Code:

```
code .
```

**What this command does:** Opens the current folder (`.` means "here") in VS Code. If this command doesn't work, open VS Code manually, go to File → Open Folder, and select the `my-portfolio` folder. To enable the `code` command in future, open VS Code, press `Cmd + Shift + P`, type "Shell Command", and select "Install 'code' command in PATH".

Preview the site locally:

```
mkdocs serve
```

**What this command does:** Starts a local web server on your computer and builds your site so you can preview it in a browser. It also watches for changes — every time you save a file, the browser refreshes automatically. Nothing is published to the internet yet; it is only visible on your machine.

Open your browser and go to: http://127.0.0.1:8000

To stop the server at any time, press `Ctrl + C` in the terminal.

---

## Step 4 of 10 — Configure mkdocs.yml

`mkdocs.yml` is your site's control file. It tells MkDocs what to call your site, which theme to use, and how to organize the navigation.

Open `mkdocs.yml` in VS Code and replace everything in it with the following. Replace `Full Name` with your name:

```yaml
site_name: Full Name | Technical Writer
site_description: Portfolio of Jane Doe, Senior Technical Writer

theme:
  name: material
  palette:
    scheme: default
    primary: white
    accent: green
  font:
    text: Inter
    code: Roboto Mono
  features:
    - navigation.tabs
    - navigation.top
    - navigation.sections
    - toc.integrate
    - header.autohide

extra_css:
  - stylesheets/extra.css

nav:
  - Home: index.md
  - About: about.md
  - Portfolio:
    - Overview: portfolio.md
    - KB Article: samples/kb-article.md
    - Integration Guide: samples/integration-guide.md
    - API Reference: samples/api-reference.md
  - Work With Me: work-with-me.md
```

Save with `Cmd + S` (Mac) or `Ctrl + S` (Windows).

**What each section does:**
- `site_name` — the title that appears in the browser tab and site header
- `theme` — sets the visual theme, color scheme, and fonts
- `extra_css` — loads your custom stylesheet for additional styling
- `nav` — defines the navigation structure and which pages appear in the menu

---

## Step 5 of 10 — Add Custom Styling

Create the stylesheets folder and file. Stop the server first with `Ctrl + C`, then run:

```
mkdir docs/stylesheets
```

**What this command does:** `mkdir` stands for "make directory." This creates a new folder called `stylesheets` inside your `docs/` folder.

```
touch docs/stylesheets/extra.css
```

**What this command does:** `touch` creates a new empty file at the path you specify. This creates a blank CSS file called `extra.css` inside the `stylesheets` folder you just created.

Open `docs/stylesheets/extra.css` in VS Code and paste the following:

```css
:root {
  --md-primary-fg-color: #ffffff;
  --md-primary-fg-color--light: #ffffff;
  --md-primary-fg-color--dark: #f5f5f5;
  --md-primary-bg-color: #1a1a1a;
  --md-accent-fg-color: #4caf7d;
}

.md-header {
  border-bottom: 2px solid #4caf7d;
  box-shadow: none;
}

.md-header__title {
  font-weight: 700;
  letter-spacing: 0.02em;
}

.md-tabs {
  background-color: #f9f9f9;
  border-bottom: 1px solid #e0e0e0;
}

.md-tabs__link {
  color: #1a1a1a !important;
  font-weight: 500;
}

.md-tabs__link--active {
  color: #4caf7d !important;
  border-bottom: 2px solid #4caf7d;
}

.md-content {
  max-width: 860px;
}

.md-typeset h1 {
  font-weight: 800;
  color: #1a1a1a;
  border-bottom: 2px solid #4caf7d;
  padding-bottom: 0.3em;
}

.md-typeset h2 {
  font-weight: 700;
  color: #1a1a1a;
  margin-top: 2em;
}

.md-typeset a {
  color: #4caf7d;
}

.md-typeset a:hover {
  color: #2e7d52;
}

.md-typeset table {
  border: 1px solid #e0e0e0;
  border-radius: 4px;
}

.md-typeset thead {
  background-color: #f0faf4;
}

.md-typeset code {
  background-color: #f0faf4;
  color: #1a1a1a;
  border-radius: 3px;
}
```

This gives your site a clean white and pastel green color scheme. To change the accent color, replace all instances of `#4caf7d` with a color of your choice.

---

## Step 6 of 10 — Create the Pages

### Home page (docs/index.md)

This is the first page visitors see. Keep it short — a brief intro about who you are, what you specialize in, and links to your Portfolio and About pages.

### About page (docs/about.md)

Your background, skills, and experience. 

> **Important:** Always add a blank line before bullet point lists in Markdown, otherwise they render as plain text instead of bullets. For example:
> 
> Correct:
> ```
> ## Skills
> 
> - Skill one
> - Skill two
> ```
> Incorrect:
> ```
> ## Skills
> - Skill one
> - Skill two
> ```

### Portfolio page (docs/portfolio.md)

An overview page with a short description of each writing sample and a link to each one.

### Writing samples (docs/samples/)

Create the samples folder and files:

```
mkdir docs/samples
```

**What this command does:** Creates a `samples` folder inside `docs/` to keep all your writing samples organized in one place.

```
touch docs/samples/kb-article.md
touch docs/samples/integration-guide.md
touch docs/samples/api-reference.md
```

**What this command does:** Creates three empty Markdown files — one for each writing sample. You will add your content to each file in VS Code.

Each sample page should include a metadata table at the top (article type, audience, product) followed by the full content formatted in Markdown.

### Work With Me page (docs/work-with-me.md)

A brief page with your key skills, what you bring to a team, and a link to your LinkedIn profile. Avoid including your email address and phone number directly on the site — use LinkedIn as your contact point instead.

---

## Step 7 of 10 — Add a .gitignore File

```
echo "site/" > .gitignore
```

**What this command does:** Creates a file called `.gitignore` in your project and writes `site/` into it. Git reads this file and ignores any folder or file listed in it. The `site/` folder contains the built HTML version of your site — it does not need to be committed to GitHub because `mkdocs gh-deploy` handles that separately. Without this file, Git would commit hundreds of unnecessary files every time you deploy.

---

## Step 8 of 10 — Set Up Git and Push to GitHub

### Configure Git

```
git config --global user.name "Jane Doe"
```

**What this command does:** Sets your name in Git's global configuration. "Global" means it applies to every Git project on your computer. Your name gets attached to every commit (save point) you make so that anyone looking at the project history can see who made each change. Replace `Jane Doe` with your real name.

```
git config --global user.email "janedoe@email.com"
```

**What this command does:** Sets your email in Git's global configuration. Git attaches this to every commit you make. It is also how GitHub links your commits to your GitHub account, so use the same email you signed up to GitHub with.

### Initialize the repository

```
git init
```

**What this command does:** Initializes a brand new Git repository inside your `my-portfolio` folder. It creates a hidden `.git` folder that Git uses to track all changes to your files from this point forward. Think of it as telling Git "start watching this folder." You only ever run this once per project.

### Connect to GitHub

First, create a new repository on GitHub:

1. Go to https://github.com/new
2. Set the repository name to `my-portfolio`
3. Leave everything else as default
4. Click **Create repository**

Then connect your local project to GitHub:

```
git remote add origin https://github.com/jane-doe/my-portfolio.git
```

**What this command does:** Tells your local Git repository where to send your files when you push. `remote` refers to the GitHub repository online. `origin` is just a nickname for that remote location — it's the standard convention. Replace `jane-doe` with your GitHub username.

### Push to GitHub

```
git add .
```

**What this command does:** Stages all the files in your project for committing. The `.` means "everything in the current folder." Think of staging as putting files in a box before sealing and sending it — you're telling Git which files to include in your next commit.

```
git commit -m "Initial portfolio site"
```

**What this command does:** Creates a commit — a permanent save point in your project's history — with a message describing what changed. The `-m` flag lets you write the message inline. Good commit messages are short and descriptive. This is like sealing and labelling the box.

```
git branch -M main
```

**What this command does:** Renames your current branch to `main`. A branch is like a separate version of your project. By convention, the primary branch is called `main`. This command makes sure your branch follows that convention before pushing.

```
git push -u origin main
```

**What this command does:** Sends your committed files to GitHub. `push` uploads your local commits to the remote repository. `origin` is the nickname for your GitHub repo. `main` is the branch you're pushing. The `-u` flag sets `origin main` as the default so future pushes only need `git push`.

> **Note:** When prompted for a password, do not enter your GitHub account password — GitHub no longer accepts it for terminal authentication. Instead, generate a Personal Access Token at https://github.com/settings/tokens (choose Classic token, select the `repo` scope, set an expiration). Copy the token and paste it as your password. The terminal will not show any characters as you type — this is normal.

---

## Step 9 of 10 — Deploy to GitHub Pages

```
mkdocs gh-deploy
```

**What this command does:** Does two things at once — builds your Markdown files into a complete HTML website, then pushes that HTML to a special branch on GitHub called `gh-pages`. GitHub Pages reads from this branch to serve your live site. You do not need to touch the `gh-pages` branch manually.

Then enable GitHub Pages in your repository settings:

1. Go to your repository on GitHub
2. Click **Settings**
3. Click **Pages** in the left sidebar
4. Under **Source**, select **Deploy from a branch**
5. Under **Branch**, select **gh-pages** and click **Save**

Your site will be live at:
```
https://jane-doe.github.io/my-portfolio/
```

It may take 2–3 minutes to go live the first time.

> **Note:** GitHub Pages is only available for public repositories on free GitHub accounts. To make your repository public, go to Settings → scroll to Danger Zone → Change visibility → Make public.

---

## Step 10 of 10 — Updating the Site

Every time you make changes to your content or configuration, run these four commands in order:

```
git add .
```
**What this does:** Stages all changed files for committing.

```
git commit -m "describe what you changed"
```
**What this does:** Creates a save point with a description of your changes. Write something meaningful like "Update about page" or "Add new writing sample".

```
git push origin main
```
**What this does:** Uploads your source files (Markdown, configuration, CSS) to the `main` branch on GitHub.

```
mkdocs gh-deploy
```
**What this does:** Rebuilds the site and updates the live version on GitHub Pages. If you skip this step, your live site will not reflect your changes even though your files are saved on GitHub.

---

## Troubleshooting

### `code .` not working in terminal
The VS Code command line tool is not installed. Open VS Code, press `Cmd + Shift + P`, type "Shell Command", and select "Install 'code' command in PATH". Then try again.

### GitHub asking for a password and rejecting it
GitHub no longer accepts your account password in the terminal. Generate a Personal Access Token at https://github.com/settings/tokens (Classic token, `repo` scope) and use that as your password instead.

### `site/` folder being committed to GitHub
You either skipped Step 7 or ran `git add .` before creating the `.gitignore` file. To fix it, make sure `.gitignore` contains `site/`, then run:
```
git rm -r --cached site/
git add .
git commit -m "Remove site folder from tracking"
git push origin main
```

### Bullet points rendering as plain text
You are missing a blank line before your bullet list. In Markdown, always add a blank line between a heading or paragraph and the bullet points below it.

### Warning about MkDocs 2.0
This warning appears when using the Material theme with MkDocs 1.6.x. It is about a future version of MkDocs and does not affect your current site. It is safe to ignore.

### Pages not appearing in the nav warning
Every page in your `docs/` folder must be listed in the `nav` section of `mkdocs.yml`. If you add a new file, remember to add it to the nav as well.

