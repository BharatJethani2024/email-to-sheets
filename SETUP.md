# Setup Guide — Build This Automation Yourself

This guide walks through rebuilding the automation from scratch in Make.com. No prior automation experience needed.

## Before You Start

Prepare the two ends first — the input (email) and the output (sheet) — before building anything.

### 1. Create the spreadsheet
1. Google Drive → New → Google Sheets
2. Name it `Shop Sales Log`
3. In row 1, add four headers, one per column: `Shop`, `Sales`, `Customers`, `Date`
4. Leave everything else empty

### 2. Send a test email
Send an email to yourself with subject `SALES REPORT` and this body:

```
Shop: Downtown
Sales: 4500
Customers: 120
Date: 2026-06-22
```

Leave it unread in the inbox.

---

## Build the Scenario

### Module 1 — Gmail: Watch Emails
1. Create a new scenario, click the `+` to add the first module
2. Choose **Gmail → Watch Emails**, connect your Google account
3. Settings:
   - **Filter type:** Simple Filter
   - **Folder:** INBOX
   - **Criteria:** All emails
   - **Mark as read when fetched:** No (so you can re-test on the same email)
   - **Limit:** 1
4. When asked "Choose where to start," pick **Choose manually** and select your test email

### Module 2 — Text Parser: Match Pattern
1. Add the next module: **Text parser → Match pattern**
2. **Pattern:**
   ```
   Shop:\s*(?<shop>.+)\s*Sales:\s*(?<sales>\d+)\s*Customers:\s*(?<customers>\d+)\s*Date:\s*(?<date>.+)
   ```
3. **Global match:** Yes (to capture multiple shop blocks per email)
4. **Multiline:** Yes
5. **Case sensitive:** No
6. **Text:** map the Gmail **Full text body** field here

### Module 3 — Google Sheets: Add a Row
1. Add the next module: **Google Sheets → Add a Row**
2. Connect the same Google account that owns the sheet
3. Settings:
   - **Search Method:** Select using Search
   - **Spreadsheet:** `Shop Sales Log`
   - **Sheet:** Sheet1
   - **Table contains headers:** Yes
4. Map each column to its parser bucket:
   - Shop → `shop`
   - Sales → `sales`
   - Customers → `customers`
   - Date → `date`

---

## Test It

1. Send yourself a fresh `SALES REPORT` email (the Gmail watch only moves forward, so each test needs a new email)
2. Click **Run once**
3. Check the bubbles: Text parser shows the number of shop blocks found, Google Sheets shows the same number of rows written
4. Open the sheet and confirm the new rows

## Go Live

1. Turn on the schedule (clock toggle, bottom left) — every 15 minutes is fine
2. Flip the scenario to **ON**

The automation now runs on its own.

---

## Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| "No new data" on Gmail | The watch bookmark already passed your test email | Send a fresh email, or reset via "Choose where to start" |
| Parser output empty | Pattern didn't match the body text | Check the actual "Full text body" value and confirm labels/spelling match the pattern |
| Only first shop captured | Global match is off | Set Global match to Yes |
| Values in wrong columns | Mapping mismatch in Module 3 | Re-map each column to the correct bucket |
| Used HTML body by mistake | Picked "HTML body" instead of plain text | Map "Full text body" instead |
