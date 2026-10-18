# andywhite.org

My personal website. It's a static site built with [Hugo](https://gohugo.io), with
hand-written templates and one plain CSS file. It needs nothing installed beyond
macOS: `_tools/site` downloads the exact Hugo version named in `.hugo-version`
into `bin/` the first time it runs.

Everything goes through one script:

```
_tools/site serve    # preview locally, including drafts
_tools/site build    # build the site into public/
_tools/site check    # verify the built site in public/
_tools/site deploy   # build, check and upload to DreamHost
```

## Write a post

Each post is a folder under `content/blog/`, with the text in `index.md` and any
images alongside it:

```
content/blog/my-new-post/
  index.md
  photo.jpg
```

Either create the folder by hand, or let Hugo fill in the front matter (use the
version in `.hugo-version`, which `_tools/site` has downloaded):

```
bin/hugo-$(cat .hugo-version)/hugo new content blog/my-new-post/index.md
```

The front matter:

```yaml
---
title: My new post
description: One line shown in the post list and to search engines.
date: 2026-10-18
slug: my-new-post      # the last part of the URL: /2026/10/18/my-new-post/
draft: true            # remove, or set to false, to publish
---
```

Refer to images by file name: `![A description](photo.jpg)`.

### Images

`serve` and `build` shrink any image over 2048px on the long edge or 1 MB to web
size. They **overwrite the file in place**, so put copies in the post folder,
never your only original. A pre-commit hook (turned on by the script) refuses to
commit an oversized image; run `_tools/site build` and stage it again.

## Preview

```
_tools/site serve
```

Then open <http://localhost:1313/>. Drafts are included, and the page reloads as
you edit. Press Ctrl-C to stop.

## Deploy

```
_tools/site deploy
```

This builds the site, runs `check`, then shows a dry run of what would change on
the server and asks before uploading (`--yes` skips the question). The upload
deletes anything on the server that isn't in the build, except DreamHost's own
`.dh-diag` and `.well-known/` (used to renew the certificate). Afterwards it
checks the live site.

It logs in as `andy2047@iad1-shared-b7-14.dreamhost.com` with an SSH key, and
fails rather than asking for a password if the key isn't set up. To set it up
on a new machine: `ssh-copy-id andy2047@iad1-shared-b7-14.dreamhost.com`.

## Redirects

- **A moved page:** list its old URLs under `aliases` in its front matter, and
  Hugo writes a redirect page at each one:

  ```yaml
  aliases:
    - /old/path/
  ```

- **The canonical address:** `static/.htaccess` sends `http://` and `www.` to
  `https://andywhite.org/`. In the DreamHost panel, the www setting must be
  "remove www" or "leave alone", never "add www", or the two loop.

## Upgrade Hugo

1. Change the version in `.hugo-version`.
2. Run `_tools/site build` (it downloads the new version) and `_tools/site check`.
3. Run `_tools/site serve` and look over the site, then commit.

Hugo makes occasional breaking changes, so read its release notes when jumping
several versions.
