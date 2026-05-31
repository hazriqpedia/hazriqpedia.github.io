# hazriqpedia.github.io – My Hugo Dev Blog ✍️

This site is powered by [Hugo](https://gohugo.io/) and deployed via GitHub Pages at:

🌐 **https://hazriqpedia.github.io**

## 🚀 Quick Start Guide

### Step 1: Create Post
```bash
hugo new posts/your-post-title.md
```

### Step 2: Write & Preview Locally
```bash
hugo server --buildDrafts
```
Open: [http://localhost:1313](http://localhost:1313)

### Step 3: Publish
Set `draft = false` in the post front matter, then merge to `main`. GitHub Actions handles the build and deploy automatically.

## 🛠️ Setup & Configuration

### Requirements
- [Install Hugo](https://gohugo.io/getting-started/installing/)
- Git
- A GitHub repo (like this one)

### Site Configuration
Edit `hugo.toml` to customize:
- Site title/description
- Menus
- Theme settings (using PaperMod)

## ✍️ Content Management

### Writing Posts
1. Create with: `hugo new posts/title.md`
2. Edit the front matter (`title`, `date`, `draft = false`)
3. Write your content in Markdown
4. Merge to `main` — deploy happens automatically

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
