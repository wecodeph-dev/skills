# weplan page style

Use this when you write the HTML yourself: a spec, plan, report, audit or dashboard. Skip it when the user hands you a finished HTML file to publish, or asks for a specific look.

The reference page is a spec with diagrams, a permission matrix and phase cards. It reads like product documentation, not like a generated report.

## Theme

- Light is the default, whatever the reader's system setting is. Set `data-theme="light"` on `<html>` and do not use `prefers-color-scheme`.
- Dark is a pure black page with near-black cards, switched by a small button at the top right that flips `data-theme`.
- Keep the choice in the URL as `?theme=dark`. Read it on load and update it with `history.replaceState` when the button is clicked, so a reload or a shared link keeps the theme. Light has no parameter.
- Do not use `localStorage`. Pages run in a sandboxed origin where it throws.

## Color

- Grayscale does almost all the work: white page, hairline borders, one muted gray for secondary text.
- Red marks a problem. Green, amber and a gray dash mark allowed, conditional and denied. No other colors.
- One inverted box (black on the light theme, near-white on dark) is allowed per diagram, for the single thing the reader should look at.

Do not use colored stripes down the side of cards, tinted callout boxes, pill badges, uppercase letter-spaced labels, gradients, emoji, or a different accent color per card. Readers see these as machine-made.

## Layout

- Use the screen. Content goes up to 1440px wide. At 1180px and wider the section links sit in a fixed column on the left.
- Plain paragraphs and lists stay capped at 78ch so lines remain readable. Diagrams, tables and card rows span the full content width.
- Turn short parallel lists into card rows (three or four across) and put related sections side by side in two columns. Phases and decisions go in a two-column grid.
- Everything collapses to one column on a phone. Use `minmax(0, 1fr)` for every grid track and `overflow-wrap: anywhere` on the body, so nothing scrolls sideways at 360px.

## Components

- **Card.** White, 1px border, 14px radius, faint shadow, 18px by 20px padding. A title, an optional muted line under it, then the content.
- **Diagram.** A light gray panel that holds rows of boxes. Each row has a small muted sentence-case label. Rows connect with a 1px vertical line, not arrowheads. Dashed boxes mean "later".
- **Status.** A 9px green dot for allowed, an amber ring for conditional, a short gray dash for denied. Put the meaning in a `title` attribute and a legend above the table.
- **Table.** No outer border. Hairline row separators, a stronger line under the header, group rows with a bold title and a muted subtitle.
- **Numbered items.** Phases and decisions use a two-digit monospace counter in faint gray (`00`, `01`), not colored circles.

## Writing

- Sentence-case headings, no section numbers.
- First person where the author is speaking. State what was not verified near the top.
- No bold label followed by a colon that restates the line. No em dashes.
- If an unslop or similar writing skill is available, apply it to the page text before publishing.

## Starter CSS

```css
:root {
  --bg: #ffffff; --panel: #fafafa; --card: #ffffff; --fg: #0a0a0a; --muted: #6f6f6f; --faint: #a3a3a3;
  --line: #ebebeb; --line-strong: #d9d9d9; --code: #f4f4f4;
  --invert-bg: #0a0a0a; --invert-fg: #ffffff; --invert-muted: #a3a3a3;
  --green: #16803c; --amber: #b45309; --red: #d92d20; --red-bg: #fef3f2;
  --shadow: 0 1px 2px rgba(0,0,0,.04), 0 1px 1px rgba(0,0,0,.02);
}
[data-theme="dark"] {
  --bg: #000000; --panel: #0a0a0a; --card: #0f0f0f; --fg: #ededed; --muted: #8f8f8f; --faint: #5c5c5c;
  --line: #1c1c1c; --line-strong: #2b2b2b; --code: #161616;
  --invert-bg: #ededed; --invert-fg: #0a0a0a; --invert-muted: #5c5c5c;
  --green: #3ecf7a; --amber: #f5a623; --red: #ff6b5e; --red-bg: #1c0a08;
  --shadow: none;
}
* { box-sizing: border-box; }
body { margin: 0; background: var(--bg); color: var(--fg); overflow-wrap: anywhere; -webkit-font-smoothing: antialiased;
  font: 15.5px/1.6 Inter, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; }
main { max-width: 1440px; margin: 0 auto; padding: 40px 20px 120px; }
main > p, main > ul, main > ol, main > h1 { max-width: 78ch; }
h1 { font-size: 34px; line-height: 1.15; font-weight: 650; letter-spacing: -.025em; margin: 0 0 16px; }
h2 { font-size: 22px; line-height: 1.25; font-weight: 620; letter-spacing: -.015em; margin: 72px 0 14px; }
h3 { font-size: 15.5px; font-weight: 620; margin: 32px 0 8px; }
code { font: 12.5px/1.4 ui-monospace, SFMono-Regular, Menlo, monospace; background: var(--code); padding: 2px 5px; border-radius: 5px; }
li::marker { color: var(--faint); }
.lead { font-size: 18px; line-height: 1.55; }
.muted { color: var(--muted); }

.toggle { font: inherit; font-size: 13px; color: var(--fg); background: var(--card); border: 1px solid var(--line-strong);
  border-radius: 8px; padding: 5px 11px; cursor: pointer; box-shadow: var(--shadow); }
nav { display: flex; flex-wrap: wrap; gap: 4px 18px; font-size: 13.5px; padding-top: 16px; border-top: 1px solid var(--line); }
nav a { color: var(--muted); text-decoration: none; }

.card { background: var(--card); border: 1px solid var(--line); border-radius: 14px; padding: 18px 20px; box-shadow: var(--shadow); }
.row2, .row3, .row4 { display: grid; grid-template-columns: minmax(0, 1fr); gap: 12px; }

.dia { background: var(--panel); border: 1px solid var(--line); border-radius: 14px; padding: 20px; }
.tier-label { font-size: 12.5px; color: var(--muted); margin: 0 0 8px; }
.box { background: var(--card); border: 1px solid var(--line); border-radius: 10px; padding: 12px 14px; box-shadow: var(--shadow); }
.box.flag { border-color: var(--red); background: var(--red-bg); }
.box.later { border-style: dashed; box-shadow: none; background: transparent; }
.box.core { background: var(--invert-bg); border-color: var(--invert-bg); color: var(--invert-fg); }
.link { height: 22px; display: flex; justify-content: center; }
.link i { width: 1px; background: var(--line-strong); }

.tbl { width: 100%; border-collapse: collapse; font-size: 14px; }
.tbl th { font-weight: 500; font-size: 12.5px; color: var(--muted); padding: 8px 4px; border-bottom: 1px solid var(--line-strong); }
.tbl td { padding: 11px 4px; border-bottom: 1px solid var(--line); }
.dot { display: inline-block; width: 9px; height: 9px; border-radius: 50%; }
.dot.y { background: var(--green); }
.dot.i { border: 2px solid var(--amber); }
.dot.n { width: 10px; height: 2px; border-radius: 1px; background: var(--line-strong); }

@media (min-width: 680px) {
  .row2, .row3, .row4 { grid-template-columns: repeat(2, minmax(0, 1fr)); }
}
@media (min-width: 900px) {
  main { padding-left: 40px; padding-right: 40px; }
}
@media (min-width: 1180px) {
  main { padding-left: 264px; padding-right: 56px; }
  nav { position: fixed; top: 132px; left: calc(max(0px, (100vw - 1440px) / 2) + 56px); width: 168px;
    flex-direction: column; gap: 2px; padding: 0; border: 0; }
  .row3 { grid-template-columns: repeat(3, minmax(0, 1fr)); gap: 16px; }
  .row4 { grid-template-columns: repeat(4, minmax(0, 1fr)); gap: 16px; }
  h1 { font-size: 40px; }
}
```

Theme toggle, kept in the URL:

```html
<button class="toggle" id="toggle" type="button">Dark</button>
<script>
  const root = document.documentElement, toggle = document.getElementById("toggle");
  function setTheme(theme, write) {
    root.dataset.theme = theme;
    toggle.textContent = theme === "dark" ? "Light" : "Dark";
    if (!write) return;
    try {
      const url = new URL(location.href);
      if (theme === "dark") url.searchParams.set("theme", "dark"); else url.searchParams.delete("theme");
      history.replaceState(null, "", url);
    } catch (e) {}
  }
  setTheme(new URLSearchParams(location.search).get("theme") === "dark" ? "dark" : "light", false);
  toggle.addEventListener("click", () => setTheme(root.dataset.theme === "dark" ? "light" : "dark", true));
</script>
```

## Check before publishing

Screenshot the file with headless Chrome and look at the image. Check a wide window (about 1680px) and a narrow one, in both themes, and scroll far enough to see every section type.

```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless=new --hide-scrollbars \
  --window-size=1680,3000 --screenshot=wide.png "file://$PWD/page.html"
```

Headless Chrome will not go narrower than 500px, so a 500px screenshot stands in for a phone. To see the dark theme, add `?theme=dark` to the file URL. Tell the user which widths and sections you looked at.
