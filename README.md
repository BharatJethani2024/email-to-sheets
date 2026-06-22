# Email-to-Spreadsheet Sales Automation (Make.com)

A no-code automation that reads sales data out of incoming emails and logs it into a spreadsheet automatically, with zero manual data entry. Built in [Make.com](https://www.make.com) for a small-business client who receives daily sales-and-customer reports by email and wanted them captured into a spreadsheet without typing anything.

---

## The Problem

The client runs multiple shops. Each location emails in a short report containing the shop name, total sales, customer count, and the date. Previously, someone had to open every email and copy those numbers into a spreadsheet by hand, which is slow, easy to get wrong, and has to be repeated every single day.

## The Solution

A three-step automation that runs on its own:

```
[1] Gmail            ->   [2] Text Parser      ->   [3] Google Sheets
    Watch Emails          Extract the fields        Add a Row
```

1. **Gmail – Watch Emails** detects each new report email as it arrives.
2. **Text Parser – Match Pattern** reads the email body and pulls out the four fields (shop, sales, customers, date) using a single regular expression.
3. **Google Sheets – Add a Row** writes those values as a new row in the tracking sheet.

The scenario is scheduled to check for new mail automatically, so the spreadsheet stays up to date with no human involvement. The client opens the sheet on any device, and can download it as a Microsoft Excel file whenever an `.xlsx` copy is needed.

## Key Feature: Multiple Reports per Email

A single email can contain several shop blocks. With **Global match** enabled on the parser, the automation extracts every block and writes one row per shop in a single run, so a two-shop email produces two rows with no extra setup.

---

## How It Works (Detail)

### Step 1 — Gmail: Watch Emails
- Watches the inbox for new messages.
- A simple subject filter (e.g. `SALES REPORT`) can be used so the scenario only reacts to the right emails and ignores everything else.
- Returns the email body as plain text (the "Full text body" field) for the next step to read.

### Step 2 — Text Parser: Match Pattern
The heart of the automation. One regular expression extracts all four fields into named buckets:

```
Shop:\s*(?<shop>.+)\s*Sales:\s*(?<sales>\d+)\s*Customers:\s*(?<customers>\d+)\s*Date:\s*(?<date>.+)
```

What each part does:
- `Shop:\s*` — finds the label `Shop:` and skips any spaces or line breaks after it
- `(?<shop>.+)` — captures the shop name into a bucket named `shop`
- `Sales:\s*(?<sales>\d+)` — captures the sales figure (digits only) into `sales`
- `Customers:\s*(?<customers>\d+)` — captures the customer count into `customers`
- `Date:\s*(?<date>.+)` — captures the date into `date`

Parser settings used:
- **Global match: Yes** — find every shop block in the email, not just the first
- **Multiline: Yes** — read across the multiple lines of the email body
- **Case sensitive: No**

### Step 3 — Google Sheets: Add a Row
- Points at the tracking spreadsheet (`Shop Sales Log`) with a header row of `Shop | Sales | Customers | Date`.
- "Table contains headers" is enabled so each parsed bucket maps cleanly to its column.
- Runs once per parsed block, creating one row per shop.

---

## Expected Email Format

```
Shop: Downtown
Sales: 4500
Customers: 120
Date: 2026-06-22
```

Multiple blocks in one email are supported:

```
Shop: Midtown
Sales: 62000
Customers: 122
Date: 2026-06-30

Shop: Uptown
Sales: 61000
Customers: 145
Date: 2026-07-01
```

## Resulting Spreadsheet

| Shop     | Sales | Customers | Date       |
|----------|-------|-----------|------------|
| Midtown  | 62000 | 122       | 2026-06-30 |
| Uptown   | 61000 | 145       | 2026-07-01 |

---

## Tools Used

- **Make.com** — automation platform (no-code)
- **Gmail** — email source
- **Google Sheets** — destination spreadsheet (exportable to Excel)
- **Regular expressions** — field extraction

## Screenshots

See the [`screenshots`](./screenshots) folder for the scenario canvas, the parser configuration, and the populated spreadsheet.

---

## Notes on Reliability and Cost

- The scenario uses three operations per email block, keeping it well within Make's free-tier limits for typical volumes.
- A subject-line filter prevents the scenario from waking for unrelated mail.
- The Gmail module advances only forward through the inbox, so each email is processed exactly once and never duplicated.

## Possible Extensions

- Send a confirmation or daily summary email after logging.
- Add a daily step that exports an `.xlsx` copy to the client's Drive automatically.
- Add basic validation to flag malformed reports for review.

---

*Built as a delivered, working automation — not just instructions — for an Upwork client. Documentation written to be clear to a non-technical reader.*
