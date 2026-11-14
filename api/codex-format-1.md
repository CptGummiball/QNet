# QNet codex pages of addons – format 1

Addons add pages to QNet's codex as **resources** of their own namespace; no Java call is needed. QNet merges every
namespace's contribution when the codex opens (and again after a resource reload or a language change). This format is
part of the QNet API (`qnet-api-1`); it is versioned by `"schema": 1` and only gets additions.

Write the pages in Markdown and let QNet's generator make the JSON (recommended), or write the JSON yourself.

## Files

```
assets/<ns>/qnet_guide/<lang>/contrib.json              areas, entries, item pages of the addon
assets/<ns>/qnet_guide/<lang>/pages/<path>.json         one file per page (key <ns>:<path>)
assets/<ns>/textures/gui/codex/<file>.png               pictures of image blocks (optional)
```

`<lang>` is a Minecraft language code. `en_us` is required; other languages are optional, a page missing in the player's
language is shown in English. Resource packs may override any of these files.

## contrib.json

```json
{
  "schema": 1,
  "name": "Example Addon",
  "areas": [
    {"id": "example:vats", "title": "Vats", "icon": "example:vat_controller", "color": "B0182A",
     "pages": [{"key": "example:vats/vat", "title": "The Vat", "icon": "example:vat_controller", "order": 10}]}
  ],
  "entries": [
    {"area": "qnet:processing", "key": "example:vats/press", "title": "Press", "icon": "example:press", "order": 305}
  ],
  "items": {"example:vat_controller": "example:vats/vat"}
}
```

| Field | Meaning |
|---|---|
| `name` | name on the badge of the addon's pages (default: the mod's display name) |
| `areas` | own areas, listed after QNet's areas; `id` with the addon's namespace; `color` RRGGBB |
| `entries` | single pages in one of QNet's areas (`qnet:<area>` or `<area>`: `getting_started`, `energy`, `processing`, `storage`, `logic`, `rift`, `reference`); `order` sorts them between QNet's pages (QNet's pages carry their order in `qnet:guide/<lang>/book.json`) |
| `items` | item or block id → page key: the codex key ("?") on that item opens the page |

Rules:
- Page keys are `<ns>:<path>` with the addon's own namespace. Pages with another namespace and keys that exist already are
  left out with a warning in the log (the first wins). Addons cannot replace or remove QNet's pages.
- An entry naming an area QNet does not have is left out with a warning.
- Every page of the addon is marked with a badge (its name) in the page list and the page head.

## Pages

```json
{"schema": 1, "title": "The Vat", "icon": "example:vat_controller", "blocks": [ ... ]}
```

Blocks (the same as QNet's own pages):

| `t` | Fields | Shows |
|---|---|---|
| `h` | `text` | a heading |
| `p` | `spans` | a paragraph |
| `list` | `items` (list of span lists), `ordered` | a list |
| `table` | `head` (span lists), `rows` (lists of span lists) | a table |
| `callout` | `kind` (`note`, `tip`, `important`, `warning`, `caution`), `title`, `body` (spans) | a hint box |
| `item` | `id` | the item large |
| `grid` | `slots` (9 ids or `#tag`, "" = empty), `result`, `count` | a crafting recipe |
| `image` | `tex` (`<ns>:textures/gui/codex/<file>.png`), `w`, `h`, `caption` (spans) | a picture |
| `structure` | `id` (a structure id), `caption` (spans) | the 3D build view: QNet's plants, or an addon structure registered with `QNetApi.structures()` (made from its plan: layers, parts, bill of materials; the plan's `controller` block stands at the controller's place) |

Spans: `{"s": "text", "b": true, "i": true, "c": true, "link": "page:<key>" | "item:<id>"}`. A page link may name the
addon's pages (`page:example:vats/vat`) or QNet's (`page:qnet:energy/smes` or `page:energy/smes`). A link whose page is
not there (an addon that is not installed) is drawn as plain text; nothing breaks.

## Generator

```bash
python tools/qnet/codex.py --addon --source <addon>/docs/codex --namespace <ns> --out <resources dir> \
    --name "Example Addon" --lang-dir <assets/<ns>/lang> --recipes <data/<ns>/recipe> \
    --qnet-data qnet-codex-data-<version>.json --qnet-book <QNet's en_us book.json> [--check]
```

Source: `docs/codex/<lang>/<area>/_area.md` (frontmatter `title`, `icon`, `color`, `order`) and
`docs/codex/<lang>/<area>/<page>.md` (frontmatter `title`, `icon`, `order`, `blocks` = items whose "?" opens the page,
`area: qnet:<area>` = put the page into that QNet area). Markdown: `##` headings, paragraphs, `-`/`1.` lists, `|` tables,
`> [!TIP]` callouts, `**bold**`, `*italic*`, `` `code` ``, `[text](page:…)`, `[text](item:…)` (`[](item:…)` = the item's
name), `{{spotlight:<id>}}`, `{{recipe:<id>}}`, `{{build:<structure>}}`, `{{image:<file> Caption}}` (from
`docs/codex/_images`), `{{value:<ns>:<key>}}` / `{{value:qnet:<key>}}`, `{{config:<ns>:<key>}}`.

Numbers come from placeholders, never by hand: `{{value:<ns>:…}}` and `{{config:<ns>:…}}` read the addon's
`docs/codex/_data/codex_data.json` (`{"values": {...}, "config": {"key": {"default": …, "range": "(a..b)"}}}`),
`{{value:qnet:…}}` reads QNet's values (`qnet-codex-data-<version>.json`, published with every QNet release next to the
API jar). Values are put in when the generator runs: rebuild the pages after updating to a new QNet version.

Every addon item without a page (from the language file given by `--lang-dir`) gets a generated page in the area
`<ns>:items`. Missing placeholders, links, pictures, recipes or areas stop the generator with a list; a page missing in
German is only noted (the codex shows the English page).
