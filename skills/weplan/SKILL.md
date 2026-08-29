---
name: weplan
description: Publish a self-contained HTML file (report, dashboard, prototype, chart, mockup) as a shareable link with the weplan CLI, and update that page in place later. Use whenever the user wants to view, open, share, preview, or publish HTML output in a browser, or when an HTML artifact would be easier to review as a link than as a file.
license: MIT
metadata:
  author: wecodeph-dev
  homepage: https://plan.wecodeph.dev
---

# weplan — share HTML as a link

weplan turns one HTML file into a URL like `https://plan.wecodeph.dev/p/<slug>` with a single command. Nothing to install — run it with `npx -y weplan`.

## Workflow

1. **Make the HTML self-contained.** One file: inline all CSS and JS, images as data URIs or absolute `https://` URLs, no references to local files. Do not use `localStorage`/`sessionStorage` — pages run in a sandboxed origin where they throw.
2. **Publish** and give the user the link:
   ```bash
   npx -y weplan create <file.html> --json
   ```
   Parse the JSON output; present `url` to the user as a clickable link. **Keep the `slug`** — you need it for follow-ups.
3. **Revise in place** when the user asks for changes, so the link they already have keeps working:
   ```bash
   npx -y weplan update <slug> <file.html> --json
   ```
   Do not create a new page for a revision unless the user asks for a separate one.

## Options

| Flag | When to use it |
|---|---|
| `--name <slug>` | User wants a stable, readable URL (`a-z 0-9 -`, 3–63 chars). Fails with 409 if taken. Requires sign-in. |
| `--private` | Content is sensitive: only the owner and allow-listed emails can view. Requires sign-in. |
| `--allow a@x.com,b@y.com` | Share a private page with specific people (implies `--private`). |
| `weplan access <slug> --public\|--private --allow … --revoke …` | Change who can see an existing page. |
| `weplan list` · `weplan delete <slug>` · `weplan whoami` | Housekeeping. |
| `-` as the file | Read the HTML from stdin. |

Run `npx -y weplan --help` if unsure about a flag.

## Authentication

- If `WEPLAN_TOKEN` is set or the user has run `weplan login` on this machine, commands are authenticated: pages persist, and can be named and private.
- Otherwise uploads are **anonymous**: public, unnamed, deleted after 12 hours. That is fine for a quick look — tell the user the link expires.
- If `--name`/`--private` fail with "sign in required", ask the user to run `npx -y weplan login` once (it prints a link and a code to confirm in a browser). Do not retry in a loop, and do not try to complete the login yourself.

## Errors

Errors are printed to stderr as `{"error": "..."}` with exit code 1. Report the message to the user rather than retrying blindly. Common cases: the file is not `.html` (write it as `.html`), the page exceeds 2 MB (1 MB anonymous — trim inline assets), or a quota is reached (`weplan list` then `weplan delete` old pages).
