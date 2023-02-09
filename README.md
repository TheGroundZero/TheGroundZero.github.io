# GitHub Pages

This is the [GitHub Pages](https://pages.github.com/ "GitHub Pages") repository for https://sequr.be.

# Build

This site is built using [Hugo](https://gohugo.io "Hugo").

The site build is pushed to this repo using the following workflow:

```yaml
---
# 
# https://gohugo.io/hosting-and-deployment/hosting-on-github/
# 
name: github pages

on:
  push:
    branches:
      - main  # Set a branch to deploy
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod
      
      - name: Exiftool Scrub
        uses: TheGroundZero/exiftool-scrub@v1.2.0
        env:
          SUB_DIR: /content
        continue-on-error: true

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/main'
        with:
          deploy_key: ${{ secrets.ACTIONS_DEPLOY_KEY }}
          external_repository: TheGroundZero/TheGroundZero.github.io
          publish_branch: main
          publish_dir: ./public
```
