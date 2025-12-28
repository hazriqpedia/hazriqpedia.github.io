# hazriqpedia.github.io – My Hugo Dev Blog ✍️

This site is powered by [Hugo](https://gohugo.io/) and deployed via GitHub Pages at:

🌐 **https://hazriqpedia.github.io**

## 🚀 Quick Start Guide

### Step 1: Create Post
```bash
hugo new posts/your-post-title.md
```

### Step 2: Start Local Server
```bash
hugo server --buildDrafts
```
Open: [http://localhost:1313](http://localhost:1313)

### Step 3: Build
```bash
hugo
```

### Step 4: Navigate to Public
```bash
cd public
```

### Step 5: Commit & Deploy
```bash
git add .
git commit -m "New post: your-post-title"
git push origin gh-pages --force
```

## 🛠️ Setup & Configuration

### Requirements
- [Install Hugo](https://gohugo.io/getting-started/installing/)
- Git
- A GitHub repo (like this one)

### One-time Setup (Optional - Git Worktree)
```bash
git worktree add -B gh-pages public origin/gh-pages
```
This makes `public/` track the `gh-pages` branch directly.

### Site Configuration
Edit `hugo.toml` to customize:
- Site title/description
- Menus
- Theme settings (using PaperMod)

## ✍️ Content Management

### Writing Posts
1. Create with: `hugo new posts/title.md`
2. Edit the front matter (`title`, `date`, `draft: false`)
3. Write your content in Markdown
4. Follow the workflow above to publish

### Adding Pages
```bash
hugo new about.md
# or
hugo new about/_index.md
```

### Static Assets
- Put files in `static/`
- Example: `static/images/me.png` → use as `/images/me.png` in Markdown

### Removing Content
```bash
rm content/posts/my-old-post.md
```

## 🙌 Credits

- Generator: [Hugo](https://gohugo.io)
- Theme: [PaperMod](https://github.com/adityatelange/hugo-PaperMod)
