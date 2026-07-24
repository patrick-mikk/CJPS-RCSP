# Developing Zotero Citation Style (CSL) Files — Deep Research

A practical, end-to-end guide for developers who author, test, and contribute Citation Style Language (CSL) styles used by Zotero — covering author-date, numeric, and note (footnote) styles.

---

## 1. Overview

### What CSL is

The **Citation Style Language (CSL)** is an open, XML-based language for describing how citations, notes, and bibliographies should be formatted. A style file encodes the punctuation, capitalization, ordering, and element selection that a reference manager applies when it renders a citation. CSL is consumed by Zotero, Mendeley, Papers, and many other tools, so a single style file works across the whole ecosystem.

A CSL style has two jobs:

1. **Define the format** — how in-text citations, notes, and bibliography entries look — via the `cs:citation` and `cs:bibliography` elements.
2. **Describe itself** — self-documenting metadata (name, author, id, license, category) via the `cs:info` block.

Localization data (translated terms, date formats, grammar rules) is deliberately kept **out** of the style and stored in shared **locale files**, so styles stay compact and language-agnostic.

### The two style classes

Every independent style declares one of two classes on its root element, and this choice drives almost everything downstream:

- **`class="in-text"`** — the citation renders inline in the body text. This covers both **author-date** styles (`(Doe 2020)`, §4) and **numeric** styles (`[1]`, §5).
- **`class="note"`** — the citation is rendered into a footnote or endnote by the word processor, and the style is expected to handle **position-dependent** behavior such as "Ibid." and short-form subsequent citations (§6). Chicago (notes-bibliography) and OSCOLA are the canonical examples, and note styles are roughly half the real-world ecosystem.

The `class` value does not itself insert the note — the word processor does that for note styles — but it tells the processor which formatting model applies and enables the `position` tests.

### CSL 1.0.2

The current stable specification is **CSL 1.0.2**, a backward-compatible superset of 1.0.1. Importantly, the `version` attribute on the root element stays `"1.0"` for all CSL 1.0-family styles (1.0, 1.0.1, and 1.0.2). Version 1.0.2 clarifies and adds options over 1.0.1 while remaining fully compatible; a processor advertising "standard CSL 1.0.2 support" parses the same overall structure — metadata, locales, macros, citation, and bibliography.

### How Zotero consumes CSL

Zotero ships thousands of styles and lets you install more. When you insert a citation, Zotero's citation processor (`citeproc-js`) reads the selected style, resolves any locale files, and renders your library items through the style's rules. Installed styles live in Zotero's data directory (the `styles` folder). Zotero also exposes editing and preview tooling directly in its preferences (see §7).

### Independent vs dependent styles

- **Independent styles** contain the full formatting logic (`class="in-text"` or `class="note"`) plus their own macros, citation, and bibliography definitions.
- **Dependent styles** contain **no formatting of their own**. They are aliases that point to an independent "parent" style via a `<link rel="independent-parent" href="..."/>` element in `cs:info`. Many journals that share the same house format (e.g. all Nature-formatted journals) are shipped as dependent styles. A dependent style may also set `default-locale` to localize its parent.

---

## 2. Anatomy of a CSL File

### Top-level structure (in order)

A CSL file is rooted in `cs:style` in the namespace `http://purl.org/net/xbiblio/csl`. Its children appear in a fixed order:

| Element | Cardinality | Purpose |
|---|---|---|
| `cs:info` | required, once | Style metadata |
| `cs:locale` | optional, repeatable | In-style term/date overrides |
| `cs:macro` | zero or more | Reusable named rendering blocks |
| `cs:citation` | required, once | In-text (or note) citation format |
| `cs:bibliography` | optional, once | Bibliography/reference-list format |

Both `cs:citation` and `cs:bibliography` contain an optional `cs:sort` **before** a required `cs:layout`.

Root attributes:

- `xmlns="http://purl.org/net/xbiblio/csl"` (required)
- `version="1.0"` (required)
- `class="in-text"` or `class="note"` (required on independent styles)
- `default-locale="en-US"` (optional; sets the style's default language/dialect)

### The `<info>` metadata block

**Required children:**

- `cs:title` — human-readable style name shown to users.
- `cs:id` — a stable, unique identifier expressed as a URI (a UUID is recommended for brand-new styles not destined for the official repo; the URI need not resolve).
- `cs:updated` — an ISO/W3C timestamp with timezone of the last change (e.g. `2014-06-16T12:00:00+00:00`).

**Common optional children:**

- `cs:author` / `cs:contributor` — each with `cs:name`, `cs:email`, `cs:uri` (e.g. an ORCID).
- `cs:category` — `citation-format="author-date|numeric|note|label|author"` and `field="..."` (discipline, e.g. `science`, `history`).
- `cs:issn`, `cs:eissn`, `cs:issnl` — journal identifiers.
- `cs:link` — `href` + `rel` where `rel` is `self`, `template`, `documentation`, or `independent-parent`.
- `cs:published` — initial creation timestamp.
- `cs:rights` — license statement (with optional `license` attribute).
- `cs:summary` — description.
- `cs:title-short` — abbreviation (e.g. "APA").

`cs:link`, `cs:rights`, `cs:summary`, `cs:title`, and `cs:title-short` support `xml:lang`. The `cs:info` design is based on the Atom Syndication Format.

### `<locale>` and terms

An in-style `cs:locale` redefines or supplements the shared locale files without editing them. It carries an optional `xml:lang` (if omitted, it applies to all locales) and may contain `cs:style-options`, `cs:date`, and `cs:terms`. See §9 for the full localization model.

### `<macro>`

A `cs:macro` is a named, reusable rendering block:

```xml
<macro name="author">
  <names variable="author">
    <name initialize-with=". "/>
  </names>
</macro>
```

It is invoked elsewhere via `macro="author"` on `cs:text` or on a sort `cs:key`. Macros can contain any rendering elements, including conditional `cs:choose` logic.

### `<citation>` and `<bibliography>`

Both hold an optional `cs:sort` then a required `cs:layout`. Each also exposes its own tunable options (disambiguation, collapsing, hanging indents — detailed in §4–§6).

### Key rendering elements

Seven elements produce output:

- **`cs:text`** — text from exactly one source: `variable="..."`, `macro="..."`, `term="..."`, or a literal `value="..."`.
- **`cs:date`** — date variables, either localized or built from `cs:date-part name="year|month|day"` (see the dedicated subsection below).
- **`cs:number`** — number variables; `form="numeric|ordinal|long-ordinal|roman"`.
- **`cs:names`** — name variables with et-al handling; contains `cs:name`, `cs:et-al`, `cs:substitute`, `cs:label`.
- **`cs:label`** — renders a term matching a variable (locator, page, number); `form="long|short|symbol"`, `plural="contextual|always|never"`.
- **`cs:group`** — a container that applies its `delimiter`/affixes only if at least one child **variable** produces output; suppressed entirely otherwise. This is the primary tool for conditional punctuation. (Note the "variable" qualifier: a group whose only output is literal `value=`/`term=` text is *not* suppressed — see the troubleshooting table in §8.)
- **`cs:choose`** — conditional branching via `cs:if`, `cs:else-if`, `cs:else`.

**`cs:names` / `cs:name` and name-parts:** Personal names always have a `family` part and may include `given`, `suffix`, `non-dropping-particle` (e.g. Dutch "van" in "van Gogh", kept with the surname), and `dropping-particle` (e.g. "van" in "Ludwig van Beethoven", dropped in short form). `cs:name-part name="given|family"` gives per-part formatting (e.g. uppercase family names).

**et-al attributes** (settable/inheritable on `cs:style`, `cs:citation`, `cs:bibliography`, `cs:names`, `cs:name`): `et-al-min`, `et-al-use-first`, `et-al-use-last`, `et-al-subsequent-min`, `et-al-subsequent-use-first`, plus `and`, `delimiter-precedes-et-al`, `delimiter-precedes-last`, `initialize`, `initialize-with`, `name-as-sort-order`, `sort-separator`, `name-form`, `name-delimiter`, `names-delimiter`.

**`cs:choose` test attributes** (on `cs:if`/`cs:else-if`): `variable` (non-empty test), `type` (item type), `is-numeric`, `position` (`first|ibid|ibid-with-locator|subsequent|near-note`), `disambiguate`, `locator` (locator type). Combine multiple conditions with `match="all|any|none"` (default `all`). The `position` tests are central to note styles and are worked through in §6.

**`cs:sort` / `cs:key`:** `cs:sort` holds ordered `cs:key` children, each with `variable="..."` OR `macro="..."`, plus `sort="ascending"` (default) or `"descending"`, and optional `names-min`/`names-use-first`/`names-use-last`.

**Formatting attributes** (on most rendering elements): `font-style` (`normal|italic|oblique`), `font-variant` (`normal|small-caps`), `font-weight` (`normal|bold|light`), `text-decoration` (`none|underline`), `vertical-align` (`baseline|sup|sub`), `text-case` (`lowercase|uppercase|capitalize-first|capitalize-all|title|sentence` [deprecated]), `quotes` (`cs:text` only), and `display` (`block|left-margin|right-inline|indent`).

**Affixes and delimiters:** `prefix`/`suffix` wrap output and are applied **only when the element produces output**. `delimiter` separates non-empty children and is allowed on `cs:layout`, `cs:group`, `cs:names`, `cs:name`, and `cs:date`. A nested delimiting element does not inherit an ancestor's delimiter.

### Working with `cs:date`

`cs:date` renders a date variable (`issued`, `accessed`, `original-date`, `event-date`, `submitted`, …) in one of two modes.

**Localized mode** (`form="text"` or `form="numeric"`) reuses the date layout defined in the active locale file, so a single element produces locale-correct output:

```xml
<!-- With the en-US locale: "June 16, 2014"; with fr-FR: "16 juin 2014" -->
<date variable="issued" form="text"/>

<!-- Localized numeric: "6/16/2014" (en-US) -->
<date variable="issued" form="numeric"/>
```

You can restrict which parts a localized date renders with `date-parts` (`year-month-day` [default], `year-month`, or `year`):

```xml
<date variable="issued" form="text" date-parts="year-month"/>   <!-- "June 2014" -->
```

**Non-localized mode** builds the date from explicit `cs:date-part` children (`year`, `month`, `day`), each of which accepts its own `form`, affixes, and `text-case`. This is what an author-date style uses to render only the year, and it is also where you override month/season rendering:

```xml
<date variable="issued">
  <date-part name="year"/>
</date>

<!-- Custom "Spring 2021": a season is stored in the month slot (13–16),
     so month form="long" renders the season term -->
<date variable="issued">
  <date-part name="month" form="long" suffix=" "/>
  <date-part name="year"/>
</date>
```

**Date ranges** are handled automatically: when a date variable holds a start and end date, the processor renders both and joins the differing parts with the locale's range delimiter (an en-dash), e.g. `2019–2021` or `June–July 2020`. In sorting, a range sorts on its start date first, then its end date (§4, sort semantics). **BC/negative years** and less-specific dates are handled by the `YYYYMMDD` zero-padded sort key described in §4. Locale files themselves define the two canonical date layouts (`form="text"` and `form="numeric"`); a style only needs `cs:date-part` overrides when it deviates from those (see §9).

### Annotated skeleton

```xml
<?xml version="1.0" encoding="utf-8"?>
<style xmlns="http://purl.org/net/xbiblio/csl"
       class="in-text"
       version="1.0"
       default-locale="en-US">
  <!-- ===== Metadata ===== -->
  <info>
    <title>Example Author-Date Style</title>
    <title-short>Example</title-short>
    <id>http://www.zotero.org/styles/example-author-date</id>
    <link href="http://www.zotero.org/styles/example-author-date" rel="self"/>
    <link href="https://example.org/style-guide" rel="documentation"/>
    <author>
      <name>Jane Developer</name>
      <email>jane@example.org</email>
    </author>
    <category citation-format="author-date"/>
    <category field="science"/>
    <summary>A minimal author-date style for demonstration.</summary>
    <updated>2026-07-24T00:00:00+00:00</updated>
    <rights license="http://creativecommons.org/licenses/by-sa/3.0/">
      This work is licensed under a Creative Commons Attribution-ShareAlike 3.0 License</rights>
  </info>

  <!-- ===== Optional in-style locale override ===== -->
  <locale xml:lang="en">
    <terms>
      <term name="no date">n.d.</term>
    </terms>
  </locale>

  <!-- ===== Reusable macros ===== -->
  <macro name="author">
    <names variable="author">
      <name initialize-with=". " name-as-sort-order="first" and="text"
            delimiter=", "/>
      <substitute>
        <names variable="editor"/>
        <text variable="title"/>
      </substitute>
    </names>
  </macro>

  <macro name="issued-year">
    <choose>
      <if variable="issued">
        <date variable="issued">
          <date-part name="year"/>
        </date>
      </if>
      <else>
        <text term="no date"/>
      </else>
    </choose>
  </macro>

  <!-- ===== In-text citation ===== -->
  <citation et-al-min="3" et-al-use-first="1"
            disambiguate-add-year-suffix="true"
            disambiguate-add-givenname="true"
            collapse="year">
    <sort>
      <key macro="author"/>
      <key macro="issued-year"/>
    </sort>
    <layout prefix="(" suffix=")" delimiter="; ">
      <group delimiter=", ">
        <text macro="author"/>
        <text macro="issued-year"/>
      </group>
    </layout>
  </citation>

  <!-- ===== Bibliography ===== -->
  <bibliography hanging-indent="true">
    <sort>
      <key macro="author"/>
      <key macro="issued-year"/>
      <key variable="title"/>
    </sort>
    <layout suffix=".">
      <group delimiter=". ">
        <text macro="author"/>
        <text macro="issued-year"/>
        <text variable="title" font-style="italic"/>
        <text variable="container-title"/>
      </group>
    </layout>
  </bibliography>
</style>
```

---

## 3. CSL Item Types & Variables

Real styles are built almost entirely around **branching on item type** (`<choose><if type="...">`) and **selecting variables** (`variable="..."`). You cannot author a style without knowing the vocabulary, so this section is the reference the rest of the guide draws on. The authoritative lists live in the schema modules validated in §8 (`csl-types.rnc` and `csl-variables.rnc`); the tables below cover the common members.

### Common item types

An item's `type` is set by the reference manager from its record. A style typically has a `cs:choose` chain that renders books, chapters, articles, and web pages differently.

| Type | Typical use |
|---|---|
| `book` | Whole book / monograph |
| `chapter` | Chapter in an edited book (uses `container-title` for the book) |
| `article-journal` | Journal article |
| `article-magazine` / `article-newspaper` | Magazine / newspaper article |
| `paper-conference` | Conference paper / proceedings |
| `report` | Technical or institutional report |
| `thesis` | Dissertation / thesis (see `genre` for "PhD diss.") |
| `webpage` | Standalone web page |
| `post-weblog` / `post` | Blog post / forum post |
| `dataset` | Published dataset |
| `software` | Software / code |
| `manuscript` | Unpublished manuscript |
| `entry-dictionary` / `entry-encyclopedia` | Reference-work entry |
| `legal_case` / `legislation` / `bill` / `regulation` / `treaty` | Legal materials (see §9 on `jurisdiction`) |
| `patent`, `map`, `motion_picture`, `song`, `broadcast`, `interview`, `personal_communication`, `speech`, `standard` | Specialized types |

### Core variables

Variables fall into four categories, and this matters because each category is rendered by a different element (`cs:text` for standard/number, `cs:date` for dates, `cs:names` for names) and sorts differently (§4).

**Standard (text) variables** — rendered with `cs:text variable="..."`:
`title`, `container-title`, `collection-title`, `original-title`, `publisher`, `publisher-place`, `event-place`, `page`, `locator`, `version`, `genre`, `medium`, `DOI`, `URL`, `ISBN`, `ISSN`, `call-number`, `archive`, `note`, `abstract`, `status`, `jurisdiction`, `authority`.

**Number variables** — rendered with `cs:number` (support `form="ordinal|roman|…"` and `is-numeric` tests):
`volume`, `issue`, `number`, `edition`, `chapter-number`, `collection-number`, `page-first`, `citation-number` (the sequential cite index used by numeric styles, §5), `first-reference-note-number` (the footnote number of the first cite of an item, used by note styles, §6).

**Date variables** — rendered with `cs:date` (§2):
`issued`, `accessed`, `original-date`, `event-date`, `submitted`, `available-date`.

**Name variables** — rendered with `cs:names variable="..."`:
`author`, `editor`, `translator`, `container-author`, `collection-editor`, `editorial-director`, `composer`, `director`, `recipient`, `interviewer`, `illustrator`, `original-author`.

Two of the number variables — `citation-number` and `first-reference-note-number` — are computed by the processor rather than stored on the item, and they are the backbone of numeric and note styles respectively.

---

## 4. Building an Author-Date Style

Author-date styles (Harvard-family, APA-like) are the most common target. Note that "Harvard" is **not** a single official style — build to a specific journal's or manual's rules.

### Name formatting

Set name attributes on `cs:name` (or inherit them from `cs:style`/`cs:citation`/`cs:bibliography` to avoid repetition; the lowest-level setting wins).

- **`initialize-with`** — turns given names into initials, appending the attribute value after each (e.g. `". "` → "J. R."). `initialize="false"` keeps full names but still appends the string after existing initials.
- **`name-as-sort-order`** — `"first"` inverts only the first name ("Doe, John"); `"all"` inverts every name.
- **`sort-separator`** — delimiter for inverted parts (default `", "`).
- **`and`** — `"text"` uses the "and" term; `"symbol"` uses "&".
- **`delimiter-precedes-last`** — `contextual` (default: delimiter before the final "and" only for 3+ names), `after-inverted-name`, `always`, `never`.
- **`form`** — `"long"` (default) or `"short"` (family + non-dropping-particle only).

### et-al thresholds

- **`et-al-min`** / **`et-al-use-first`** — when the number of names **meets or exceeds** `et-al-min`, truncate after `et-al-use-first` names and append the "et al." term. A common author-date setting is `et-al-min="3" et-al-use-first="1"` ("Doe et al."). When truncating to one name, a space separates the name and "et al."; truncating to 2+ uses the name delimiter.
- **`et-al-subsequent-min`** / **`et-al-subsequent-use-first`** — override the above for repeat cites.
- **`et-al-use-last="true"`** — appends an ellipsis and the final name of the list (requires at least two more names than shown).
- **`delimiter-precedes-et-al`** — `contextual` (default), `after-inverted-name`, `always`, `never`.
- **`cs:et-al`** — an optional child of `cs:names` (after `cs:name`) for formatting the term, e.g. `<et-al font-style="italic"/>` or `<et-al term="and others"/>`.

### Rendering locators and labels in a cite

A **locator** is the page/chapter/figure the reader supplies at citation time (it lives on the cite, not the item — see `CITATION-ITEMS` in §8, where each cite object may carry `locator` and `label`). Core citation behavior is rendering it *inside* the cite, e.g. `(Doe 2020, p. 5)`. Use `cs:label variable="locator"` for the label term and `cs:text variable="locator"` for the value, wrapped in a `cs:group` so the comma and label only appear when a locator is present:

```xml
<layout prefix="(" suffix=")" delimiter="; ">
  <group delimiter=", ">
    <text macro="author"/>
    <text macro="issued-year"/>
    <group delimiter=" ">
      <label variable="locator" form="short"/>   <!-- "p." / "pp." / "chap." -->
      <text variable="locator"/>                  <!-- "5" / "5–9" -->
    </group>
  </group>
</layout>
```

Given a cite `{"id":"doe2020","label":"page","locator":"5"}`, this renders `(Doe 2020, p. 5)`. The `cs:label` `form` (`long`/`short`/`symbol`) and `plural` (`contextual`/`always`/`never`) control whether you get "page", "p.", or "§", and whether it pluralizes for a range. Because the inner `cs:group` self-suppresses when `locator` is empty, the same layout also produces a clean `(Doe 2020)` for cites with no locator.

### Disambiguation

Per the CSL 1.0.2 specification (Disambiguation), a processor always tries four methods **in this fixed order**, stopping as soon as cites become distinct:

1. **Expand given names** — `disambiguate-add-givenname="true"`; e.g. `(Simpson 2005; Simpson 2005)` → `(H. Simpson 2005; B. Simpson 2005)`. Behavior is tuned by `givenname-disambiguation-rule`:
   - `by-cite` (default) — only ambiguous names in ambiguous cites, stopping at the first disambiguating name.
   - `all-names`, `all-names-with-initials`, `primary-name`, `primary-name-with-initials` — the `all-names*` variants also disambiguate names in otherwise-unambiguous cites (e.g. `(Doe 1950; Doe 2000)` → `(Jane Doe 1950; John Doe 2000)`).
2. **Show more names** — `disambiguate-add-names="true"`; reveals names hidden by et-al one at a time. If both add-givenname and add-names are true, given-name expansion runs first.
3. **The `disambiguate` condition** — a `cs:choose`/`cs:if disambiguate="true"` branch renders extra distinguishing content.
4. **Year-suffix** — `disambiguate-add-year-suffix="true"`; appends `a`, `b`, `c`… (continuing `z, aa, ab…`, in bibliography order). The spec designates this the last resort precisely **because it is always able to succeed** — any two remaining collisions can be forced apart by a fresh letter, which is why the ordered fallback terminates here. By default the suffix attaches to the first `cs:date` year; to control its position, render the `year-suffix` variable explicitly via `cs:text`.

A style opts into methods 1, 2, and 4 with the three `disambiguate-*` attributes on `cs:citation`; method 3 is opt-in wherever you place a `disambiguate="true"` test.

### Year-suffix, grouping & collapsing

Author-date citations often group and collapse cites that share an author:

- **`cite-group-delimiter`** (default `", "`) activates grouping of cites with identical rendered names: `(Doe 1999; Doe 2006; Smith 2002)`.
- **`collapse`** — `year` → `(Doe 2000, 2001)`; `year-suffix` → `(Doe 2000a, b)`; `year-suffix-ranged` → `(Doe 2000a–c, e)`; `citation-number` → `[1–3, 5]` (numeric styles, §5).
- **`year-suffix-delimiter`** and **`after-collapse-delimiter`** customize the joins; per the spec's Cite Grouping/Collapsing section, collapsed year-suffix and citation-number ranges use an en-dash, and `after-collapse-delimiter` replaces the normal delimiter *after* a collapsed group.

### Sorting: citation vs bibliography

- **Bibliography** almost always sorts explicitly: `<key macro="author"/>`, then `<key macro="issued-year"/>`, then `<key variable="title"/>`.
- **Citation** sorting orders cites **within a single citation** (e.g. multiple works in one parenthesis); with no `cs:sort`, cites keep cited/appearance order.
- **Sort semantics** (CSL 1.0.2 spec, Sorting): comparison is case-insensitive, and **empty sort-key values are placed at the end in both ascending and descending order** — the spec states this explicitly so that, e.g., a `<key variable="issued"/>` tie-break does not float undated items to the top of a descending list. Names called through a key sort long-form with `name-as-sort-order="all"`. Dates sort as `YYYYMMDD` with zero-padding, so less-specific dates precede more-specific of the same year; BC (negative) years sort inversely; ranges use start date then end date.
- **Prefer sorting by a macro rather than a raw variable** — the spec's Sorting section notes a macro key lets author substitution apply (empty author → editor), lets et-al abbreviation apply, and lets you sort by surname only (`form="short"`) or by name count (`form="count"`) — none of which a raw `variable="author"` key can do.

### Bibliography layout options

- `hanging-indent="true"` for hanging indents.
- `second-field-align="flush|margin"` aligns wrapped lines to the second field (used with a numeric first field — see §5).
- `line-spacing` (default 1), `entry-spacing` (default 1).
- `subsequent-author-substitute` (e.g. `"———"`) with `subsequent-author-substitute-rule` (`complete-all` [default], `complete-each`, `partial-each`, `partial-first`).

### Relevant global options (on `cs:style`)

- `initialize-with-hyphen` (default `true`, for compound given names).
- `page-range-format` (`chicago|expanded|minimal|minimal-two`).
- `demote-non-dropping-particle` (`never|sort-only|display-and-sort`).

---

## 5. Building a Numeric Style

Numeric styles (Vancouver, IEEE, Nature) replace the author-year cite with a sequential number — `[1]`, `(1)`, or a superscript `1` — and print a matching numbered reference list. Everything hinges on the processor-computed **`citation-number`** variable, which is the item's position in the reference list.

### The in-text number

Render `citation-number` in the citation layout, and use affixes for the bracket style — this is the one-line change that turns `(1)` into `[1]`:

```xml
<citation collapse="citation-number">
  <sort>
    <key variable="citation-number"/>
  </sort>
  <layout prefix="[" suffix="]" delimiter=", ">
    <text variable="citation-number"/>
  </layout>
</citation>
```

- **Brackets vs parentheses vs superscript:** `prefix="["`/`suffix="]"` gives `[1]`; `prefix="("`/`suffix=")"` gives `(1)`; for a superscript style drop the affixes and set `vertical-align="sup"` on the `cs:text` instead.
- **`collapse="citation-number"`** turns a multi-item cite into a compressed range: cites `1,2,3,5` render as `[1–3, 5]` (en-dash ranges, per §4's collapsing rules). `after-collapse-delimiter` controls the delimiter used after each collapsed run.
- **Sorting:** most numeric styles sort the *bibliography* in citation order (i.e. the reference list is numbered in the order items are first cited), so the numeric `citation-number` sort key is placed on `cs:citation` to order multiple cites within one bracket, while the bibliography either sorts by `citation-number` (citation order) or, for some styles, alphabetically by author.

### The numbered reference list

The bibliography prints the number as a first field and aligns the entry text against it with **`second-field-align`**:

```xml
<bibliography second-field-align="flush" entry-spacing="0">
  <layout>
    <text variable="citation-number" prefix="[" suffix="]"/>
    <group delimiter=". ">
      <text macro="author"/>
      <text variable="title"/>
      <text variable="container-title" font-style="italic"/>
    </group>
  </layout>
</bibliography>
```

- **`second-field-align="flush"`** puts the first rendered element (`[1]`) in the margin and flushes the wrapped lines of the entry to the start of the *second* field, producing the classic hanging, number-aligned reference list. `"margin"` instead pulls the number into the left margin.
- Numeric styles typically do **not** use author-date disambiguation (no year-suffix), since the number is inherently unique; the author macro is rendered plainly.

Many numeric journal styles are **dependent** styles pointing at a shared parent — e.g. the *Scientific Reports* style is a dependent alias of `nature-brackets` (see §10). If an existing numeric parent already matches, you write only a dependent stub.

---

## 6. Building a Note (Footnote) Style

Note styles (`class="note"`) — Chicago notes-bibliography, OSCOLA, many humanities and legal styles — are roughly half the ecosystem, and they are structurally different from in-text styles: the word processor places each citation in a footnote or endnote, and the style must vary its output based on **where the cite sits relative to previous cites of the same item**. That variation is driven entirely by the `position` test on `cs:choose`.

### The `position` values

On `<if position="...">` (and combinable with `match`):

- **`first`** — the first time this item is cited. Render the **full** citation (full author, full title, publisher, page).
- **`subsequent`** — any later cite of the same item. Render a **short form** (e.g. author surname + short title, or author + note number).
- **`ibid`** — the immediately preceding cite was the **same item with the same locator**. Render "Ibid." alone.
- **`ibid-with-locator`** — the immediately preceding cite was the same item but a **different locator**. Render "Ibid., p. 12."
- **`near-note`** — an earlier cite of this item appeared within `near-note-distance` footnotes (default **5**), controlled by the `near-note-distance` attribute on `cs:citation`. Used to decide whether a short form can rely on proximity.

Because `ibid` and `ibid-with-locator` are more specific than `subsequent`, order the branches from most to least specific and combine them with `match`.

### A worked note-position macro

```xml
<citation near-note-distance="5">
  <layout suffix=".">
    <choose>
      <!-- 1. Same item + same locator as the cite just before -->
      <if position="ibid">
        <text term="ibid" text-case="capitalize-first"/>   <!-- "Ibid" -->
      </if>
      <!-- 2. Same item, different locator -->
      <else-if position="ibid-with-locator">
        <group delimiter=", ">
          <text term="ibid" text-case="capitalize-first"/>
          <group delimiter=" ">
            <label variable="locator" form="short"/>
            <text variable="locator"/>
          </group>
        </group>
      </else-if>
      <!-- 3. Any other repeat cite: short form -->
      <else-if position="subsequent">
        <group delimiter=", ">
          <names variable="author">
            <name form="short"/>                 <!-- "Smith" -->
          </names>
          <text variable="title" form="short" font-style="italic"/>
          <group delimiter=" ">
            <label variable="locator" form="short"/>
            <text variable="locator"/>
          </group>
        </group>
      </else-if>
      <!-- 4. First cite: full note -->
      <else>
        <group delimiter=", ">
          <text macro="author-note"/>            <!-- "John Smith" (given-first) -->
          <text variable="title" font-style="italic"/>
          <text macro="publisher"/>
          <group delimiter=" ">
            <label variable="locator" form="short"/>
            <text variable="locator"/>
          </group>
        </group>
      </else>
    </choose>
  </layout>
</citation>
```

This produces the canonical Chicago-note progression:

1. First: `John Smith, The Great Work (Chicago: Univ. Press, 2019), 12.`
2. Immediately after, same page: `Ibid.`
3. Immediately after, different page: `Ibid., 15.`
4. Later, after intervening cites: `Smith, Great Work, 20.`

Two details matter. The **"Ibid." term** comes from the locale (`<text term="ibid"/>`), so it localizes automatically. And note styles frequently reference **`first-reference-note-number`** (§3) in the short form to produce cross-references like "Smith, *Great Work* (see n. 4)". Note styles still usually ship a `cs:bibliography` for the end-of-work reference list, formatted much like an author-date bibliography but with given-name-first authors.

---

## 7. Tooling & Workflow

### Zotero's built-in Style Editor

Open it from the **"Style Editor"** button at the bottom of the **Cite** pane in Zotero preferences (the menu path is *Edit > Settings/Preferences > Cite* on Windows/Linux, or *Zotero > Settings > Cite* on Mac). It is also historically called the Reference Test Pane / Zotero CSL Editor / `csledit`.

- The **upper pane** holds editable CSL XML; the **lower pane** shows formatted in-text citations and bibliography entries rendered against items you select in your local library.
- Select one or more library items and click **Refresh** to change the test data; as you edit, the preview updates automatically.
- **Error behavior:** invalid XML raises an error; valid XML that is invalid CSL may raise an error or simply stop updating the preview. If the editor misbehaves, disable plugins, and if needed reset styles from *Advanced > Files and Folders*.

### Zotero's Style Preview pane

The separate **"Style Preview"** button (also on the Cite pane) renders your selected items across **all installed styles at once**, so you can compare formats. You can filter by class (author-date, numeric, note, …) and change the citation language.

### The Visual CSL Editor

The web-based, no-code GUI at **https://editor.citationstyles.org/** (developed by Mendeley and Columbia University Library) is the recommended starting point for most users:

- **Search by name** to find a similar base style and click **Edit**, or search **by example** if you know the exact target citation format.
- **Style > Upload Style** loads an existing `.csl`; **Style > New Style** starts from scratch.
- The interface has a **Style overview** panel (the tree of logic elements), an **Example** panel (live preview), and an **Info** panel (settings of the selected object).
- Common point-and-click customizations: bold/italicize author names, journal titles, or article titles; add a trailing period; switch `(1)` to `[1]`; change bibliography sort order; set the et-al threshold (mapping to `et-al-min`/`et-al-use-first`, `disambiguate-add-givenname`, and group `delimiter`/`prefix`/`suffix`).

*Caveat:* a style built in the Visual Editor occasionally fails to import into Zotero, usually a validity or ID problem — validate before installing.

### Editing locally

For hand-editing, use an XML-aware editor: **VS Code**, **oXygen XML Editor**, **Emacs (nXML mode)**, or **jEdit**. Always start from a style on the `master` branch of the official `styles` repo, and always validate against the CSL schema before use.

### Installing an edited style

1. Save the file with a **`.csl`** extension.
2. In the **Cite** pane, click the **"+"** below the installed-styles list and open your file. It is copied into Zotero's `styles` folder.

> **Critical:** Before installing an edited copy of an existing style, change **both** `<title>` and `<id>` (and the `rel="self"` link) near the top of the file. Otherwise Zotero overwrites your changes the next time the original official style updates, and your style may not appear as a distinct entry. Example: change `<id>http://www.zotero.org/styles/apa</id>` to `.../apa-modified` and the `<title>` to "…Modified". The ID URL does not need to resolve.

To install existing repository styles (10,000+), use **"Get additional styles…"** in the Style Manager on the Cite pane.

---

## 8. Validation, Testing & Debugging

### Schema validation (RELAX NG)

CSL styles and locales validate against the official **RELAX NG** schema maintained in the `citation-style-language/schema` repo. Validate against **`csl.rnc`** (RELAX NG Compact syntax), which bundles the other modules: `csl-categories.rnc`, `csl-terms.rnc`, `csl-types.rnc`, `csl-variables.rnc` (the last two are the authoritative lists behind the §3 tables). Your validator must support RELAX NG **Compact** syntax. Version-pinned schemas are fetchable, e.g. `github.com/citation-style-language/schema/raw/v1.0.1/csl.rnc`.

**Command-line validators:**

- **jing** (`thaiopensource.com/relaxng/jing.html`)
- **RNV** (`davidashen.net/rnv.html`)

**Two-stage validation is required** because plain RELAX NG cannot check that every called macro is actually defined. The schema embeds **Schematron** rules for this (e.g. verifying that each `<text macro="X"/>` or sort `<key macro="X"/>` has a matching `<macro name="X">`), but **jing ignores embedded Schematron**. So:

1. Validate structure against `csl.rnc` with jing.
2. Validate the Schematron/macro rules against the separately provided **`csl.sch`**.

**Web validators:**

- **https://validator.citationstyles.org/** (built on validator.nu) — accepts a URL, a file upload, or pasted text. Because it uses jing/validator.nu internally, it does **not** run the Schematron macro checks.
- **csl-validator.js** (`simonster.github.io/csl-validator.js`) — a browser-based alternative validating against the 1.0.1 schema.

A minimal valid style needs the root `<style>` in the CSL namespace with `class` and `version`, and `cs:info` containing `<title/>`, `<id/>`, and `<updated/>` (dependent styles also need `<title-short/>` and a `rel="independent-parent"` link).

### The CSL test suite

Behavioral correctness (not just structure) is tested with the **`citation-style-language/test-suite`**. Fixtures are human-readable files divided by section markers:

- A section opens with `>>===== NAME =====>>` and closes with `<<===== NAME =====<<`. Text outside markers is ignored; sections may appear in any order.
- **Required sections:** `MODE`, `CSL`, `INPUT`, `RESULT`.
- **Optional sections:** `BIBENTRIES`, `BIBSECTION`, `CITATION-ITEMS`, `CITATIONS`.

**Section semantics:**

- `MODE` is exactly `citation` or `bibliography`.
- `INPUT` is a JSON array of CSL-JSON item objects, e.g. `{"id":"ITEM-1","type":"book","author":[{"family":"Smith","given":"John"}],"issued":{"date-parts":[[2020]]}}`.
- `CSL` is a full `<style>` (with `info` containing `<id/>`, `<title/>`, `<updated/>`).
- `CITATION-ITEMS` is an array of cite arrays, e.g. `[[{"id":"ITEM-1","label":"page","locator":"5"}]]`; each cite object has at least `id` and may add `label`/`locator` — this is exactly the mechanism the §4 locator example renders, and the `CITATIONS` section can additionally mimic word-processor insert/edit interactions (with `citationID`) to exercise the `position` tests from §6.
- In `bibliography` mode the expected `RESULT` is wrapped:
  ```html
  <div class="csl-bib-body"><div class="csl-entry">...</div></div>
  ```

**`processor.py`** (in the test-suite root) prepares tests:

- `--grind` converts human-readable fixtures into machine-friendly JSON.
- `--cranky` validates the fixture's style code against the CSL schema (via jing).
- `--verbose` prints test names; `--help` lists options.

Ground JSON tests are then run against a processor (e.g. `citeproc-js`) via processor-specific runners.

### "Why does my output look wrong?" — common rendering bugs

Validation catches structural and macro errors, but the frequent authoring bugs produce *valid* styles with *wrong output*. These do not show up in the validator — only in the Zotero preview:

| Symptom | Likely cause | Fix |
|---|---|---|
| **Doubled or dangling punctuation** (`.,` or a stray trailing `;`) | A manual `suffix="."` on a child *and* a delimiter/suffix on the parent both fire; or a hard-coded prefix leaks when the neighbor is empty | Move separators to the parent `cs:group`/`cs:layout` `delimiter`; let affixes render only with output. Never hand-punctuate between two variables — group them. |
| **Punctuation on an empty field** (e.g. a lone "`, .`" where a title should be) | Prefix/suffix placed on the wrong element, or literal text used where a variable was expected | Affixes render only when *their own* element produces output; put the connecting punctuation on the enclosing group so it self-suppresses. |
| **Non-English title mangled by `text-case="title"`** | Title-casing applied unconditionally capitalizes words in French/German/etc. | Apply `text-case="title"` only for English content (branch on the item/locale), or use `capitalize-first`; the processor only skips title-casing when the title's language is tagged. |
| **Wrong particle/suffix placement** (e.g. "Gogh, van" vs "van Gogh", or a lost "Jr.") | `non-dropping-particle` vs `dropping-particle` handled by the wrong sort/display rule | Set `demote-non-dropping-particle` appropriately and check `name-as-sort-order`; remember non-dropping particles stay with the surname, dropping ones are dropped in short form. |
| **A whole clause vanishes** or a literal separator never appears | `cs:group` self-suppression: a group is dropped only when it renders **no variable** output; a group whose only content is `value=`/`term=` text is *not* suppressed and can leak | Ensure conditional punctuation lives in a group that contains at least one *variable*; don't rely on a text-only group to disappear. |

### Interactive debugging

Zotero's Style Editor is the fastest debugging loop: its live two-pane preview re-renders against selected library items as you type and flags invalid XML immediately. Keep a small test library with the edge cases from §11 (single author, 3+ authors, no-date, editor-only, duplicate author/year, an item with a locator) selected while editing.

---

## 9. Locales, Terms & Bilingual Styles

### The locale model

Localization data — translated terms, date formats, and grammar rules — lives in standalone **locale files**, not in each style. The official repo `citation-style-language/locales` covers ~52 languages, all released under **Creative Commons Attribution-ShareAlike 3.0 Unported** (attribution to the CSL project with a link to CitationStyles.org; keep translator listings intact).

**Naming:** `locales-xx-XX.xml` (e.g. `locales-en-US.xml`, `locales-en-GB.xml`, `locales-fr-FR.xml`). The code in the filename must match the file's `xml:lang`.

**Root element:**

```xml
<locale xmlns="http://purl.org/net/xbiblio/csl" version="1.0" xml:lang="en-US">
```

**Internal structure (four sections):**

1. `cs:info` — metadata (translator name/email/uri, `rights` with `license`, `updated`).
2. `cs:style-options` — grammar rules.
3. Two `cs:date` blocks — `form="text"` and `form="numeric"`, each with `cs:date-part` children (these are the localized layouts that §2's `<date form="text"/>` reuses).
4. `cs:terms` — all `cs:term` translations.

`cs:style-options` attributes include `punctuation-in-quote` (whether commas/periods go inside closing quotes, `true` in en-US) and `limit-day-ordinals-to-day-1` (used by e.g. French).

### Declaring and overriding a locale in a style

- **`default-locale`** on `cs:style` sets the style's default language (e.g. `default-locale="fr-FR"`). A calling application (a word processor's document language) can override it at runtime.
- **In-style `cs:locale`** redefines or supplements locale-file data. It carries an optional `xml:lang` (omit it to apply to all locales) and may contain `cs:style-options`, `cs:date`, and `cs:terms`.

### Fallback / prioritization cascade

For a chosen locale (example `de-AT`), each localizable unit resolves in strict order, stopping at the first match:

1. In-style `cs:locale` with `xml:lang` = chosen dialect (`de-AT`).
2. In-style `cs:locale` with `xml:lang` = matching language (`de`).
3. In-style `cs:locale` with no `xml:lang`.
4. Locale file for the chosen dialect (`de-AT`).
5. Locale file for the matching **primary** dialect (`de-DE`) — only for secondary dialects.
6. Locale file for **`en-US`** — the ultimate fallback.

**Primary → secondary dialect pairs** include: `de-DE`→`de-AT`/`de-CH`; `en-US`→`en-GB`; `es-ES`→`es-CL`/`es-MX`; `fr-FR`→`fr-CA`; `pt-PT`→`pt-BR`; `zh-CN`→`zh-TW`. A bare language code (e.g. `de`) uses the primary dialect.

### Terms

`cs:terms` wraps translations; each `cs:term` has a `name` from the spec's term list (roles, labels, months `month-01`…`month-12`, seasons, ordinals, punctuation/misc):

```xml
<terms>
  <term name="et-al">et al.</term>
  <term name="page">
    <single>page</single>
    <multiple>pages</multiple>
  </term>
  <term name="editortranslator" form="short">
    <single>ed. &amp; trans.</single>
    <multiple>eds. &amp; trans.</multiple>
  </term>
</terms>
```

- **Singular/plural:** child `cs:single`/`cs:multiple` instead of text content.
- **`form` values:** `long` (default), `short`, `verb`, `verb-short`, `symbol`. Fallback chains: `verb-short → verb → long`; `symbol → short → long`; `short → long`.
- **Ordinals:** `ordinal` (default), `ordinal-00`…`ordinal-99` for specific numbers, `long-ordinal-01`…`long-ordinal-10` for spelled-out forms. An optional `match` attribute (`whole-number`, `last-digit`, `last-two-digits`) scopes which numbers a specific ordinal term applies to.
- **Gender:** a `cs:term` may carry `gender="feminine|masculine"` to declare a noun's grammatical gender; ordinal terms carry `gender-form` to supply gender-matched suffixes. When `cs:number` renders ordinals, the processor picks the matching gender-form (falling back to neuter). French yields e.g. "1re éd.", "1er janvier", "3e édition".

### Bilingual / multilingual styles (CSL-M)

**True per-item multilingual rendering is NOT part of core CSL 1.0.1/1.0.2.** The standard `default-locale`/document-language mechanism only switches the language of terms and dates **globally**, not per reference — a core CSL style cannot emit two-language bibliography entries for a single item.

The feature that does this is a `locale` attribute on `cs:layout`, allowing multiple locale-filtered layout blocks — but it belongs to the **MLZ / Juris-M CSL-M** extended schema, supported by some processors including Zotero/`citeproc-js`, *not* the official schema validated in §8. A minimal CSL-M pattern looks like:

```xml
<!-- CSL-M extension only — will FAIL official schema validation -->
<citation>
  <layout locale="en" prefix="(" suffix=")">
    <text macro="author-year"/>          <!-- English cites -->
  </layout>
  <layout locale="zh" prefix="（" suffix="）">
    <text macro="author-year-zh"/>       <!-- Chinese cites -->
  </layout>
</citation>
```

CSL-M also adds the machinery Juris-M/legal users actually need and that core CSL lacks: a **`jurisdiction`** variable and `legal_case`/`legislation`/`bill`/`regulation` handling, plus **institutional-name** support — personal-name fields carrying an institution are rendered with CSL-M's `<institution/>` sub-element of `cs:names` (with its own `institution-parts`/abbreviation controls) rather than as a personal name. **Scope note:** none of this validates against the official CSL schema, so if you are contributing to the official repo (§10) you must stay within core CSL; genuine mixed-language or legal-jurisdiction bibliographies require the **Juris-M (Multilingual Zotero)** fork, whose styles repository maintains working multilingual examples.

---

## 10. Contributing to the Official `citation-style-language/styles` Repo

The official repo (`github.com/citation-style-language/styles`) is the single source that feeds Zotero, Mendeley, Papers, and others.

### Acceptance criteria

1. **Sufficient quality** meeting the official requirements (the submitter is responsible for compliance).
2. **Official basis** — the style must derive from an official style guide, journal, or publisher with an online or printed reference.
3. **Broad appeal** — "style submitters should not know all individuals who would be interested in their style." **Accepted:** journals accepting unsolicited submissions, their publishers, and styles based on published guides from professional organizations, universities, or departments. **Rejected:** custom styles for personal/internal use and styles designed only for internal API implementation.

### Required metadata

- **`<id>` and `<link rel="self"/>`** must **both** equal `http://www.zotero.org/styles/[filename-without-extension]`. E.g. for `chicago-notes.csl`, both are `http://www.zotero.org/styles/chicago-notes`.
- **`<rights>`** must contain exactly: *"This work is licensed under a Creative Commons Attribution-ShareAlike 3.0 License"* with `license="http://creativecommons.org/licenses/by-sa/3.0/"`. No hard line breaks. All styles are released under CC BY-SA 3.0; preserve existing author/contributor listings when deriving.
- **`<updated>`** — an ISO/W3C datetime with timezone (e.g. `2014-06-16T12:00:00+00:00`).
- **`<title>`** — the official manual, journal, or publisher name; variants in parentheses (e.g. "MHRA Style Guide 4th edition (author-date)"); non-English styles include the language in parentheses; diacritics preserved. **`<title-short>`** holds abbreviations/search terms.
- **`<category>`** — `citation-format` (numeric/author-date/note) plus `field` (e.g. `science`, `history`). Derivative styles must remove `generic-base` from categories.
- **`<issn>`/`<eissn>`** — required for journal styles when available.
- **`<author>`/`<contributor>`** — a `<name>` child and optional `<uri>` (e.g. ORCID).
- **`default-locale`** on `<style>` when appropriate (e.g. `en-GB`, `de-DE`, `pt-BR`).

### Link relations

| `rel` | Requirement | Points to |
|---|---|---|
| `self` | required | the Zotero styles URL |
| `documentation` | required for independent styles | the style guide / a locatable reference |
| `template` | required when deriving from an existing style | `http://www.zotero.org/styles/[original]` |
| `independent-parent` | required in dependent styles | the parent independent style's URI |

### Naming rules

Allowed characters: **`a-z`, `0-9`, hyphens only**. Transformations: lowercase capitals; convert `&` to `and`; replace spaces and apostrophes with hyphens; drop diacritics without substitution; remove parenthetical text (except disambiguating info); add `.csl`. Example: *"Documents d'archéologie française"* → `documents-d-archeologie-francaise.csl`.

### Structural convention

**XML indentation must be 2 spaces per nesting level.**

### Dependent styles

A dependent style is an alias used when multiple publications share one format. It contains **`cs:info` metadata only** (no formatting) and **must** include `<link rel="independent-parent" href="[parent URI]"/>`. Place it in the **`/dependent`** subdirectory. Real examples: `scientific-reports.csl` → parent `http://www.zotero.org/styles/nature-brackets`; `ams-review.csl` → parent `http://www.zotero.org/styles/springer-socpsych-author-date`.

Example dependent style:

```xml
<?xml version="1.0" encoding="utf-8"?>
<style xmlns="http://purl.org/net/xbiblio/csl" version="1.0"
       default-locale="en-US">
  <info>
    <title>Scientific Reports</title>
    <id>http://www.zotero.org/styles/scientific-reports</id>
    <link href="http://www.zotero.org/styles/scientific-reports" rel="self"/>
    <link href="http://www.zotero.org/styles/nature-brackets" rel="independent-parent"/>
    <category citation-format="numeric"/>
    <category field="science"/>
    <eissn>2045-2322</eissn>
    <updated>2014-06-16T12:00:00+00:00</updated>
    <rights license="http://creativecommons.org/licenses/by-sa/3.0/">
      This work is licensed under a Creative Commons Attribution-ShareAlike 3.0 License</rights>
  </info>
</style>
```

### Submission / PR + validation process

1. **Validate** at `https://validator.citationstyles.org/` — passing schema validation is a hard requirement.
2. **Open a pull request** (not email):
   - New style: on GitHub, *Create new file* (for a dependent style, first navigate into `/dependent`), name it `something.csl`, paste the code, *Propose new file*, *Create pull request*.
   - Edit an existing style: open the file, click the pencil, edit, *Propose file change*, create the PR with a description.
3. **Automated testing** by **`@csl-bot`** begins within minutes of opening the PR.
4. **Human review** — volunteer reviewers usually respond within a week or two.
5. **If changes are requested, update the SAME PR** rather than opening a new one.

*Repo mechanics:* development happens on `master`; the repo maintains version branches (e.g. `v1.0.2`) that only receive updates from `master` when all `master` tests pass.

---

## 11. Step-by-Step "Author a New Style" Checklist

1. **Confirm the style qualifies** — it must derive from an official guide/journal/publisher and have broad appeal (skip the official repo if it is for personal/internal use only).
2. **Gather the exact rules** — collect real sample citations and bibliography entries from the target journal's or manual's instructions.
3. **Classify the style** — author-date, numeric, or note? This fixes `class` (`in-text` vs `note`) and which of §4/§5/§6 you follow.
4. **Pick a base style** — search the Visual CSL Editor (by name or by example) or the `master` branch of the `styles` repo for the closest existing style; save it locally.
5. **Decide independent vs dependent** — if an existing independent parent already matches the format, write a small **dependent** style pointing to it via `rel="independent-parent"` and stop after step 9.
6. **Set identity metadata** — for a repo submission, set `<id>` and `<link rel="self"/>` to `http://www.zotero.org/styles/[filename]`; add `<title>`, `<title-short>`, `<category>`, `<updated>`, the exact CC BY-SA 3.0 `<rights>` block, `documentation`/`template` links, and (for journals) ISSN/eISSN. For a private style, just use a unique `<id>` and a distinct `<title>`.
7. **Build macros** — author, editor (with `<substitute>`), issued-year (with an `n.d.` fallback via `cs:choose`), title, container, publisher, and a locator group. Branch on `type` (§3) for books vs chapters vs articles vs web pages.
8. **Write `cs:citation`:**
   - *Author-date:* et-al thresholds, disambiguation (`disambiguate-add-givenname`, `disambiguate-add-year-suffix`), grouping/collapsing, a `cs:sort`, and a `cs:layout` with `prefix="("`/`suffix=")"` plus a suppressible locator group.
   - *Numeric:* render `citation-number`, set bracket affixes, `collapse="citation-number"`.
   - *Note:* a `cs:choose` on `position` (`ibid` → `ibid-with-locator` → `subsequent` → full), with `near-note-distance` set.
9. **Write `cs:bibliography`** — add `cs:sort` (author → year → title, or citation order for numeric), `hanging-indent` / `second-field-align` as needed, and assemble the entry in `cs:layout`.
10. **Add locale/terms overrides** only if the target format needs term wording (or an "Ibid." form) different from the shared locale files.
11. **Validate the structure** — run jing (or `validator.citationstyles.org`) against `csl.rnc`.
12. **Validate the macros** — run a second pass against `csl.sch` to catch undefined macros that RELAX NG misses.
13. **Preview in Zotero** — install via the Cite pane "+", select representative library items (single author, three-plus authors, no-date, editor-only, duplicate author/year, an item cited with a page locator), and check the live rendering in the Style Editor and Style Preview.
14. **Test edge cases** — et-al truncation, given-name and year-suffix disambiguation, sort order, subsequent-author substitution, locator/label output, and (for note styles) the ibid/subsequent position sequence.
15. **Scan for rendering bugs** — walk the §8 "why does my output look wrong" table: doubled punctuation, title-case on non-English titles, particle handling, empty-group leaks.
16. **(Optional) Write test-suite fixtures** — `MODE`/`CSL`/`INPUT`/`RESULT` sections; grind with `processor.py --grind` and validate with `--cranky`.
17. **Ensure repo compliance** — 2-space indentation, lowercase-hyphen filename, exact rights text, correct link relations, core-CSL only (no CSL-M), dependent styles in `/dependent`.
18. **Open the PR** — paste/commit the `.csl`, let `@csl-bot` run, and iterate on the **same** PR until reviewers approve.

---

## Sources

- Primer — An Introduction to CSL (citationstyles.org): https://docs.citationstyles.org/en/stable/primer.html
- CSL 1.0.2 Specification (citationstyles.org): https://docs.citationstyles.org/en/stable/specification.html
- CSL 1.0.1 Specification (for version comparison): https://docs.citationstyles.org/en/v1.0.1/specification.html
- Guide to Translating CSL Locale Files: https://docs.citationstyles.org/en/stable/translating-locale-files.html
- CSL documentation source — primer.rst (GitHub): https://raw.githubusercontent.com/citation-style-language/documentation/master/primer.rst
- CSL documentation source — specification.rst (GitHub): https://raw.githubusercontent.com/citation-style-language/documentation/master/specification.rst
- CSL documentation source — translating-locale-files.rst (GitHub): https://raw.githubusercontent.com/citation-style-language/documentation/master/translating-locale-files.rst
- Standard CSL 1.0.2 Support — citeproc-typst (DeepWiki): https://deepwiki.com/pku-typst/citeproc-typst/4.1-standard-csl-1.0.2
- citation-style-language/schema (README — schema files, jing, Schematron): https://github.com/citation-style-language/schema
- CSL schema — csl-types.rnc (item types, raw): https://raw.githubusercontent.com/citation-style-language/schema/master/csl-types.rnc
- CSL schema — csl-variables.rnc (variables, raw): https://raw.githubusercontent.com/citation-style-language/schema/master/csl-variables.rnc
- styles/STYLE_DEVELOPMENT.md — citation-style-language/styles: https://github.com/citation-style-language/styles/blob/master/STYLE_DEVELOPMENT.md
- styles/CONTRIBUTING.md — citation-style-language/styles: https://github.com/citation-style-language/styles/blob/master/CONTRIBUTING.md
- STYLE_REQUIREMENTS.md — citation-style-language/styles (raw): https://raw.githubusercontent.com/citation-style-language/styles/master/STYLE_REQUIREMENTS.md
- styles/README.md — citation-style-language/styles (raw): https://raw.githubusercontent.com/citation-style-language/styles/master/README.md
- Style Requirements — CSL styles Wiki: https://github.com/citation-style-language/styles/wiki/Style-Requirements
- scientific-reports.csl — example dependent style (raw): https://raw.githubusercontent.com/citation-style-language/styles/master/dependent/scientific-reports.csl
- test-suite citeproc-test.rst — fixture format & processor.py: https://github.com/citation-style-language/test-suite/blob/master/manual/citeproc-test.rst
- citeproc-js — Exploring the test suite: https://citeproc-js.readthedocs.io/en/draft/testing.html
- CSL-M: extensions to CSL (multilingual, jurisdiction, institution) — citeproc-js documentation: https://citeproc-js.readthedocs.io/en/latest/csl-m/
- CSL Validator (validator.citationstyles.org): https://validator.citationstyles.org/
- csl-validator.js (alternative browser validator): http://simonster.github.io/csl-validator.js/
- Tool talk: style validation — CSL Development discourse: https://discourse.citationstyles.org/t/tool-talk-style-validation/1524
- Official CSL locales repository (README): https://github.com/citation-style-language/locales
- locales-en-US.xml (CSL locales repo, raw): https://raw.githubusercontent.com/citation-style-language/locales/master/locales-en-US.xml
- CSL locales README (raw): https://raw.githubusercontent.com/citation-style-language/locales/master/README.md
- Zotero Style Editor (Reference Test Pane) | Zotero Documentation: https://www.zotero.org/support/dev/citation_styles/reference_test_pane
- Zotero Style Preview | Zotero Documentation: https://www.zotero.org/support/dev/citation_styles/preview_pane
- Editing CSL Styles - Step-by-Step Guide | Zotero Documentation: https://www.zotero.org/support/dev/citation_styles/style_editing_step-by-step
- Cite | Zotero Documentation: https://www.zotero.org/support/preferences/cite
- Citation Styles | Zotero Documentation: https://www.zotero.org/support/styles
- Localization | Zotero Documentation: https://www.zotero.org/support/dev/localization
- Visual CSL Editor: https://editor.citationstyles.org/
- Visual CSL Editor — Technion Library: https://library.technion.ac.il/csl-editor/
- Editing Citation Styles - Zotero — Research Guides at Carleton College: https://gouldguides.carleton.edu/zotero/csl
- Multiple language locales in a single CSL format — Zotero Forums: https://forums.zotero.org/discussion/24675/multiple-language-locales-in-a-single-csl-format
