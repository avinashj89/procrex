# Procure-X RFx Manager

**From RFx request to award, in one workspace.** AI does the reading and writing; the buyer makes every decision.

Procure-X is a working prototype of an AI-assisted sourcing tool. A buyer describes what they need in plain English, gets a formal RFx, uploads vendor quotes in any format, compares them in one normalised matrix, picks an award scenario and sends award and regret letters, all in one place.

| | |
|---|---|
| **Live app** | https://claude.ai/artifact/8a3cCrzvNdf1kp9pRYuKUQ |
| **Pitch deck** | https://claude.ai/artifact/9D74KSd867zeMb6TjfnDym |
| **Walkthrough video** | [Add your Loom or Google Drive link] |

> The AI features run on Claude inside claude.ai. Open the live link while signed in to claude.ai and allow Claude when asked. Everything else works in any browser.

![Vendor Analyser: normalised line-item matrix with L1 column and extraction audit](docs/screenshots/vendor-analyser.png)

---

## The problem

Every sourcing event goes through the same four steps, and each one is manual today:

1. **Write the spec:** drafted by hand or copied from an old RFx.
2. **Read the quotes:** PDFs, Excel sheets and phone photos retyped line by line.
3. **Compare:** spreadsheet matrices that break on currency and unit mismatches.
4. **Approve and notify:** approvals chased over email; letters written from scratch.

## What it does

The app follows those four steps as four tabs.

| Tab | What AI does | What the buyer does |
|---|---|---|
| **RFx Creator** | Turns a plain-English request into line items, compliance clauses and terms | Reviews the draft and issues it (or an amendment) to vendors |
| **Vendor Analyser** | Reads quotes from PDF, Excel, CSV, text, email or a photo; maps prices to RFx lines; flags anything uncertain | Approves or corrects flagged prices; requests clarifications |
| **Decision Maker** | Answers questions about the bids from the event's own data | Picks single-source or split award; approvals gate large awards |
| **Email Compose** | Drafts an award or regret letter for every bidder, in a chosen tone | Edits, approves and sends |

| RFx Creator | Decision Maker | Email Compose |
|---|---|---|
| ![RFx Creator](docs/screenshots/rfx-creator.png) | ![Decision Maker](docs/screenshots/decision-maker.png) | ![Email Compose](docs/screenshots/email-compose.png) |

## Design principles

- **AI drafts, people decide.** Every AI output is a draft the buyer can review, edit or reject. Nothing is awarded or sent without a person.
- **Maths in code, not in AI.** Totals, L1/L2/L3 ranks, scores and savings are calculated by the app. The AI explains the numbers but never produces them.
- **Every number is traceable.** Each extracted price keeps its source line, a confidence level and an audit-log entry.
- **Governance in the flow.** Awards above ₹25 L need CPO and Finance sign-off. RFx changes reach the evaluation only when issued to vendors as a numbered amendment.

## How the evaluation works

- **Qualified vendor:** ISO 9001/14001 certified (mandatory clause 01) with a quote received.
- **L-ranks per line:** L1 is the lowest valid rate among qualified vendors; L2 and L3 are the next lowest. Low-confidence prices are excluded until a buyer approves them.
- **Score:** 60% price, 20% compliance, 20% payment terms (longer credit terms free working capital).
- **Recommendation:** the lowest total among complete, qualified quotes.
- **Split award:** the cheapest qualified, reviewed rate on each line, with the saving against the best single source.
- **Currency:** quotes in USD, EUR or GBP are converted to ₹ at the rate the buyer sets for the day. Extraction of a foreign-currency quote waits until today's rate is confirmed.
- **Amendments:** edits in RFx Creator stay a draft until issued. An issued line no qualified vendor has quoted yet is marked "awaiting quotes" and left out of the award, so it never blocks one.

## Product decisions and trade-offs

| In the prototype | Why | In production |
|---|---|---|
| The buyer sets the exchange rate each day | The prototype can't reach a live rate feed | Official daily reference rate, fetched automatically |
| Emails are logged, not sent | Keeps the demo safe; a person always sends | Email service plus e-signature |
| Example event and vendors | Shows the whole flow end to end | ERP and supplier-portal integration |
| Doubtful prices excluded until approved | A wrong price costs more than a review | Confidence thresholds tuned on pilot data |

## Try it in five minutes

1. Open the **live app** on claude.ai, signed in.
2. **RFx Creator:** ask the co-pilot "Tighten the batch rejection rate to 0.5%" and watch the spec update.
3. **Vendor Analyser:** on the Sterling Corrugators card, click **Add quote** and upload [`test-quotes/Sterling_Corrugators_Quote_USD.png`](test-quotes/Sterling_Corrugators_Quote_USD.png), a quote priced in US dollars. Confirm today's USD rate and click **Extract with Claude**. Prices should be converted to ₹, the freight footnote on line 4 flagged for review, and Sterling should become the lowest complete bid.
4. **Decision Maker:** ask the analyst "Should we split or single-source?", record the Finance approval, and execute the award.
5. **Email Compose:** draft the award and regret notices with Claude.

Use **Reset example data** (clock icon, top right) to start again.

## How it's built

- **One self-contained HTML file** ([`procurex-rfx-manager.html`](procurex-rfx-manager.html)): plain HTML, CSS and JavaScript, no framework and no build step.
- **Hosting:** published as a [claude.ai artifact](https://claude.ai/artifact/8a3cCrzvNdf1kp9pRYuKUQ). Its runtime capabilities provide:
  - `sample`: calls to Claude for the co-pilot, quote extraction (including images), analyst and drafting, on the viewer's own Claude account.
  - `db` and `user`: saved views, private to each signed-in user.
  - `downloads`: CSV export of the comparison matrix.
- **File reading in the browser:** PDFs with [pdf.js](https://mozilla.github.io/pdf.js/) 3.11.174 (scanned PDFs are sent to Claude as page images); Excel with [SheetJS](https://sheetjs.com/) 0.18.5. Both load on demand from cdnjs.
- **Data:** the workspace is kept in the browser's local storage. Saved views go to the user's claude.ai account, or the browser when the page runs elsewhere.
- **Design:** screens designed in [Google Stitch](https://stitch.withgoogle.com/) (originals in [`stitch/`](stitch/)), then built with Claude Code. Type is Inter with JetBrains Mono; icons are Material Symbols.

### Run it locally

Open `procurex-rfx-manager.html` in any modern browser. Every screen, calculation, file reader and saved view works locally. The Claude-powered features only run when the page is opened from claude.ai.

## Known limitations

- AI features require claude.ai; each call uses the viewer's own Claude usage.
- Emails, purchase orders and e-signatures are simulated; nothing leaves the page.
- Each viewer's data lives in their own browser, so viewers don't see each other's changes.
- Example data only: the event, vendors and prices are fictional.
- Uploads up to 25 MB; the first 15 pages of a PDF; up to 5 pages of a scanned PDF; the first 60,000 characters of text.

## Repository contents

```
procurex-rfx-manager.html    The app (single file)
stitch/                      Original Google Stitch screen designs (image + HTML)
test-quotes/                 Sample vendor quote (USD) for trying the upload
docs/screenshots/            Screens used in this README
```

## Next steps

A 6-week pilot on one sourcing category, measured on RFx-to-award cycle time, buyer hours per event, prices accepted without correction, savings against benchmark and adoption. After that: ERP integration, a supplier self-upload portal, e-signature and negotiation rounds.

---

Built by **Avinash J** as a product-management exercise.
