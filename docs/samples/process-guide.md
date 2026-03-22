# How to build a technical writing portfolio site


**Author:** Evangeline Philip

**Audience:** Technical writers building a portfolio site

**Estimated time:** 1 to 2 hours

This guide walks you through building a professional technical writing portfolio site using MkDocs, the Material theme, and GitHub Pages. By the end, you will have a live site at `https://your-username.github.io/my-portfolio/`.

!!! note "Before you start"
    Replace all instances of `jane-doe` and `Jane Doe` in this guide with your own GitHub username and name.

---

## Quick reference

| Step | What you are doing | Tool |
|---|---|---|
| [Step 1: Install Python](#step-1-install-python) | Install the language MkDocs runs on | Terminal |
| [Step 2: Install MkDocs](#step-2-install-mkdocs-and-material-theme) | Install the site generator and theme | Terminal |
| [Step 3: Create the project](#step-3-create-the-project) | Scaffold and preview locally | Terminal + Browser |
| [Step 4: Configure settings](#step-4-configure-mkdocsyml) | Set up your site name, theme, and nav | VS Code |
| [Step 5: Add custom styling](#step-5-add-custom-styling) | Apply your color scheme and fonts | VS Code |
| [Step 6: Create pages](#step-6-create-the-pages) | Write your home page, about, and samples | VS Code |
| [Step 7: Add .gitignore](#step-7-add-a-gitignore-file) | Prevent the built site from being committed | Terminal |
| [Step 8: Set up Git](#step-8-set-up-git-and-push-to-github) | Version control and push to GitHub | Terminal |
| [Step 9: Deploy](#step-9-deploy-to-github-pages) | Go live on GitHub Pages | GitHub |
| [Step 10: GitHub Actions](#step-10-set-up-github-actions-for-automatic-deployment) | Automate deployment on every push | GitHub + VS Code |
| [Step 11: Update the site](#step-11-update-the-site) | Push changes and redeploy | Terminal |

---

## Prerequisites

Before starting, confirm you have the following:

| Requirement | Details |
|---|---|
| **Computer** | Mac or Windows |
| **Internet connection** | Required throughout |
| **GitHub account** | Free account at [github.com](https://github.com) |
| **VS Code** | Download at [code.visualstudio.com](https://code.visualstudio.com) |
| **Writing samples** | Ready as text or Word documents |
| **Personal Access Token** | Generate at [github.com/settings/tokens](https://github.com/settings/tokens). Choose Classic token with `repo` and `workflow` scopes. |

!!! tip "Generate your token before you start"
    GitHub no longer accepts account passwords in the terminal. You will need your [Personal Access Token](https://github.com/settings/tokens) in Step 8 when pushing to GitHub for the first time. Generate it now so you have it ready.

---

## Tools and technologies

| Tool | Purpose |
|---|---|
| Python 3 | Required to run MkDocs |
| MkDocs | Static site generator that converts Markdown files into a website |
| Material for MkDocs | Professional theme for MkDocs |
| VS Code | Text editor for writing and editing files |
| Git | Version control that tracks changes to your files |
| GitHub | Remote repository that stores your files online |
| GitHub Pages | Free static site hosting |
| GitHub Actions | Automates site deployment on every push to main |

---

## Project structure

When complete, your project folder will look like this:

```
my-portfolio/
├── mkdocs.yml                    # Site configuration file
├── .gitignore                    # Tells Git to ignore the site/ folder
├── .github/
│   └── workflows/
│       └── deploy.yml            # GitHub Actions deployment workflow
└── docs/
    ├── index.md                  # Home page
    ├── about.md                  # About me page
    ├── portfolio.md              # Portfolio overview page
    ├── work-with-me.md           # Hire me / contact page
    ├── stylesheets/
    │   └── extra.css             # Custom styling
    └── samples/
        ├── kb-article.md         # KB / Troubleshooting sample
        ├── integration-guide.md  # Integration guide sample
        └── api-reference.md      # API reference sample
```

---

## Step 1: Install Python

Python is the programming language that MkDocs runs on. You need to install it before anything else.

Download and install Python 3 from [python.org/downloads](https://www.python.org/downloads/). On the download page, select the installer for your operating system.

!!! warning "Windows users"
    During installation, check the box that says **Add Python to PATH** before clicking Install. If you skip this, Python commands will not work in your terminal.

Once installed, verify it worked. Open your terminal and run:

=== "Mac"
    ```bash
    python3 --version
    ```

=== "Windows"
    ```bash
    python --version
    ```

This asks Python to print its version number. A response like `Python 3.14.3` means the installation was successful.

If you see `command not found` (Mac) or `'python' is not recognized` (Windows), Python did not install correctly. Re-run the installer and make sure **Add Python to PATH** is checked on Windows.

---

## Step 2: Install MkDocs and Material theme

MkDocs converts your Markdown files into a website. The Material theme gives it a clean, professional look.

=== "Mac"
    ```bash
    pip3 install mkdocs mkdocs-material
    ```

=== "Windows"
    ```bash
    pip install mkdocs mkdocs-material
    ```

`pip` is Python's package manager. This command installs `mkdocs` (the site generator) and `mkdocs-material` (the theme) in one go.

Verify MkDocs installed correctly:

```bash
mkdocs --version
```

This prints the installed version number. A response like `mkdocs, version 1.6.1` means the installation was successful.

If you see `command not found`, try closing and reopening your terminal and running the command again. If it still fails, reinstall Python and make sure the **Add Python to PATH** option is enabled during setup.

---

## Step 3: Create the project

```bash
mkdocs new my-portfolio
```

This creates a new folder called `my-portfolio` on your computer and populates it with a `mkdocs.yml` configuration file and a `docs/` folder containing a starter `index.md` page.

```bash
cd my-portfolio
```

`cd` stands for "change directory." This moves your terminal into the `my-portfolio` folder so all subsequent commands run in the right place. If you skip this step, the following commands will not find your project.

Open the project in VS Code:

```bash
code .
```

This opens the current folder in VS Code. If this command does not work, open VS Code manually, go to **File > Open Folder**, and select the `my-portfolio` folder.

!!! tip "Enable the `code` command"
    Open VS Code, press `Cmd + Shift + P` (Mac) or `Ctrl + Shift + P` (Windows), type "Shell Command", and select **Install 'code' command in PATH**.

Preview the site locally:

```bash
mkdocs serve
```

This starts a local web server and builds your site for preview. Nothing is published to the internet yet. The browser refreshes automatically every time you save a file.

Open your browser and go to [http://127.0.0.1:8000](http://127.0.0.1:8000).

To stop the server at any time, press `Ctrl + C` in the terminal.

---

## Step 4: Configure mkdocs.yml

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

markdown_extensions:
  - md_in_html
  - admonition
  - pymdownx.tabbed:
      alternate_style: true

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

| Section | Purpose |
|---|---|
| `site_name` | Title shown in the browser tab and site header |
| `theme` | Sets the visual theme, color scheme, and fonts |
| `extra_css` | Loads your custom stylesheet |
| `markdown_extensions` | Enables HTML in Markdown, admonition blocks, and tabbed content |
| `nav` | Defines the navigation structure and page order |

---

## Step 5: Add custom styling

Stop the server first with `Ctrl + C`, then create the stylesheets folder:

=== "Mac"
    ```bash
    mkdir docs/stylesheets
    touch docs/stylesheets/extra.css
    ```

=== "Windows"
    ```bash
    mkdir docs\stylesheets
    type nul > docs\stylesheets\extra.css
    ```

`mkdir` creates the `stylesheets` folder inside `docs/`. The second command creates a blank `extra.css` file inside it.

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

To change the accent color, replace all instances of `#4caf7d` with a hex color of your choice. You can pick colors at [htmlcolorcodes.com](https://htmlcolorcodes.com).

---

## Step 6: Create the pages

#### Home page (docs/index.md)

This is the first page visitors see. Keep it concise: a brief introduction, your specialization, and links to your Portfolio and Work With Me pages.

#### About page (docs/about.md)

Your background, skills, and experience.

#### Portfolio page (docs/portfolio.md)

An overview page with a short description of each writing sample and a link to each one.

#### Writing samples (docs/samples/)

Create the samples folder and files:

=== "Mac"
    ```bash
    mkdir docs/samples
    touch docs/samples/kb-article.md
    touch docs/samples/integration-guide.md
    touch docs/samples/api-reference.md
    ```

=== "Windows"
    ```bash
    mkdir docs\samples
    type nul > docs\samples\kb-article.md
    type nul > docs\samples\integration-guide.md
    type nul > docs\samples\api-reference.md
    ```

`mkdir` creates the `samples` folder inside `docs/`. The remaining commands create one empty Markdown file for each writing sample.

!!! tip
    You can name your sample files anything you like and create as many as you need. Just make sure each file is listed under the `nav` section of `mkdocs.yml`, otherwise it will not appear in the navigation.

Each sample page should include a metadata table at the top (article type, audience, product) followed by the full content formatted in Markdown.

#### Work With Me page (docs/work-with-me.md)

A brief page with your key skills, what you bring to a team, and a link to your LinkedIn profile. Do not include your email address or phone number directly on the site. Use LinkedIn as your contact point instead.

---

## Step 7: Add a .gitignore file

=== "Mac"
    ```bash
    echo "site/" > .gitignore
    ```

=== "Windows"
    ```bash
    echo site/ > .gitignore
    ```

This creates a `.gitignore` file and writes `site/` into it. Git reads this file and skips anything listed in it. The `site/` folder contains the built HTML version of your site and does not need to be committed — GitHub Actions handles deployment automatically.

---

## Step 8: Set up Git and push to GitHub

#### Configure Git

```bash
git config --global user.name "Jane Doe"
```

This sets your name in Git's global configuration. It is attached to every commit you make so anyone reviewing the project history can see who made each change. Replace `Jane Doe` with your real name.

```bash
git config --global user.email "janedoe@email.com"
```

This sets your email in Git's global configuration. Use the same email you signed up to GitHub with — Git uses it to link your commits to your GitHub account.

#### Initialize the repository

```bash
git init
```

This initializes a new Git repository inside your `my-portfolio` folder. It creates a hidden `.git` folder that Git uses to track all changes from this point forward. Run this only once per project.

#### Connect to GitHub

1. Go to [github.com/new](https://github.com/new).
2. Set the repository name to `my-portfolio`.
3. Leave everything else as default.
4. Click **Create repository**.

Then connect your local project to GitHub:

```bash
git remote add origin https://github.com/jane-doe/my-portfolio.git
```

This tells your local Git repository where to send your files when you push. `origin` is the standard nickname for the remote GitHub repository. Replace `jane-doe` with your GitHub username.

#### Push to GitHub

```bash
git add .
git commit -m "Initial portfolio site"
git branch -M main
git push -u origin main
```

| Command | Purpose |
|---|---|
| `git add .` | Stages all files, marking them ready to commit |
| `git commit -m "..."` | Creates a save point in your project history with a description |
| `git branch -M main` | Renames the current branch to `main`, following GitHub convention |
| `git push -u origin main` | Sends your committed files to GitHub for the first time |

!!! note "When prompted for a password"
    Paste your [Personal Access Token](https://github.com/settings/tokens), not your GitHub account password. The terminal will not show any characters as you type. This is normal.

---

## Step 9: Deploy to GitHub Pages

Enable GitHub Pages in your repository settings:

1. Go to your repository on GitHub.
2. Click **Settings**.
3. Click **Pages** in the left sidebar.
4. Under **Source**, select **Deploy from a branch**.
5. Under **Branch**, select **gh-pages** and click **Save**.

Your site will be live at:

```
https://jane-doe.github.io/my-portfolio/
```

It may take 2 to 3 minutes to appear the first time.

!!! note
    GitHub Pages is only available for public repositories on free GitHub accounts. To make your repository public, go to **Settings > Danger Zone > Change visibility > Make public**.

---

## Step 10: Set up GitHub Actions for automatic deployment

GitHub Actions automates your deployment. Every time you push to `main`, it builds and deploys your site automatically. You will never need to run `mkdocs gh-deploy` manually.

#### Create the workflow file

In your terminal, create the required folder:

=== "Mac"
    ```bash
    mkdir -p .github/workflows
    ```

=== "Windows"
    ```bash
    mkdir .github\workflows
    ```

This creates the `.github/workflows/` folder where GitHub looks for automation workflows.

In VS Code, right-click the `workflows` folder in the sidebar, select **New File**, name it `deploy.yml`, and paste the following:

```yaml
name: Deploy Portfolio

on:
  push:
    branches:
      - main

permissions:
  contents: write

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.x'

      - name: Install MkDocs
        run: pip install mkdocs mkdocs-material

      - name: Deploy to GitHub Pages
        run: mkdocs gh-deploy --force
```

Save the file.

#### Push the workflow to GitHub

```bash
git add .
git commit -m "Add GitHub Actions deployment workflow"
git push origin main
```

#### Verify the deployment

1. Go to your repository on GitHub.
2. Click the **Actions** tab.
3. You should see a workflow called **Deploy Portfolio**. A yellow circle means it is running. A green checkmark means it succeeded.

From this point forward, every push to `main` triggers a deployment automatically.

!!! tip
    If the workflow fails, click on the failed run in the **Actions** tab to see which step caused the error and the full error message.

---

## Step 11: Update the site

Every time you make changes, run the following three commands:

```bash
git add .
git commit -m "Describe what you changed"
git push origin main
```

| Command | Purpose |
|---|---|
| `git add .` | Stages all changed files |
| `git commit -m "..."` | Creates a save point with a description |
| `git push origin main` | Pushes changes to GitHub and triggers automatic deployment |

!!! tip
    Write meaningful commit messages. "Update about page" or "Add API reference sample" is far more useful than "update" or "changes" when reviewing your history later.

---

## Troubleshooting

Expand an issue to see the resolution.

??? issue "`code .` is not working in the terminal"
    The VS Code command line tool is not installed.

    Open VS Code, press `Cmd + Shift + P` (Mac) or `Ctrl + Shift + P` (Windows), type "Shell Command", and select **Install 'code' command in PATH**. Then try again.

??? issue "GitHub is rejecting my password"
    GitHub no longer accepts your account password for terminal authentication.

    Generate a [Personal Access Token](https://github.com/settings/tokens). Choose Classic token, select the `repo` and `workflow` scopes, and set an expiration. Use the token as your password when Git prompts you.

??? issue "GitHub blocked my push because of a detected secret"
    GitHub scans commits for API keys and credentials. If one is detected, the push is blocked.

    Remove the secret from the file, then run:
    ```bash
    git reset --soft HEAD~1
    git add .
    git commit -m "Remove secret"
    git push origin main
    ```
    If the secret appears in older commits, use `git filter-repo` to scrub the full history, then force push.

??? issue "The `site/` folder is being committed to GitHub"
    You either skipped Step 7 or ran `git add .` before creating the `.gitignore` file.

    Make sure `.gitignore` contains `site/`, then run:
    ```bash
    git rm -r --cached site/
    git add .
    git commit -m "Remove site folder from tracking"
    git push origin main
    ```

??? issue "The GitHub Actions workflow is failing"
    Click on the failed run in the **Actions** tab, then click the **deploy** job to expand the steps. Find the step with the red cross and read the error message.

    | Cause | Resolution |
    |---|---|
    | Missing `permissions: contents: write` in `deploy.yml` | Add the permissions block as shown in Step 10 |
    | Personal Access Token missing the `workflow` scope | Edit your token at [github.com/settings/tokens](https://github.com/settings/tokens) and enable the `workflow` scope |
    | MkDocs install failing | Check that `mkdocs` and `mkdocs-material` are spelled correctly in the workflow file |

??? issue "Bullet points are rendering as plain text"
    You are missing a blank line before your bullet list.

    In Markdown, always add a blank line between a heading or paragraph and the bullet points below it.

??? issue "I am seeing a warning about MkDocs 2.0"
    This warning appears when using the Material theme with MkDocs 1.6.x. It refers to a future version and does not affect your current site. It is safe to ignore.

??? issue "My page is not appearing in the navigation"
    Every page in your `docs/` folder must be listed in the `nav` section of `mkdocs.yml`.

    Open `mkdocs.yml` and add the missing page to the `nav` section. The path is relative to the `docs/` folder.

<div class="feedback-section">
  <p class="feedback-question">Was this documentation helpful?</p>
  <div class="feedback-buttons">
    <button class="feedback-btn" onclick="sendFeedback('yes', this)">🫰 Yes</button>
    <button class="feedback-btn" onclick="sendFeedback('no', this)">👎 No</button>
  </div>
  <p class="feedback-thanks" style="display:none;">Thanks for your feedback!</p>
</div>

<script>
function sendFeedback(value, btn) {
  var section = btn.closest('.feedback-section');
  section.querySelector('.feedback-buttons').style.display = 'none';
  section.querySelector('.feedback-thanks').style.display = 'block';
  gtag('event', 'feedback', {
    'event_category': 'Helpfulness',
    'event_label': document.title,
    'value': value === 'yes' ? 1 : 0
  });
}
</script>