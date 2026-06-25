# DQA User Guide Builder

A lightweight, browser-based tool that reads your Excel workbook locally and builds a structured prompt you can paste into any AI assistant (Claude, Copilot, ChatGPT) to generate a professional Data Quality Assurance user guide.

---

## What it does

1. You drop your Excel file into the tool
2. It reads the sheet names directly in your browser
3. You fill in your data sources and DQA checks
4. It builds a detailed, structured prompt tailored to your workbook
5. You copy the prompt and paste it into your AI assistant of choice

The AI then writes a full step-by-step analyst guide — with numbered steps and `[Screenshot]` placeholders ready for you to fill in.

---

## How to use it

**Option A — Run locally (recommended)**

1. Download `dqa-guide-builder.html`
2. Double-click it — it opens in any browser
3. Drop your Excel file in, fill in the fields, hit **Build my prompt**
4. Copy the prompt and paste it into Claude, Copilot, or ChatGPT

**Option B — Use the hosted version**

Visit: `https://yourusername.github.io/dqa-guide-builder`

No install, no login, works in any modern browser.

---

## Privacy and security

- **Your file never leaves your computer.** The Excel file is read entirely in the browser using the [SheetJS](https://sheetjs.com/) library. Nothing is uploaded, sent to a server, or stored anywhere.
- The tool has no backend. It is a single HTML file.
- The only outbound request is loading the SheetJS library from a CDN when the page first opens. After that, everything runs offline.
- The prompt it generates contains only what you typed in — sheet names, source descriptions, and check descriptions. No file content is ever included.

---

## What the generated guide covers

The prompt instructs the AI to write a guide structured in four sections:

1. **Updating data links** — refreshing external links, spotting stale or failed ones
2. **Refreshing external data sources** — triggering refreshes, confirming correct data loaded, handling errors
3. **Data quality checks** — what each check tests, what a pass looks like, how to sign it off, what to do on failure
4. **Final review before sign-off** — confirming all checks are complete and the workbook is ready

---

## Caveats

**The tool reads sheet names only — not your data.**
SheetJS reads the workbook structure (sheet names, and optionally named ranges or connections if present). It does not read cell values, formulas, or any actual data. The prompt it builds reflects what you manually describe in the tool, not the contents of your file.

**The AI still needs context from you.**
Sheet names alone are not enough for a great guide. The more you fill in — data sources, check descriptions, special instructions — the more accurate and useful the output will be.

**AI output quality varies by assistant.**
Claude tends to produce the most structured and analyst-appropriate output for this type of prompt. Copilot inside Excel can be inconsistent. ChatGPT works well but may need a follow-up nudge to stay in the right format. Always review the output before sending it to users.

**Power Query connections and named ranges are not auto-detected.**
The tool detects sheet names automatically. It does not inspect Power Query connections, named ranges, defined tables, or external link paths. You need to describe these manually in the "External data sources" field.

**The tool requires an internet connection to load.**
The SheetJS library loads from a CDN (`cdn.jsdelivr.net`) when you first open the page. If you are in a restricted network environment, download the SheetJS library separately and update the `<script>` tag to point to your local copy.

**The prompt is a starting point, not a finished guide.**
The AI will produce a solid first draft based on your inputs. You will likely need to review it, adjust step numbers, correct any assumptions the AI made, and add the screenshots before sharing it with analysts.

**No data is saved between sessions.**
The tool has no memory. If you close the tab, your inputs are gone. Copy the generated prompt before closing.

---

## Tech stack

- Plain HTML, CSS, and JavaScript — no framework, no build step
- [SheetJS (xlsx)](https://sheetjs.com/) for local Excel parsing
- No backend, no database, no cookies, no tracking

---

## Customising the tool

The entire tool is one HTML file. Open it in a text editor to:

- Change the sections in the generated prompt
- Add your organisation's logo or branding
- Pre-fill fields for your specific workbook
- Translate the UI into another language

---

## Contributing

Pull requests welcome. Keep it simple — the value of this tool is that it is a single file with no dependencies to install.

---

## Licence

MIT — free to use, modify, and share.
