# DQA User Guide Builder

A lightweight, browser-based tool that reads your Excel workbook locally and builds a fully tailored prompt you can paste into any AI assistant (Claude, Copilot, ChatGPT) to generate a professional Data Quality Assurance user guide.

Unlike a generic prompt, this tool extracts the actual structure and content of your workbook — sheet names, column headers, check rows with expected values, table definitions, named ranges, and Power Query connections — and injects all of it into the prompt. The AI then writes instructions that reference your exact sheet names, column names, and check logic rather than producing something generic.

---

## What it does

1. You drop your Excel file into the tool
2. It reads the workbook structure and cell content directly in your browser
3. It automatically masks any sensitive data it finds in connection strings or link paths before showing them to you
4. You review what was detected, expand each sheet to check the content, edit anything that needs changing, and add any missing context
5. It builds a detailed structured prompt containing your actual workbook data
6. You copy the prompt and paste it into Claude, Copilot, or ChatGPT
7. The AI writes a full step-by-step analyst guide with numbered steps and `[Screenshot]` placeholders

---

## Privacy and sensitive data

**Your file never leaves your computer.** The tool reads your Excel file entirely within the browser using JSZip. Nothing is uploaded, sent to a server, or stored anywhere.

However, the prompt the tool generates contains your workbook content — sheet names, column names, check descriptions, and connection details. You will paste this prompt into an external AI service. The tool handles this in two ways:

**Automatic masking.** When the file loads, the tool scans all detected connections, link paths, and named range references for sensitive patterns and replaces them with placeholders before showing them to you. The following are masked automatically:

- SharePoint URLs
- All other HTTP and HTTPS URLs
- UNC network paths (e.g. `\\server\share\...`)
- Local file paths (e.g. `C:\Users\...`)
- IP addresses
- SQL and ODBC connection string credentials (Server=, Database=, Password=, UID=, etc.)
- JDBC connection strings

The connection name itself is kept (e.g. "SAP Import" or "Power Query: Incidents") so the AI still understands what source is being used. Only the sensitive path or credential detail is removed.

**Warning banner.** If sensitive content is detected, a red warning banner appears explaining what was found. A toggle button lets you reveal the original unmasked values if you need to review or edit them.

**Review reminder.** Every time you build a prompt, a warning appears above the output reminding you to review it before copying. Automatic masking catches common patterns but cannot catch everything — if your workbook stores sensitive information in unusual formats, it may not be caught. Always read the prompt before pasting it into an external AI.

---

## What it reads from your file

| Data | Status |
|---|---|
| Sheet names | Read |
| Column headers | Read |
| Cell values (text and numbers) | Read, up to 60 rows per sheet |
| Named tables and their columns | Read |
| Named ranges and defined names | Read |
| Power Query connection names | Read, where stored in connections.xml |
| Full Power Query M query code | Not read |
| Cell colours and conditional formatting | Not read |
| VBA macros | Not read (binary format) |
| Pivot table definitions | Not read |
| Charts | Not read |
| Formula results | Only if the file was saved after the last calculation |
| Password-protected workbooks | Not readable |

---

## File size and format limits

**Recommended maximum: 50 MB**

The tool loads the entire file into browser memory before parsing. An xlsx file typically compresses at 4 to 10 times, so a 50 MB file can expand to 200-500 MB in memory. Most modern devices handle this without issue. Files above 100 MB may slow down or crash the browser tab on devices with less than 8 GB of RAM.

In practice, most DQA workbooks are between 1 MB and 15 MB. The tool handles these instantly.

**Supported formats:**

- `.xlsx` — fully supported
- `.xlsm` — fully supported (macros are ignored, everything else reads normally)
- `.xls` — not supported (old binary format)
- `.xlsb` — not supported (binary xlsx)
- `.csv` — not supported
- Google Sheets — export as `.xlsx` first

---

## Caveats

**Cell colours and conditional formatting are not read.**
If your workbook uses cell colours to indicate pass or fail (e.g. green = passed, red = failed), the tool cannot detect what those colours mean. Describe this in the "Anything else to include" field so the AI can reference it in the guide.

**VBA macros are not read.**
Macros are stored in a binary file inside the xlsx archive. The tool cannot read binary content. If analysts need to run or interact with macros as part of the update process, describe them in the "Anything else to include" field.

**Power Query connection names are detected but not the full query logic.**
The tool reads connection names from `xl/connections.xml`. It does not parse the full M query code or the transformation steps. You will see the connection name in the detected sources but not what it does. Add that context manually if it matters for the guide.

**Formula results only appear if the file was saved after calculation.**
Excel stores the last calculated result of each formula cell in the file. If the workbook was saved before formulas recalculated, those cells will appear blank or stale in the extracted content.

**Password-protected workbooks cannot be read.**
If your workbook is encrypted, JSZip will fail to parse it. Remove password protection before dropping the file in, then re-protect it afterwards if needed.

**The tool reads a maximum of 60 rows per sheet.**
This is intentional. The AI needs structure and examples, not all your data. For a DQA checks sheet this is almost always enough. For raw data sheets with thousands of rows, only the first 60 are included as a sample.

**Automatic masking catches common patterns but not everything.**
The masking covers URLs, file paths, IP addresses, and SQL credential key-value pairs. It will not catch sensitive data stored in unusual formats, inside cell values, or in sheet content (as opposed to connection strings). Always review the full prompt before copying it to an external AI.

**Copilot in the Excel sidebar has a short context window.**
The prompt this tool generates can be long for workbooks with many sheets and checks. Copilot in the Excel sidebar has an input limit of roughly 4-8k tokens and will silently truncate a long prompt. Use Copilot at copilot.microsoft.com instead, which has a larger context window.

**No data is saved between sessions.**
The tool has no memory. Closing the tab clears everything. Copy the prompt before closing.

**Requires an internet connection on first load.**
JSZip loads from a CDN when the page first opens. After that the tool can work offline. For restricted network environments, download JSZip separately and update the script tag to point to your local copy.

---

## Where to paste the prompt

| AI | Notes |
|---|---|
| Claude (claude.ai) | Best results. 200k token context, handles large prompts easily. |
| ChatGPT-4o | Works well. 128k token context. |
| Copilot (copilot.microsoft.com) | Works well. Use this rather than the Excel sidebar. |
| Copilot in Excel sidebar | Works for short prompts only. Will silently truncate long ones. |
| Gemini Advanced | Works. May need prompting to follow the section structure closely. |

---

## How to use it

**Option A — Run locally**

1. Download `dqa-guide-builder.html`
2. Double-click it to open in any browser
3. Drop your Excel file in, review what was detected, fill in any gaps
4. Hit Build my prompt, review the output, then copy and paste into your AI of choice

**Option B — Hosted on GitHub Pages**

1. Upload `dqa-guide-builder.html` to a GitHub repo and rename it `index.html`
2. Go to Settings, Pages, Source, Deploy from branch, main, root, Save
3. Share the link: `https://yourusername.github.io/your-repo-name`

---

## Tech stack

- Plain HTML, CSS, and JavaScript — no framework, no build step, one file
- [JSZip](https://stuk.github.io/jszip/) for reading xlsx files locally in the browser
- No backend, no database, no cookies, no tracking

---

## Customising the tool

Open the HTML file in any text editor to:

- Change the sections in the generated prompt
- Add your organisation's logo or branding
- Pre-fill fields for a specific workbook your team always uses
- Change the row limit per sheet (search for `maxRows=60`)
- Add additional masking patterns for your organisation's specific URL formats or naming conventions
- Translate the UI into another language

---

## Contributing

Pull requests welcome. The value of this tool is that it is a single self-contained file with no dependencies to install. Please keep it that way.

---

## Licence

MIT — free to use, modify, and share.
