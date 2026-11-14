# Setup notes

Three n8n workflows, each: Manual Trigger -> official Apify node (runs one Apify Actor) -> Code node (flattens nested fields) -> Google Sheets (append).

## 1. Get an Apify API token
1. Create a free account at https://apify.com (or sign in).
2. Console > Settings > API & Integrations > copy your **Personal API token**.
3. In n8n: Credentials > New > **Apify API** > paste the token.
   The Apify node comes from the community package `@apify/n8n-nodes-apify` (n8n: Settings > Community nodes > Install, or it ships with recent n8n Cloud).
   You pay Apify for runs on your own account; the Actors are billed pay per event (table below).

## 2. Import a workflow
n8n: Workflows > Add workflow > menu (three dots) > **Import from file** > pick one JSON from `workflows/`.

## 3. Set credentials
- **Run Apify Actor** node: choose your Apify credential.
- **Append to Google Sheets** node: choose (or create) a Google Sheets OAuth2 credential, then pick your spreadsheet and the tab name. The first run creates columns from the Actor's output fields; give the sheet a header row if you want fixed columns.
- SEC workflows only: put your own email in `secContactEmail` in the Apify node's input JSON. The SEC asks every automated client to identify itself.

## 4. Run it
Click **Execute workflow**. To run on a schedule, replace the Manual Trigger with a Schedule Trigger.
Each Apify node sets *Maximum Cost per Run* ($0.50 TikTok, $1.00 each SEC workflow), so one run cannot spend more than that. If the Actor returns 0 items the workflow stops with an error and writes nothing to the sheet.

## What a run costs (Apify pay per event, read from the Apify API on 2026-11-14)
| Workflow | Event | Price per event |
|---|---|---|
| TikTok trending | Trending item | $0.002 |
| SEC 13D/13G stakes | Schedule 13D/13G filing | $0.001 |
| | Ownership stake record delivered | $0.001 |
| | New Schedule 13D (activist stake) delivered | $0.02 |
| SEC Form 4 clusters | Form 4 filing | $0.0004 |
| | Insider cluster detected | $0.02 |

Example from the arithmetic only (not a measured run): a Form 4 run that parses 300 filings and finds 2 clusters = 300 x $0.0004 + 2 x $0.02 = $0.16. Prices can change; the Actor's Apify store page is the source of truth.

## What has not been verified
See the README section "What has and has not been checked": the workflows were not executed in a running n8n, and no fresh Actor run was possible on 2026-11-14 (Apify free-plan credit used up).
