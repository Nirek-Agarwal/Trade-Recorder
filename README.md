# Trade Recorder

A single-file web tool that turns a pasted broker order-history page into a colour-coded Excel ledger with daily and running totals. No backend, no sign-up, no data leaves the browser.

**Live demo:** https://nirek-agarwal.github.io/trade-recorder/
**Try it without real data:** paste the contents of [`sample-order-history.txt`](sample-order-history.txt) into Step 2.

## Why I built it

Keeping a day-by-day record of options premiums sold and bought usually means retyping every order into a spreadsheet each evening. It is slow and easy to get wrong, and broker reports rarely give the layout you want (SELL and BUY side by side, with running gross totals). Nobody asked me to fix this. I wrote a tool that does it in one paste.

## How it works

1. **Load previous file (optional):** upload the last `.xlsx` this tool produced, so today's trades are appended to the running ledger.
2. **Paste:** copy the Order History table from the broker's web page and paste it in. Only `EXECUTED` orders are kept.
3. **Review and edit:** every field is editable, and quantities and totals recalculate live.
4. **Export:** download an `.xlsx` with one block per trading day, SELL on the left (red) and BUY on the right (green), with day totals and cumulative gross totals.

## Design decisions

- **Anchor on the timestamp, read backwards.** Pasted page text contains stray UI fragments between records (for example lone "B" / "S" button labels). A parser that steps forward a fixed number of lines per record drifts as soon as one appears. Every record ends with a timestamp line, so I find those and read the fixed fields backwards from each one. Noise between records no longer matters.
- **No backend.** Trade data is personal financial data. Everything runs in the browser, so there is nothing to host, secure, or leak. The trade-off is no sync across devices; the exported `.xlsx` is the source of truth and can be re-uploaded.
- **Round-trip through the spreadsheet instead of a database.** The user already lives in Excel, so state is stored in the file they download and reload.
- **Dates handled defensively.** Spreadsheet apps silently convert date strings into serial numbers, so dates are written as text, and the loader normalises Date objects, Excel serials, and several string formats back to one format.
- **Two libraries on purpose.** SheetJS reads uploaded files; ExcelJS writes the styled output.

## Supported format

Built around the Order History layout of Angel One's web platform. The parser expects that page's text layout, so other brokers will not parse as-is.

## Known limitations

- Parsed trades are stamped with the date of use, not the date inside the pasted timestamp (editable in the review table).
- Libraries load from a CDN, so the first page load needs internet. Your data is never uploaded.
- Depends on the broker's page text layout; a layout change breaks parsing.

## Run locally

Download `index.html` and open it in a browser.
