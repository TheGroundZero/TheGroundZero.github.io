# GitHub Pages

This is the [GitHub Pages](https://pages.github.com/ "GitHub Pages") repository for [https://sequr.be](https://sequr.be).

## Build

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
  build:
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        with:
          submodules: recursive  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0         # Fetch all history for .GitInfo and .Lastmod
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
      - name: Cache
        uses: actions/cache@v2
        with:
          path: /tmp/hugo_cache
          key: ${{ runner.os }}-hugomod-${{ hashFiles('**/go.sum') }}
          restore-keys: |
            ${{ runner.os }}-hugomod-
      - name: Build
        run: hugo --minify
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: github.ref == 'refs/heads/main'
        with:
          deploy_key: ${{ secrets.ACTIONS_DEPLOY_KEY }}
          external_repository: TheGroundZero/TheGroundZero.github.io
          publish_branch: main  # default: gh-pages
          publish_dir: ./public
```
