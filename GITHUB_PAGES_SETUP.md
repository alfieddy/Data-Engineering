# GitHub Pages Setup Instructions

This document explains how to enable and configure GitHub Pages for the Git Cheat Sheets repository.

## Quick Setup (5 minutes)

### Step 1: Enable GitHub Pages

1. Go to your repository on GitHub: `https://github.com/alfieddy/Data-Engineering`
2. Click on **Settings** (gear icon in top menu)
3. Scroll down to **Pages** section in the left sidebar (under "Code and automation")
4. Under **Source**, select:
   - **Source**: Deploy from a branch
   - **Branch**: `main` (or your default branch)
   - **Folder**: `/ (root)`
5. Click **Save**

### Step 2: Wait for Deployment

- GitHub will automatically build and deploy your site
- This usually takes 1-3 minutes
- You'll see a message: "Your site is live at https://alfieddy.github.io/Data-Engineering/"

### Step 3: Visit Your Site

Your Git Cheat Sheets are now live at:
```
https://alfieddy.github.io/Data-Engineering/
```

Individual pages:
- Core Commands: `https://alfieddy.github.io/Data-Engineering/git-core-commands`
- Team Workflows: `https://alfieddy.github.io/Data-Engineering/git-team-workflows`
- Advanced Commands: `https://alfieddy.github.io/Data-Engineering/git-advanced-commands`

---

## What Was Set Up

This repository includes all necessary files for GitHub Pages:

### ✅ Configuration Files

1. **`_config.yml`**
   - Jekyll configuration for GitHub Pages
   - Sets theme to `jekyll-theme-cayman`
   - Configures markdown rendering and plugins

2. **`README.md` / `index.md`**
   - Landing page with navigation to all cheat sheets
   - Includes badges, features, and usage instructions

3. **`.github/workflows/pages.yml`**
   - GitHub Actions workflow for automated deployment
   - Builds Jekyll site and deploys to GitHub Pages

4. **`assets/css/style.scss`**
   - Custom CSS for enhanced styling
   - Improves tables, code blocks, and overall appearance

### ✅ Content Files

- `git-core-commands.md` - Core Git commands reference
- `git-team-workflows.md` - Team collaboration workflows
- `git-advanced-commands.md` - Advanced Git techniques

---

## Alternative: Using GitHub Actions (Recommended)

If you want more control, you can use GitHub Actions for deployment:

### Step 1: Settings Configuration

1. Go to **Settings** → **Pages**
2. Under **Source**, select:
   - **Source**: GitHub Actions

### Step 2: Workflow Already Configured

The `.github/workflows/pages.yml` file is already configured and will:
- Automatically deploy on every push to `main` branch
- Use Jekyll to build the site
- Deploy to GitHub Pages

---

## Customization Options

### Change Theme

Edit `_config.yml` and change the theme:

```yaml
theme: jekyll-theme-minimal    # Minimalist theme
# theme: jekyll-theme-slate    # Dark theme
# theme: jekyll-theme-architect # Blueprint-style theme
# theme: jekyll-theme-cayman   # Current theme
```

Available GitHub Pages themes:
- `jekyll-theme-cayman` (current)
- `jekyll-theme-minimal`
- `jekyll-theme-slate`
- `jekyll-theme-architect`
- `jekyll-theme-dinky`
- `jekyll-theme-merlot`
- `jekyll-theme-midnight`
- `jekyll-theme-modernist`
- `jekyll-theme-tactile`
- `jekyll-theme-time-machine`

### Custom Domain

If you have a custom domain:

1. Go to **Settings** → **Pages**
2. Under **Custom domain**, enter your domain (e.g., `git-cheatsheets.example.com`)
3. Create a `CNAME` file in the repository root with your domain
4. Configure DNS with your domain provider:
   - Add a `CNAME` record pointing to `alfieddy.github.io`

---

## Troubleshooting

### Site Not Building?

1. Check **Actions** tab for build errors
2. Ensure `_config.yml` has no syntax errors
3. Make sure the branch specified in Settings matches your default branch

### Mermaid Diagrams Not Rendering?

GitHub Pages with Jekyll might not render Mermaid diagrams by default. Solutions:

1. Add Mermaid JavaScript to a custom layout
2. Use an alternative theme that supports Mermaid
3. Convert Mermaid to images before deployment

### Page Not Found?

1. Ensure files have `.md` extension
2. Check that the branch and folder are correctly set in Settings
3. Wait a few minutes for deployment to complete
4. Check the Actions tab for deployment status

### CSS Not Applied?

1. Ensure `assets/css/style.scss` exists
2. The file must start with front matter (`---`)
3. Check that `_config.yml` doesn't override styles

---

## Viewing Locally

To preview the site locally before deploying:

```bash
# Install Jekyll
gem install bundler jekyll

# Create Gemfile
cat > Gemfile << EOF
source 'https://rubygems.org'
gem 'github-pages', group: :jekyll_plugins
gem 'jekyll-theme-cayman'
EOF

# Install dependencies
bundle install

# Serve locally
bundle exec jekyll serve

# Visit http://localhost:4000
```

---

## Verification

After setup, verify your site:

1. ✅ Homepage loads: `https://alfieddy.github.io/Data-Engineering/`
2. ✅ All three cheat sheets are accessible
3. ✅ Navigation links work
4. ✅ Code blocks and tables render correctly
5. ✅ Theme is applied

---

## Updating Content

To update the cheat sheets:

1. Edit the markdown files
2. Commit and push to `main` branch
3. GitHub Pages will automatically rebuild (takes 1-3 minutes)
4. Changes will be live at your GitHub Pages URL

---

## Support

- **GitHub Pages Documentation**: https://docs.github.com/en/pages
- **Jekyll Documentation**: https://jekyllrb.com/docs/
- **Jekyll Themes**: https://pages.github.com/themes/

---

**Your Git Cheat Sheets are now ready to be hosted on GitHub Pages! 🚀**
