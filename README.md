# WordToHTML Cleaner

Paste content copied from **Google Docs** and get back clean, **WordToHTML-style HTML** — ready to drop straight into a CMS.

Runs entirely in the browser as a single HTML file. No build step, no backend, no server-side dependencies.

<img width="1915" height="893" alt="image" src="https://github.com/user-attachments/assets/9e574029-16e2-4184-a27c-217e4501cd76" />


## Features

- **Paste-to-clean** — paste directly from Google Docs into the left pane and get cleaned HTML on the right, automatically.
- **Two-stage cleaning pipeline**
  1. A JavaScript pass strips Google Docs' bloated inline styles down to the essentials (bold, italic, underline, links, font color/size where meaningful).
  2. A Python pass (running in-browser via [Pyodide](https://pyodide.org/) + BeautifulSoup) normalizes heading levels, merges paragraphs, fixes spacing/`&nbsp;` artifacts, adds `target="_blank" rel="noopener"` to links, and appends trailing line breaks — matching the output of the original WordToHTML tool.
- **Spacing modes** — toggle between `None`, `Double`, and `Single` spacing between headings/lists/blocks in the final output.
- **Auto-copy** — automatically copies the cleaned HTML to your clipboard after every paste (toggleable).
- **Manual controls** — "Load sample," "Clear," "Copy Clean HTML," and "Run Cleaner" (for re-running the Python pass on demand).
- **Light / dark mode** — toggle in the header, remembers your preference (`localStorage`) and respects your OS preference on first load.
- **Collapsible intermediate view** — inspect the JS-cleaned HTML before the Python pass runs on it.

## Usage

1. Download `wordtohtml-cleaner.html`.
2. Open it in any modern browser (Chrome, Edge, Firefox, Safari). An internet connection is required the first time, so Pyodide and BeautifulSoup can load from their CDNs.
3. Copy content from a Google Doc (`Ctrl+C` / `Cmd+C`).
4. Click into the left pane and paste (`Ctrl+V` / `Cmd+V`).
5. The cleaned HTML appears on the right and is copied to your clipboard automatically (if **Auto-copy** is on).

No installation or local server is required — it's a single static HTML file.

### Running locally with a dev server (optional)

Opening the file directly (`file://`) works in most browsers, but some browsers restrict clipboard/module behavior on `file://` URLs. If you run into issues, serve it locally instead:

```bash
# from the project folder
python3 -m http.server 8000
# then open http://localhost:8000/wordtohtml-cleaner.html
```

## How it works

```
Google Docs paste
      │
      ▼
JavaScript cleaner (cleanGoogleHTML)
  • strips Google's inline style noise
  • keeps bold / italic / underline / links / meaningful colors & sizes
      │
      ▼
Python cleaner (Pyodide + BeautifulSoup, runs in-browser)
  • normalizes h1–h6 down to h3/h4
  • merges consecutive <p> tags into spaced <div> blocks
  • removes trailing &nbsp; before closing tags / <br>
  • adds target="_blank" rel="noopener" to links
  • appends trailing <br> tags at the end of content
      │
      ▼
Spacing pass (applySpacingDouble / applySpacingSingle)
  • optionally inserts extra <br> between heading/list/div blocks
      │
      ▼
Final cleaned HTML (displayed + copied to clipboard)
```

## Tech stack

- Vanilla HTML/CSS/JavaScript — no framework, no build step.
- [Pyodide](https://pyodide.org/) — runs Python (and `pip`-installed `beautifulsoup4`) directly in the browser via WebAssembly.
- CSS custom properties for theming (light/dark mode).

## File structure

```
wordtohtml-cleaner.html   # everything: markup, styles, JS cleaner, Python cleaner, Pyodide bridge
```

Everything lives in one file by design, so it's trivial to host (GitHub Pages, a CDN, an internal wiki, etc.) or hand to someone as a standalone tool.

## Known limitations

- Requires an internet connection on first load to fetch Pyodide and `beautifulsoup4` from jsDelivr/PyPI (subsequent loads may be served from browser cache).
- The Python cleaner takes a moment to initialize the first time it runs in a session (Pyodide + package install); a JS-only fallback is shown if it fails or hasn't finished loading.
- Designed around the specific heading/paragraph/spacing conventions of one CMS's "WordToHTML" format — you may need to adjust the Python rules in the embedded `pycode` script for a different target format.

## Contributing

Since this is a single self-contained file, the easiest way to contribute is:

1. Fork the repo.
2. Edit `wordtohtml-cleaner.html` directly.
3. Open the file in a browser and test with real Google Docs content (rich text, links, headings, lists).
4. Submit a PR describing the change and any before/after HTML output examples.

## License

MIT — do whatever you'd like with it.
