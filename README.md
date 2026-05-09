# hoffnet

A clean Jekyll blog for GitHub Pages, built for writing in Markdown.

## Add A New Post

Create a Markdown file in `_posts` using this filename format:

```text
YYYY-MM-DD-post-title.md
```

Start it with front matter:

```markdown
---
title: "My Post Title"
date: 2026-05-09 09:00:00 -0600
categories: [defense]
tags: [detection, linux]
excerpt: "A short summary shown on the homepage."
---

Write the post in Markdown here.
```

## Run Locally

Install Ruby dependencies:

```bash
bundle install
```

Start the local server:

```bash
bundle exec jekyll serve
```

Open `http://localhost:4000`.

## Deploy On GitHub Pages

1. Push this project to a GitHub repository.
2. In GitHub, open `Settings -> Pages`.
3. Set the source to your default branch.
4. GitHub Pages will build the Jekyll site automatically.

If publishing from a project repository instead of `username.github.io`, set `baseurl` in `_config.yml` to `"/repository-name"`.
