# DQA Documentation Assistant

A browser-based tool that reads your Excel workbook locally and generates ready-to-use documentation for analysts — user guides, SOPs, quick references, and training guides — tailored to your actual workbook structure.

Drop your file in. The tool reads your sheets, tables, checks, connections, and named ranges without uploading anything. It then builds a structured prompt you paste into Claude, Copilot, or ChatGPT, which writes documentation that references your exact sheet names, column names, and check logic.

---

## What makes it different

Every other AI Excel tool focuses on formulas, data analysis, or chat with your spreadsheet. This tool does one specific thing: it understands the structure of a DQA workbook and produces process documentation for the analysts who run it.

It does not generate generic instructions. It reads your workbook, identifies which sheets are checks, which are raw data, which are summaries, detects your validation logic, and writes steps that say "navigate to the DQA Checks sheet, find the Row Count Match row in column A, verify column D shows the expected value" — because it actually knows those things are in your file.

---

## How it works

1. Drop your Excel file in — it is read entirely in your browser, never uploaded
2. Watch the stepped progress as it reads sheets, tables, connections, named ranges, and detects validation checks
3. Review the workbook health panel — green, amber, and red indicators show what was and was not detected
4. Check the sheet previews — each sheet shows its detected role (Raw Data, Checks, Summary, Lookup, Archive), column headers, and sample rows
5. Review any auto-detected connections and edit or remove sensitive values before generating
6. Choose your document type — User Guide, SOP, Quick Reference, or Training Guide
7. Hit Generate, copy the output, and paste it into Claude, Copilot, or ChatGPT

The AI receives a fully structured prompt containing your workbook metadata, sheet roles, column names, check rows, data sources, and named ranges. It writes documentation specific to your workbook, not a generic template.

---

## What it detects automatically

| Feature | How |
|---|---|
| Sheet names and content | Reads cell values up to 60 rows per sheet |
| Sheet roles | Infers Raw Data, Checks, Summary, Lookup, Archive, Report from name and column patterns |
| Validation checks | Detects PASS/FAIL, TRUE/FALSE, OK/ERROR, Expected/Actual column patterns |
| Named tables and columns | Reads xl/tables/ from the workbook ZIP |
| Power Query connections | Reads xl/connections.xml and customXml |
| Named ranges and defined names | Reads workbook.xml defined names |
| Sensitive data in connections | Masks URLs, network paths, file paths, IPs, and SQL credentials automatically |

---

## Document types

The same workbook extraction produces four different outputs depending on what you need:

**User Guide** — step-by-step instructions for analysts who regularly run the process. Four sections: updating data links, refreshing sources, completing checks, final review and sign-off.

**SOP** — formal standard operating procedure with purpose, scope, roles, frequency, pre-requisites, and numbered procedure steps. Suitable for governance or audit purposes.

**Quick Reference** — a one-page checkbox checklist for experienced analysts. Each item references the specific sheet and column to check.

**Training Guide** — onboarding document for someone new to the workbook. Explains what each sheet does, why each step matters, and includes a common issues section.

---

## Privacy and sensitive data

**Your file never leaves your computer.** JSZip reads the Excel file entirely within the browser. Nothing is uploaded.

When connections or link paths are detected, the tool automatically scans for and masks:

- SharePoint and other HTTPS URLs
- UNC network paths (`\\server\share\...`)
- Local Windows file paths (`C:\Users\...`)
- IP addresses
- SQL and ODBC connection string credentials (Server=, Database=, Password=, UID=)
- JDBC connection strings

The connection name is kept so the AI understands what source is being used. Only the sensitive path or credential detail is removed.

A red warning banner appears if sensitive content is found. A toggle lets you reveal original values if you need to review or edit them. A review reminder appears above the output every time you generate, as a final check before copying.

Automatic masking catches common patterns but cannot catch everything. Always read the full output before pasting it into an external AI service.

---

## File size and format limits

**Recommended maximum: 50 MB**

The file is loaded into browser memory before parsing. An xlsx file compresses at roughly 4 to 10 times, so a 50 MB file can expand to 200 to 500 MB in memory. Most DQA workbooks are between 1 MB and 15 MB and load instantly.

**Supported:**
- `.xlsx` — fully supported
- `.xlsm` — fully supported (macros ignored, everything else reads normally)

**Not supported:**
- `.xls` — old binary format
- `.xlsb` — binary xlsx
- `.csv` — no workbook structure to read
- Google Sheets — export as `.xlsx` first

---

## Caveats

**Cell colours and conditional formatting are not read.**
If green means passed and red means failed, describe this in the Additional Context field. The tool cannot read what colours mean.

**VBA macros are not read.**
Macros are stored as binary inside the xlsx. If analysts need to interact with macros as part of the process, describe this manually.

**Power Query connection names are detected but not the full query logic.**
You will see the connection name in the detected sources but not what transformations it applies. Add that detail manually if it matters.

**Formula results only appear if the file was saved after calculation.**
Cells with unsaved formula results will appear blank or stale in the extracted content.

**Password-protected workbooks cannot be read.**
Remove protection before dropping the file in.

**Maximum 60 rows read per sheet.**
Intentional — the AI needs structure and examples, not all your data.

**Copilot in the Excel sidebar has a short input limit.**
It will silently truncate long prompts. Use Copilot at copilot.microsoft.com instead.

**No session memory.**
Closing the tab clears everything. Copy the output before closing.

---

## Where to paste the output

| AI | Notes |
|---|---|
| Claude (claude.ai) | Best results. 200k token context. Handles all document types well. |
| ChatGPT-4o | Works well. 128k token context. |
| Copilot (copilot.microsoft.com) | Works well. Use this rather than the Excel sidebar. |
| Copilot in Excel sidebar | Short input limit. Will truncate long prompts silently. |
| Gemini Advanced | Works. May need a follow-up to stay on structure. |

---

## Running the tool

**Locally — no internet needed after first load**
1. Download `dqa-guide-builder.html`
2. Double-click to open in any browser
3. Drop your file in, review, generate, copy

**Hosted on GitHub Pages**
1. Upload `dqa-guide-builder.html` to a GitHub repo and rename it `index.html`
2. Go to Settings, Pages, Deploy from branch, main, root, Save
3. Share the link: `https://yourusername.github.io/your-repo-name`

---

## Tech stack

- Plain HTML, CSS, and JavaScript — one file, no framework, no build step
- [JSZip](https://stuk.github.io/jszip/) for local Excel parsing in the browser
- No backend, no database, no cookies, no tracking

---

## Customising

Open the file in any text editor to:

- Change the document type prompts
- Add your organisation's branding
- Pre-fill fields for a workbook your team always uses
- Adjust the row limit per sheet (search `maxRows=60`)
- Add masking patterns for your organisation's specific URL formats
- Add new sheet role detection rules (search `inferRole`)

---

## Contributing

Pull requests welcome. The value of this tool is that it is a single self-contained file. Please keep it that way.

---

## Licence

MIT — free to use, modify, and share.

## Licence

MIT — free to use, modify, and share.
