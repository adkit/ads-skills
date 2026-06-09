# AdKit CLI

## Setup

1. Run `adkit status`. If it succeeds, you're connected — skip the rest. (The CLI auto-reads an `ADKIT_API_KEY` from the environment, so a headless run is often already set up.)
2. If `adkit` is not found, install it: `npm i -g @adkit/cli`.
3. If `adkit status` says you're not authenticated, connect based on where you're running:
   - **A browser is reachable (local):** run `adkit setup manage` — it opens the browser for the user to sign in and connect ad accounts. Prefer this whenever a browser can open.
   - **No browser (headless / server):** you can't sign in yourself. Tell the user to set an `ADKIT_API_KEY` (created at https://app.adkit.so/dashboard/settings/devices) in the environment you run in, then restart you. Re-run `adkit status` once it's set to confirm.

## Command routing

### Meta

| Task | Command |
| --- | --- |
| Authenticate / connect | `adkit setup` |
| Check accounts and status | `adkit status` |
| Create a campaign | `adkit manage meta campaigns create` |
| Create an ad set | `adkit manage meta adsets create` |
| Create an ad (media + copy) | `adkit manage meta ads create` |
| Upload image or video | `adkit manage meta media upload` |
| Find targeting interests | `adkit manage meta interests search` |
| Review drafts before publish | `adkit manage drafts list` |
| Publish a draft | `adkit manage drafts publish <id>` |
| List campaigns/adsets/ads | `adkit manage meta {entity} list` |

### Google Ads

| Task | Command |
| --- | --- |
| List / connect accounts | `adkit manage google accounts list/available/connect <customer-id>` |
| Create a campaign | `adkit manage google campaigns create` |
| Create an ad group | `adkit manage google ad-groups create` |
| Create a responsive search ad | `adkit manage google ads create` |
| Add / update / remove a keyword | `adkit manage google keywords add/update/remove` |
| Add / remove a negative keyword | `adkit manage google keywords negatives add/remove` |
| Shared negative keyword list | `adkit manage google keywords negative-lists create/list/attach` |
| Performance results | `adkit manage google results` |
| Search terms report | `adkit manage google results search-terms` |
| Keyword research (Keyword Planner) | `adkit manage google research keywords <query>` |
| List / update / delete entity | `adkit manage google {entity} list/update/delete` |

Run `adkit <command> --help` for flags and examples. `--help full` for all fields including JSON-only options.

## Draft workflow

All create commands produce a **draft** by default. Add `--publish` to skip drafts and publish immediately.

**Meta** (Campaign → Ad Set → Ad):

1. `adkit manage meta campaigns create ...` → draft
2. `adkit manage meta adsets create ...` → draft
3. `adkit manage meta ads create ...` → draft
4. `adkit manage drafts list` → review
5. `adkit manage drafts publish <id>` → live

**Google** (Campaign → Ad Group → Ad + Keywords):

1. `adkit manage google campaigns create --name "..." --channel search --budget-daily 10 --countries US --account <id>` → draft
2. `adkit manage google ad-groups create --campaign <id> --name "..." --account <id>` → draft
3. `adkit manage google ads create --ad-group <id> --headlines "H1" --headlines "H2" --headlines "H3" --descriptions "D1" --descriptions "D2" --final-url https://example.com --account <id>` → draft
4. `adkit manage google keywords add --ad-group <id> --text "saas ads" --match-type phrase --account <id>` → draft
5. `adkit manage drafts list` → review
6. `adkit manage drafts publish <id>` → live

**Review URL**: `https://app.adkit.so/manage/drafts?draft={draftId}` — opens the draft detail for human review. Multiple: `?drafts={id1},{id2}`.

## Key behaviors

- **`--data <json>`**: bypasses named flags — pass the full AdKit API request body as JSON.
- **`platformOverrides`**: raw fields on the same normalized resource.
- **`adkit manage platform-api-requests`**: raw platform API access for unsupported native resources, endpoints, or multi-resource workflows when Advanced Platform Access is enabled. Use this for Google DSA dynamic ad groups, webpage criteria, page-feed asset sets, and unsupported PMAX resource flows; then report the AdKit gap with `adkit feedback --type missing_feature`.
- **`--json`**: force JSON output (auto-enabled in non-TTY).
- **Smart defaults**: most campaigns only need a few flags — omitted settings are handled automatically.
- **Account resolution**: auto-resolved if only one ad account connected. Otherwise `--account <id>`.
- **Media handling**: `--media` auto-uploads files and detects image vs video from extension.
