# my-apps — notes for Claude

James's personal web apps, hosted free on GitHub Pages at https://usajlh.github.io/my-apps/
Each app is one self-contained HTML file. Push to `main` and Pages redeploys in ~1–2 min;
check the live page loads before telling James it's done. Repo is public, so never commit
personal data. All file processing happens in the browser, on the device.
James uses Excel on a Windows PC and an iPhone; he's a programming novice, so keep
explanations plain.

## Shared Tiller details
James's Tiller Transactions sheet (Excel) has these columns, in this order:
Date, Description, Category, Amount, Account, Account #, Institution, Month, Week,
Transaction ID, Account ID, Check Number, Full Description, Date Added, MetaData,
Categorized Date, Reconciled/XFER Date
- Amount: expenses negative, income/credits positive.
- MetaData is a leftover from Mint; the Amazon app reuses it for product info.

## Apple Card → Tiller (`apple-card-to-tiller.html`)
Converts Apple Card CSV exports (from Apple Wallet) into Tiller rows.
- Flips Apple's signs (purchases come in positive).
- Date = clearing date by default; Month = 1st of month; Week = that week's Sunday.
- Description = Merchant; Full Description = Apple's raw description.
- Transaction ID = stable hash ("applecard-…") so re-imports produce the same ID.
- Category rules (`CATEGORY_RULES`): description starting "ACH Deposit" → "Credit Card Payment".
  Add new rules as one line each.
- Output: copy as tab-separated rows, or download CSV. Settings saved in localStorage.

## Amazon → Tiller Splitter (`amazon-matcher.html`)
Splits each Amazon charge from Tiller into one row per product, for categorizing per item.
- Data source: Amazon Order History Reporter Chrome extension (orders + items CSVs,
  one set per Amazon account; James and his wife have separate accounts).
  Backup: Amazon "Request Your Data" → Your Orders .zip (also has per-item prices).
- Input: James pastes full Tiller rows (with header) for his Amazon transactions.
- Matching: charge amount ± a few cents and date window against order payments/totals;
  handles orders charged in several parts, refunds, and same-amount ties ("Check").
- Output rows copy every column from the original; only Amount and MetaData change.
  MetaData = "qty× product name (Order ###)". Tax/shipping spread by price so the lines
  sum exactly to the original charge. Extra lines get Transaction ID suffix "-2", "-3"…
- James deletes the originals marked "Split" and pastes the new rows in.
- JSZip is bundled in `lib/` (no CDN).
