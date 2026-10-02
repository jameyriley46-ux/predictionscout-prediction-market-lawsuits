# Prediction Market Lawsuit Tracker dataset

Federal lawsuits, appeals and bills about prediction markets in the United States: cases involving Kalshi, Polymarket, Crypto.com and Robinhood, and state and congressional bills that address prediction markets or event contracts.

PredictionScout compiles this dataset from public court records and public bill records. It is the data behind the live tracker at **https://predictionscout.com/research/prediction-market-lawsuit-tracker/**, which has the charts, a state-by-state table and plain-English notes on each case. The live page is updated weekly, and so is this repository.

<!-- snapshot:start -->
**Data as of Oct 2, 2026, 2:24 AM ET.**

| File | Rows | What it holds |
|---|---|---|
| `data/federal-lawsuits.csv` | 95 | 84 federal district-court lawsuits, plus 11 duplicate dockets flagged in `duplicate_of` (61 of the 84 still open) |
| `data/appeals.csv` | 23 | 22 federal appeals, plus 1 duplicate docket(s) |
| `data/private-suits-listed-separately.csv` | 20 | private suits listed separately and left out of the totals |
| `data/bills.csv` | 79 | 48 state bills and 31 bills in Congress (48 written specifically about prediction markets) |
<!-- snapshot:end -->

## Files

All files are UTF-8 CSV with a header row. Dates are `YYYY-MM-DD`. Blank cells mean "none" or "not known", never zero.

- `data/federal-lawsuits.csv`: federal district-court cases that count toward the tracker's totals.
- `data/appeals.csv`: appeals of those cases in the federal courts of appeals.
- `data/private-suits-listed-separately.csv`: federal cases that involve a platform but are one customer's own dispute or a marketing complaint. They are kept out of the totals (see Methodology).
- `data/bills.csv`: state bills and bills in Congress that address prediction markets.

The three case files share the same columns, so you can stack them into one table if you want everything.

## Data dictionary

### Case files (`federal-lawsuits.csv`, `appeals.csv`, `private-suits-listed-separately.csv`)

| Column | Meaning |
|---|---|
| `courtlistener_docket_id` | CourtListener's numeric ID for the docket. Stable, and the easiest key for joining or de-duplicating. |
| `caption` | Case name as shown on CourtListener. Individual people who are plaintiffs or relators are replaced with `[Individual plaintiff]` or `[individual relator]`. Companies, governments, officials and tribes are named. |
| `platform` | The prediction-market platform involved: `Kalshi`, `Polymarket`, `Crypto.com`, `Robinhood`, or `Kalshi/Robinhood` when both are defendants. Polymarket includes its US entity QCX LLC, Blockratize Inc. and Adventure One QSS Inc. (the Polymarket.com operator). Crypto.com includes North American Derivatives Exchange (NADEX) and Foris DAX. |
| `case_type` | Who is suing whom. One of: `Platform v. state` (an exchange or broker sues state officials, usually arguing that federal commodities law preempts state gambling law); `State or city v. platform` (a state, attorney general or city sues the platform); `Tribe v. platform` (a tribal nation sues, usually under the Indian Gaming Regulatory Act); `Platform v. CFTC`; `Private / class action` (customers or putative classes suing a platform over its contracts); `Loss-recovery suit` ("Gambling Recovery" LLCs suing under state gambling loss-recovery statutes); `Trademark / IP`. In the separate file: `Individual user suit` (one customer suing over their own account or trades) and `Consumer marketing suit` (claims about advertising or marketing messages). |
| `state` | Two-letter postal code of the state the case is about: the state whose officials, laws or residents are involved, which is usually but not always the state where the court sits. `US` for cases about federal agencies. |
| `court` | Court abbreviation, for example `S.D.N.Y.` or `9th Cir.` |
| `level` | `District` or `Appeals`. |
| `docket_number` | The court's docket number. |
| `date_filed` | Filing date on the docket. For cases removed from state court, this is the date the case reached federal court. |
| `date_terminated` | Date CourtListener shows the docket as terminated. Blank means open. A closed docket can mean dismissal, settlement, remand to state court, transfer or a final judgment; it says nothing about who won. |
| `duplicate_of` | If this docket is a second record of a case already listed (a transfer, a refiled or opened-in-error docket, a second record of the same appeal), the `courtlistener_docket_id` of the main record. Blank otherwise. Drop rows with a value here to count cases. |
| `verified` | `Yes` if the parties, court, docket number and date were checked by hand against the docket. `Uncertain` if some detail could not be confirmed yet; the `note` says which. |
| `counted_in_headline` | `Yes` if the case counts toward the totals on the live tracker; `No` for the separately listed private suits. |
| `note` | Short plain-English context: who the government party is, links between dockets, what could not be confirmed. Often blank. |
| `courtlistener_url` | Link to the docket on CourtListener. For redacted captions, the descriptive part of the URL is rebuilt from the redacted caption; the docket ID in the URL is what CourtListener uses, so the link works the same. |

### `bills.csv`

| Column | Meaning |
|---|---|
| `state` | Two-letter postal code, or `US` for bills in Congress. |
| `bill` | Bill number as LegiScan formats it, for example `AB1840`, `HB05229`, `SR708`. |
| `session` | Legislative session years, for example `2025-2026`. |
| `title` | Official bill title (some long titles are cut off by the source). |
| `classification` | `Prediction-market bill` if the bill is written specifically about prediction markets; `Broader bill with a prediction-market provision` if it is a larger bill (budget, gaming, ethics, defense) that contains a prediction-market section. |
| `legiscan_status` | Bill status from LegiScan: `Introduced`, `Passed one chamber`, `Passed legislature`, `Enacted / adopted`, `Vetoed` or `Failed`. Simple resolutions that were agreed to in one chamber can show as `Introduced` or `Enacted / adopted` depending on how LegiScan records them. |
| `status_date` | Date of the current status. |
| `last_action_date` | Date of the latest recorded action. |
| `last_action` | Text of the latest recorded action. |
| `verified` | `Yes` if we read the bill and confirmed its prediction-market provision; `Uncertain` if we could not fully confirm it (see `note`). |
| `note` | What the prediction-market provision does, companion bills, or what is unconfirmed. |
| `legiscan_url` | Bill page on LegiScan. |
| `state_url` | Bill page on the legislature's own website. |

## Methodology

**Finding cases.** We search the CourtListener RECAP archive of federal court dockets (docket information from PACER) for cases whose names include Kalshi or KalshiEX; Polymarket, Blockratize, QCX or Adventure One QSS; North American Derivatives Exchange, Foris DAX or Crypto.com; or Robinhood Derivatives, plus Robinhood cases that mention prediction markets.

**Checking each case.** A person reviews every docket the search finds before it is added. We check the parties, court, docket number and filing date and classify the case by who is suing whom.

What we include:
- Federal district-court cases and appeals where a prediction-market platform is a party and the case is about its prediction-market or event-contract business.
- Cases brought in state court that were removed to federal court, because they now have a federal docket.

What we exclude:
- Cases that only match a company name and are not about prediction markets, for example patent suits against Crypto.com, a website cookie-tracking class action, a crypto exchange account dispute, or a civil forfeiture involving a Crypto.com account.
- Dockets with no named parties and no entries, which we cannot identify (for example records opened in error). These are rechecked if parties appear later.

**Duplicate dockets.** One case can have more than one docket, for example after a transfer to another court, when a case is refiled, or when CourtListener holds two records of the same appeal. We keep every docket but mark the extra ones in `duplicate_of`, and they are not counted twice.

**Why some private suits are listed separately.** Most private cases in `federal-lawsuits.csv` argue that a platform's contracts are illegal gambling or that the platform broke state law by offering them. Those matter for the legal fight over prediction markets, so they count. The suits in `private-suits-listed-separately.csv` are different: they are individual customers fighting over their own accounts or trades, or a consumer complaint about marketing text messages. When this repository was created (October 2, 2026), 18 of the 20 were near-identical suits by individual Polymarket.com users against its operator, Adventure One QSS, that reached the Southern District of New York between September 29 and October 1, 2026. In the ones whose details are public, the user first sued in New York state court and Polymarket moved the case to federal court. Counting them would make the totals swing on one batch of customer disputes, so we list them in full but leave them out of the totals. Many show only a caption so far, so they are marked `Uncertain` until filings appear.

**Status.** `date_terminated` is the docket's termination date on CourtListener. It is not a ruling on the merits.

**Bills.** We search LegiScan for "prediction market", "prediction markets", "event contract", "event contracts", Kalshi and Polymarket in the current sessions of all 50 states and Congress. We read the title of every hit and, where needed, the bill text, and keep only bills with a real prediction-market provision. Bills that use phrases like "event contracts" in another sense (for example event-hosting or insurance contracts) are dropped. Status comes from LegiScan; `Enacted / adopted` is LegiScan status 4, meaning signed or otherwise became law. Some bills are companions of each other (for example Tennessee HB2079 and SB1992 are the same law) and are listed separately, as LegiScan does.

**Privacy.** We do not name individual people who sue. Their names are removed from captions and from CourtListener link slugs. Public officials sued in their official capacity, companies, tribes and governments are named as they appear on the docket.

**Updates.** A script rechecks the status of every listed docket and bill each week. New dockets and bills it finds go to a review queue and are added only after a person checks them.

## Limitations

- **Federal courts only.** State-court lawsuits, state criminal cases and state regulators' cease-and-desist orders are not included, except for state cases that were removed to federal court.
- **RECAP coverage.** CourtListener only holds dockets that someone has pulled from PACER. A brand-new filing can take days to appear, and a case nobody has pulled may be missing entirely.
- **Docket metadata, not outcomes.** The data records who sued whom, where and when, and whether the docket is open. It does not record rulings, injunctions or settlements. Read the docket before describing what happened in a case.
- **Classification is a judgment call.** Case types and the separate-listing decision are our calls, made by reading the dockets. The `note` column explains the less obvious ones.
- **Bill searches can miss bills** that address prediction markets without using any of the search terms, and LegiScan statuses can lag the legislatures' own websites.
- **Allegations are not findings.** Complaints describe what one side claims.
- This is a research dataset, not legal advice.

## Update cadence

Weekly, on Mondays. Each update is a commit to `main` that changes the files in `data/`. The live page always has the latest version.

## How to cite

Please cite the dataset and link to the live tracker:

> Angelo, Arthur, and PredictionScout. *Prediction Market Lawsuit Tracker dataset*. PredictionScout. https://predictionscout.com/research/prediction-market-lawsuit-tracker/

`CITATION.cff` has the same information in machine-readable form, and GitHub's "Cite this repository" button uses it. A DOI will be added here once the dataset is archived on Zenodo.

For a chart or news story, a short credit is fine: "Source: PredictionScout Prediction Market Lawsuit Tracker (predictionscout.com)".

## License

The data and documentation are licensed under the [Creative Commons Attribution 4.0 International License (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/). You can share and adapt them for any purpose, including commercial use, as long as you give credit.

Court docket information comes from CourtListener, run by the Free Law Project. Bill information comes from LegiScan, whose data is also licensed under CC BY 4.0.

## Corrections

If a case is missing or a row is wrong, open an issue with the docket link.
