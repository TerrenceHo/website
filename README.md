# My Blog

Serves as a place to put down my writings.

## Setup

This repo uses git submodules for the `public` folder, which contains the Hugo
generated site. The submodule points to
[TerrenceHo/terrenceho.github.io](https://github.com/TerrenceHo/terrenceho.github.io),
which allows GitHub Pages to autoamtically deploy my Hugo generated website.

Clone with submodules included:

```
git clone --recurse-submodules git@github.com:TerrenceHo/website.git
```

If you already cloned without `--recurse-submodules`, initialize them
after the fact:

```
git submodule update --init --recursive
```

Ensure the public submodule is checked out to `master`.

## Making edits

I use [ox-hugo](https://ox-hugo.scripter.co/) to write my pages in Emacs
Org-mode, then transpile it to Markdown. All content is written in
`content_org/post.org`.

This ensures that the source of my posts is separated from the build artifacts
that Hugo generates.

## Deploying changes

After making changes to content or layouts, build and publish the site
with:

```
./deploy.sh
```

This runs `hugo --minify` to rebuild the site into `public/`, commits
the changes in the `public` submodule, and pushes them to its `master`
branch for GitHub Pages to rebuild the site.
