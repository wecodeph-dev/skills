---
name: weplan
description: Use when the user asks to see, open, view, preview, share, or publish an HTML file or page in a browser, asks for "a link" to HTML output, or asks to send an HTML report/dashboard/prototype/chart/mockup to someone, and whenever you have produced an HTML artifact that is easier to review as a link than as a file. Also use when the user shares a plan.wecodeph.dev link and wants it read, summarized, reviewed, or changed, or asks to update a page they already have a weplan link for. Publishes self-contained HTML as a shareable URL with the weplan CLI (npx weplan), retrieves pages from links, and updates pages in place.
license: MIT
metadata:
  author: wecodeph-dev
  homepage: https://plan.wecodeph.dev
---

# weplan: share HTML as a link

weplan turns one HTML file into a URL like `https://plan.wecodeph.dev/p/<slug>` with a single command. Nothing to install; run it with `npx -y weplan`.

## Workflow

1. **Make the HTML self-contained.** One file: inline all CSS and JS, images as data URIs or absolute `https://` URLs, no references to local files. Do not use `localStorage`/`sessionStorage`; pages run in a sandboxed origin where they throw.
2. **Publish** and give the user the link:
   ```bash
   npx -y weplan create <file.html> --json
   ```
   Parse the JSON output; present `url` to the user as a clickable link. **Keep the `slug`**; you need it for follow-ups.
3. **Revise in place** when the user asks for changes, so the link they already have keeps working:
   ```bash
   npx -y weplan update <slug> <file.html> --json
   ```
   Do not create a new page for a revision unless the user asks for a separate one.

## Page style

When you write the page yourself (a spec, plan, report, audit or dashboard), read `styleguide.md` in this skill's folder before writing any HTML and follow it. In short: a black page by default with a light mode toggle, grayscale with color only for status and problems, cards with hairline borders, and a layout that fills a wide screen and collapses to one column on a phone. It includes starter CSS and a screenshot check to run before publishing.

Do not restyle an HTML file the user gives you to publish, and follow the user's own design when they ask for one.

## Reading a shared link

When the user gives you a `https://plan.wecodeph.dev/p/<slug>` link, do not fetch it with curl or a browser tool; private pages return a sign-in redirect. Use the CLI, which sends the user's credentials:

```bash
npx -y weplan get <url> > page.html          # raw HTML to a file
npx -y weplan get <url> --json               # {slug, url, visibility, size, html}
npx -y weplan get <url> --assets <dir>       # <dir>/index.html plus embedded images as files
```

Charts and diagrams are usually data in the HTML (SVG, Mermaid, chart configs), so the text is the source of truth: read the values and labels from it. If the page embeds raster images (screenshots, exported PNGs as data URIs), use `--assets <dir>` and open the extracted `img-N.png` files to see them.

Public pages need no login. Private pages work when the user is the owner or on the allow list; a 401 or 403 means the user must sign in (`npx -y weplan login`) or ask the owner for access. Do not try to bypass it. To change a shared page: `get` it, edit the file, then `update <slug> page.html` so the link stays the same.

## Options

| Flag | When to use it |
|---|---|
| `--name <slug>` | User wants a stable, readable URL (`a-z 0-9 -`, 3-63 chars). Fails with 409 if taken. Requires sign-in. |
| `--private` | Content is sensitive: only the owner and allow-listed emails can view. Requires sign-in. |
| `--allow a@x.com,b@y.com` | Share a private page with specific people (implies `--private`). |
| `weplan access <slug> --public\|--private --allow … --revoke …` | Change who can see an existing page. |
| `weplan list` · `weplan delete <slug>` · `weplan whoami` | Housekeeping. |
| `-` as the file | Read the HTML from stdin. |

Run `npx -y weplan --help` if unsure about a flag.

## Authentication

- If `WEPLAN_TOKEN` is set or the user has run `weplan login` on this machine, commands are authenticated: pages persist, and can be named and private.
- Otherwise uploads are **anonymous**: public, unnamed, deleted after 12 hours. That is fine for a quick look; tell the user the link expires.
- If `--name`/`--private` fail with "sign in required", ask the user to run `npx -y weplan login` once (it prints a link and a code to confirm in a browser). Do not retry in a loop, and do not try to complete the login yourself.

## Errors

Errors are printed to stderr as `{"error": "..."}` with exit code 1. Report the message to the user rather than retrying blindly. Common cases: the file is not `.html` (write it as `.html`), the page exceeds 2 MB (1 MB anonymous; trim inline assets), or a quota is reached (`weplan list` then `weplan delete` old pages).
