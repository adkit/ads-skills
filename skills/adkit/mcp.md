# AdKit MCP

## Setup

1. Call `adkit_status` to get connected platforms and ad account IDs.
2. If not authenticated: call `adkit_setup` (target: `manage`, optional platform: `meta` or `google`) — returns a URL the user opens to connect.

## Discover params

Call `adkit_help` with a path to discover entities, actions, and required params. Drill down progressively:

- `adkit_help()` → root overview
- `adkit_help({ path: "manage" })` → manage entities
- `adkit_help({ path: "manage meta campaigns create" })` → exact params for creating a Meta campaign

**Always call `adkit_help` before a mutation you haven't done yet in this session.** Don't guess params — discover them.

## Tools

| Tool | Purpose |
| --- | --- |
| `adkit_status` | Connected platforms, ad accounts, project context |
| `adkit_setup` | Link Meta/Google ad accounts (opens browser) |
| `adkit_manage` | CRUD campaigns, ad sets, ads, keywords, media, drafts, results |
| `adkit_studio` | AI ad generation: briefs, image gen, media management |
| `adkit_library` | Browse 200k+ ads from 500+ advertisers, competitor research |
| `adkit_help` | Discover exact params for any operation |
| `adkit_feedback` | Report bugs or suggestions (fire-and-forget) |

## Advanced platform access

- Use `platformOverrides` only for raw fields on the same normalized resource.
- Use `entity: "platform-api-request", action: "mutate"` for unsupported native platform resources, endpoints, or multi-resource workflows when Advanced Platform Access is enabled.
- Google examples: DSA dynamic ad groups, webpage criteria, page-feed asset sets, and unsupported PMAX resource flows belong in `platform-api-request`.
- After using `platform-api-request` for a missing AdKit feature, also send `adkit_feedback` with `type: "missing_feature"`.

## Draft workflow

All create actions produce a **draft** by default. Set `publish: true` only with explicit user approval.

**Meta** (Campaign → Ad Set → Ad):

1. `adkit_manage` — entity: `campaigns`, action: `create`, platform: `meta` → draft
2. `adkit_manage` — entity: `adsets`, action: `create`, platform: `meta` → draft
3. `adkit_manage` — entity: `ads`, action: `create`, platform: `meta` → draft
4. `adkit_manage` — entity: `drafts`, action: `list` → review
5. `adkit_manage` — entity: `drafts`, action: `publish`, ids: `["<id>"]` → live

**Google** (Campaign → Ad Group → Ad + Keywords):

1. `adkit_manage` — entity: `campaigns`, action: `create`, platform: `google`, params: `{ name, channel: "search", budgetDaily, countries, accountId }` → draft
2. `adkit_manage` — entity: `ad-groups`, action: `create`, platform: `google`, params: `{ campaignId, name, accountId }` → draft
3. `adkit_manage` — entity: `ads`, action: `create`, platform: `google`, params: `{ adGroupId, headlines, descriptions, finalUrl, accountId }` → draft
4. `adkit_manage` — entity: `keywords`, action: `add`, platform: `google`, params: `{ adGroupId, text, matchType, accountId }` → draft
5. `adkit_manage` — entity: `drafts`, action: `list` → review
6. `adkit_manage` — entity: `drafts`, action: `publish`, ids: `["<id>"]` → live

**Review URL**: `https://app.adkit.so/manage/drafts?draft={draftId}` — opens the draft detail for human review. Multiple: `?drafts={id1},{id2}`.
