# CJPS-RCSP Citation Format — Deep Research

*A reference report for maintainers of a CSL (Citation Style Language) implementation of the Canadian Journal of Political Science / Revue canadienne de science politique house style.*

---

## 1. Overview

The **Canadian Journal of Political Science / Revue canadienne de science politique (CJPS-RCSP)** is published by **Cambridge University Press** on behalf of the **Canadian Political Science Association (CPSA-ACSP)** and the **Société québécoise de science politique (SQSP)**. It is a **fully bilingual (English / Canadian-French)** journal that accepts submissions in either official language.

Its citation format is an **author-date system based on the *Chicago Manual of Style* (CMOS)**, with a small number of journal-specific deviations. The essential shape:

- **Embedded parenthetical (author-date) in-text citations** — *not* footnotes for source citations.
- **Endnotes** are used only for brief substantive/explanatory notes (labelled "Endnotes"), placed *after* the text and *before* the reference list. Footnotes are never used for citations.
- An **alphabetical reference list** ("References") at the end of the article, containing **only works actually cited**.
- **Signature deviation from Chicago:** in-text citations use a **colon before the page number** — `(Tanguay and Gagnon, 1996: 3)` — with a **comma after the author name**, where standard CMOS would write `(Tanguay and Gagnon 1996, 3)`.

The authoritative rules live in two primary documents: the **CPSA "Editorial Style Guidelines" (2008)** and the current Cambridge Core **"Instructions for Contributors / Directives aux auteur·e·s."** University library guides (Memorial/MUN, St. Thomas, Saskatchewan) reproduce concrete examples. Where the CJPS documents are silent, authors default to CMOS author-date.

> **Sourcing note (revised).** Earlier drafts flagged that live fetching of the primary hosts (Cambridge, CPSA, MUN PDFs) returned HTTP 403. For this revision the **primary documents were read directly from the committed copies in the repository's `References/` folder** — the *2008 CPSA Editorial Style Guidelines* (PDF), the current *Instructions for Contributors* (PDF + Markdown), and the *MUN Style Quick Guide* (PDF). **All journal-specific examples below are therefore verbatim from those authoritative sources**, not search-engine extracts. The two previously "unverified" load-bearing claims are now resolved (see §7): the colon-before-page form is confirmed verbatim, and the issue-number/DOI question is settled with a documented split. The only residual caveat is that the live Cambridge page may carry undocumented post-print typesetting conventions.

---

## 2. In-Text / Parenthetical Citation Rules

### 2.1 Core mechanics

The parenthetical generally contains **author last name, year, and page number(s)** where a direct quotation or specific locator is involved. What appears depends on what already precedes the citation in the running text.

| Rule | CJPS form | Note |
|---|---|---|
| Basic structure | `(Cairns, 1968: 78)` | **Comma** after name, **colon** before page — the CJPS signature (CPSA 2008, verbatim). |
| Complete-work reference (no quote) | `(Cairns, 1968)` | Page numbers not required when citing a whole work. |
| Author named in running text | `According to Alan Cairns, "…" (1968: 78).` | Author omitted from the parenthesis; only year (+page) parenthesized. |

### 2.2 Author-count rules

| Authors | In-text form | Example (verbatim, CPSA 2008) |
|---|---|---|
| 1 | Last name + year | `(Cairns, 1968)` |
| 2 | Both names joined by **"and"** ("**et**" in French) | `(Tanguay and Gagnon, 1996: 3)` |
| 3 or more | First author + **"et al."** | `(Lambert et al., 1986)` — Lambert, Curtis, Brown and Kay (four **personal** authors). |

> **⚠ Threshold divergence.** CPSA 2008 states the rule as *"If a reference has **more than two authors**, the first author's last name should be followed by 'et al.'"* — i.e. **"et al." begins at three or more**. The repository CSL encodes this (`et-al-min="3"`, `et-al-use-first="1"`). The **CMOS base rule (17th/18th ed.) sets it at *four or more***. **The CJPS-specific 3+ threshold governs this style.** Note "et al." is Latin and is **not translated** in French entries.

### 2.3 Same author, same year

Append lowercase letters to the year, assigned consistently between in-text citation and reference list: `(Lambert et al., 1986a); (Lambert et al., 1986b)`. Letters track the chronological/alphabetical ordering of the matching reference entries (see §3.9).

### 2.4 Same author, multiple *different* years in one parenthesis

Separate the years with commas under a single author name (CPSA 2008, verbatim): `(Meisel, 1967, 1975)`. In a mixed list this nests inside the semicolon-separated set — e.g. `(for example, Irvine, 1974; Irvine and Gold, 1980; Johnston, 1985; Meisel, 1967, 1975)`.

### 2.5 Multiple works (different authors) in one citation

- Listed **(preferably) in alphabetical order**, separated by **semicolons**: `(Author A, 1990; Author B, 1995: 20)`.
- If the list is **representative rather than exhaustive**, preface it with **"for example"**: `(for example, Irvine, 1974; Johnston, 1985)` (a distinctive CJPS instruction).
- A **brief interpolated phrase** may sit inside the parenthesis: `(but see Lambert et al., 1986a)` (CPSA 2008, verbatim).

### 2.6 No personal author (institutional citation)

A source lacking a personal author is cited by the **sponsoring institution / issuing body** as author, and the institution's name precedes the borrowed information: `(City of St. Catharines, 1982: 2)`. *(This is a distinct rule from §2.2 — do not conflate "institutional author" with "3+ personal authors." Lambert et al. is a four-**personal**-author work; City of St. Catharines is a **no-personal-author** work.)*

### 2.7 Corporate-author abbreviation on repeat cites

Both the 2008 guide and the current Instructions require: *on first reference, spell out an institution's name followed by the abbreviation in brackets, then use the abbreviation for subsequent references.*

- First cite: `(Canadian Political Science Association [CPSA], 2019)`
- Later cites: `(CPSA, 2019)`

> **CSL limitation:** a CSL processor cannot switch author forms between first and subsequent cites. Authors must supply the "Full Name [ABBR]" as a literal for the first occurrence (or manage it manually); the CSL renders whatever is in the author field uniformly.

### 2.8 Non-page locators

CJPS uses lower case and numerals for parts/chapters; in parentheses the abbreviated form is used (CPSA 2008, verbatim): `(chap. 2)`. Combined with an author-date cite the colon applies as usual:

- `(Cairns, 1968: chap. 2)`
- `(Smith, 1990: sec. 3)`
- `(Smith, 1990: n. 14)`

The CSL `locator` macro emits the short label (`chap.`, `sec.`, `n.`) plus the locator value after the colon; only page locators drop the label.

### 2.9 Undated sources (`n.d.`) and letter suffixes

An undated work uses **`n.d.`** in place of the year: `(Smith, n.d.)`, or with a locator `(Smith, n.d.: 12)`. Two undated works by the same author disambiguate exactly as dated ones do, with the suffix appended to the term: `(Smith, n.d.-a; Smith, n.d.-b)`. In French the term is **`s.d.`** (`(Smith, s.d.)`).

---

## 3. Reference-List Formatting by Source Type

**General mechanics.** The year immediately follows the author name(s); entries are alphabetical by first author's surname (treat *Mc* as *Mac*; file *De/de la/Von* under D/V); hanging indent. **Only the first author is inverted** (Last, First); subsequent authors are First Last, joined with "and" (no ampersand); **all authors are spelled out** ("et al." is in-text only). Generational suffixes follow the given name and a comma (`Rockefeller, John D., III, …`). Article/chapter titles take **quotation marks**; book/journal titles are **italic, title case**. All examples below are **verbatim from the 2008 guidelines and/or the current Instructions** unless flagged.

### 3.1 Journal article

```
Salazar, Debra J. and Donald K. Alper. 2002. "Reconciling Environmentalism
and the Left: Perspectives on Democracy and Social Justice in British
Columbia's Environmental Movement." Canadian Journal of Political Science
35 (3): 527-66.
```

- Article title in quotes; journal title italic, title case; **volume number in boldface**; issue number in parentheses; then **colon + elided page range**.
- **Version note (settled):** the **current Instructions** print the volume in **bold** and **include the issue number** — `Canadian Journal of Political Science **35** (3): 527-66`. The **2008 guide** shows neither bold nor issue — `… 35: 527-66`. **Encode the current form** (bold volume, issue in parens).
- **DOI — labelled recommendation, not a documented CJPS rule.** *Neither* CJPS example (2008 or current) shows a DOI. Appending `https://doi.org/…` is a **CMOS 18th-ed. convention** the repository CSL adds when the field is present; treat it as optional and CMOS-derived, not a CJPS requirement.

### 3.2 Book (single, multiple author, edited volume, book with editor)

```
White, Graham. 2005. Cabinets and First Ministers. Vancouver: UBC Press.

Archer, Keith, Roger Gibbins, Rainer Knopff and Leslie Pal. 1995. Parameters
of Power: Canada's Political Institutions. Scarborough: Nelson.

Atkinson, Michael M., ed. 1993. Governing Canada: Institutions and Public
Policy. Toronto: Harcourt Brace Jovanovich Canada.

Locke, John. 1965. The Reasonableness of Christianity, ed. George W. Ewing.
Chicago: Regnery.
```

Format: `Last, First[, and further authors]. Year. Title in Italic Title Case. Place: Publisher.` All authors are named even where the in-text form is "Archer et al." For a **whole edited volume**, **"ed." / "eds." follows the editor name(s)**, set off by commas (`Atkinson, Michael M., ed.`; `Thomas, Melanee and Amanda Bittner, eds.`). CJPS **retains place of publication** (CMOS 18th ed. dropped the requirement; CJPS examples keep it).

### 3.3 Book chapter in an edited collection

```
Bennett, Colin J. and Robin Bayley. 1999. "The New Public Administration of
Information: Canadian Approaches to Access and Privacy." In Public
Administration and Policy: Governing in Challenging Times, ed. Martin W.
Westmacott and Hugh P. Mellon. Scarborough: Prentice-Hall.
```

Chapter title in quotes; **"In"** + book title italic; **"ed."** + editor(s). CJPS uses the abbreviation **"ed."** (not CMOS's "edited by") for the chapter editor, and does **not** require the chapter page range.

> **CSL divergence to fix:** the shipped CSL renders the chapter editor with `label form="verb"` → **"edited by"**, which contradicts the CJPS "ed." examples above. The whole-volume-editor macro correctly uses `form="short"` → "ed./eds." The chapter (`container-contributors`) macro should likewise use the short form. See §6.

### 3.4 Edited volume (whole book)

```
Thomas, Melanee and Amanda Bittner, eds. 2017. Mothers and Others: The Role
of Parenthood in Politics. Vancouver: UBC Press.
```

The designation **"ed." / "eds." follows the editor's name**, set off by commas.

### 3.5 Government & official documents (including Hansard)

```
Canada. Parliament. 1992. Report of the Special Joint Committee on a Renewed
Canada [Beaudoin-Dobbie Committee]. Ottawa: Supply and Services.

Canada. Privy Council Office. 1996. Discussion Paper on Values and Ethics in
the Public Service. Ottawa: Privy Council Office.

Alcock, Reg. 2001. Canada. House of Commons Debates. November 27, 7576.
```

Lead with the **jurisdiction** (country/province), then the **issuing department/agency**, then year, title, place, publisher (publisher may repeat the agency). Clarifying descriptors go in **square brackets**. Hansard/debates may lead with a named speaker as author (the Alcock example) or with the institutional body.

### 3.6 Thesis / dissertation

```
Barr, C. W. 2000. "Evaluations of Political Leaders in Canada, Britain and
the United States." Doctoral dissertation. York University, Toronto,
Ontario.
```

Title in quotes; degree designator ("Doctoral dissertation." / "Master's thesis."); granting institution and location. (Verbatim, CPSA 2008.)

### 3.7 Conference paper

```
Nesbitt-Larking, Paul. 1994. "The 1992 Referendum and the 1993 Federal
Election in Canada: Patterns of Protest." Paper presented at the annual
meeting of the Canadian Political Science Association, Calgary.
```

Current Instructions also show the untitled/italicized-title variant: `Marland, Alex and Christopher Dunn. 2017. Causes and Consequences of Seat Reductions in Canadian Legislatures. Paper presented at the Canadian Political Science Association Meetings, May 20 to June 1. Ryerson University, Ontario.` The locale term is **"Paper presented at the"** (en) / **"Communication présentée au"** (fr-CA).

### 3.8 Occasional / working / discussion paper

```
Panayiotis, C. Afxentiou. 1999. "Convergence across Canadian Provinces."
Discussion paper series. No. 99-03. Department of Economics. University of
Calgary.
```

Series name, series number (`No. 99-03`), and issuing unit. (Verbatim, CPSA 2008.)

### 3.9 Magazine / newspaper article

```
Johnson, A. D. 1998. "Measuring Excellence." Maclean's, November 23, 30-33.

"Spending Limits Irk Cabinet." 1997. The Globe and Mail (Toronto),
December 3, A1.
```

Publication name italic; date as `Month Day`; section/page (`A1`) at the end. An **unsigned** article files under its title, with the year after the title (second example). Place-of-publication disambiguator in parentheses (`(Toronto)`).

### 3.10 Legislation & legal sources — cases *are* reference-list items

**Decision (resolved):** CJPS-RCSP **models court cases as reference-list entries** and gives explicit templates for them; it does *not* relegate them to notes-only. Both the 2008 guide and the current Instructions carry a **"Court reports"** section. Statutes are handled the same institutional-author way; a full statutory citation, when one is needed, follows Canadian legal convention.

**Court reports (verbatim, CPSA 2008):**

```
Supreme Court of Canada. Morgentaler v. The Queen, [1976] 1 S.C.R. 616.

Dickson, C.J. Morgentaler v. The Queen, [1976] 1 S.C.R. 616, at 672.

Supreme Court of Canada. Simmons v. The Queen, (1988) 55 D.L.R. (4th) 673.

U.S. Supreme Court. Brown v. Board of Education, 347 U.S. 483 (1954).
```

Pattern: **court (or citing justice) as the lead element**, then the **case name** (party v. party), then the **neutral/reporter citation** (year, reporter volume, reporter abbreviation, first page; optional pinpoint `at 672`). In-text the case is cited by **party names + year** with enough sentence context to reach the entry: `(Operation Dismantle v. The Queen, 1985)`; `(U.S. Supreme Court, Gregg v. Georgia, 1976)`.

**Statutes:** CJPS provides no verbatim statute template; when a full citation is required, authors follow the Canadian standard (McGill Guide / *Canadian Guide to Uniform Legal Citation*): `Federal Accountability Act, Statutes of Canada 2006, c. 9`; `RSC 1985` for revised statutes; neutral citations (`2015 SCC 12`) for recent decisions.

> **CSL note:** the shipped CSL has a `legal_case` handler, but it renders *case name first* then authority/number, which does **not** match the CJPS "court-first" order above. It needs re-ordering to `authority → case name (italic) → reporter citation`. See §6.

### 3.11 Websites / online sources

CJPS instructs that online sources **approximate the conventional print formats above**, adding the access (or last-revised) date in parentheses at the end (CPSA 2008, verbatim):

```
Walker, Janice R. and Todd Taylor. 1988. The Columbia Guide to Online Style.
http://www.columbia.edu/cu/cup/cgos/idx_basic.html (April 23, 2001).

Canada Election Study. 2001.
http://www.fas.umontreal.ca/pol/ces-eec/ces.html (July 20, 2001).
```

Where an Internet source lacks an author, publisher, or print date, use the file name / site name and the date last revised or accessed. (This CJPS "date in parentheses" style differs from CMOS 18th ed., which places `Accessed …` before the URL and requires an access date **only** when no publication/revision date exists — see §7.)

### 3.12 Dataset / replication data *(best-effort — see note)*

CJPS strongly encourages depositing datasets and replication code in public repositories (e.g. Harvard Dataverse / the CJPS Dataverse) but publishes **no dataset reference template**. Following the CJPS online-source pattern and CMOS practice, a reasonable rendering is:

```
Fournier, Patrick, Fred Cutler, Stuart Soroka and Dietlind Stolle. 2015.
Canadian Election Study, 2015. Harvard Dataverse.
https://doi.org/10.7910/DVN/XXXXXX.
```

> **Flagged:** this template is **best-effort / not documented by CJPS**. Verify against a recent issue before relying on it.

### 3.13 Multiple works by one author — ordering and repeated names (worked)

CJPS orders multiple works by the same author **chronologically (ascending)**; same-year works take letter suffixes ordered to match. **CJPS repeats the author's full name in each entry** — it does **not** use the CMOS three-em-dash substitution (2008, verbatim: *"the name should be repeated each time and the list should be in chronological order"*):

```
Lambert, Ronald D., James E. Curtis, Steven D. Brown and Barry J. Kay.
  1986a. "In Search of Left/Right Beliefs in the Canadian Electorate."
  Canadian Journal of Political Science 19 (3): 541-63.
Lambert, Ronald D., James E. Curtis, Steven D. Brown and Barry J. Kay.
  1986b. "Effects of Identification with Governing Parties on Feelings of
  Political Efficacy and Trust." Canadian Journal of Political Science
  19 (4): 705-28.
```

Single-author, multi-year (chronological, name repeated):

```
Cairns, Alan C. 1968. "The Electoral System and the Party System in Canada,
  1921-1965." Canadian Journal of Political Science 1 (1): 55-80.
Cairns, Alan C. 1977. "The Governments and Societies of Canadian Federalism."
  Canadian Journal of Political Science 10 (4): 695-725.
```

> **CSL implication:** do **not** set `subsequent-author-substitute` (the 3-em-dash). The shipped CSL correctly omits it. Sort key: author surname → year (ascending) → year-suffix.

### 3.14 Page-range elision — the full rule

CJPS states only the ≥100 case explicitly: *"When citing page numbers from 100 and up, it is not necessary to repeat the first numeral in the last page … if it is the same as the first-page numeral"* (100-23, not 100-123). The behaviour across the full range, reconstructed from the rule plus the verbatim examples (`527-66`, `30-33`, `100-23`):

| Input range | CJPS output | Reason |
|---|---|---|
| 3–10 | `3-10` | Under 100 → all digits. |
| 30–33 | `30-33` | Under 100 → all digits (verbatim, 2008 magazine example). **Not** `30-3`. |
| 42–48 | `42-48` | Under 100 → all digits. **Not** `42-8`. |
| 100–123 | `100-23` | ≥100, leading digit matches → drop it (verbatim). |
| 205–211 | `205-11` | ≥100, leading digit matches → drop it. |
| 527–566 | `527-66` | ≥100, leading digit matches → drop it (verbatim, 2008 journal example). |
| 196–201 | `196-201` | ≥100 but leading digits differ (1 vs 2) → **no elision**, full range. |

The CSL value that reproduces all of these is **`page-range-format="minimal-two"`** (always keeps at least two trailing digits, so sub-100 ranges stay full while ≥100 ranges elide the matching lead). Note: `"minimal"` is too aggressive (`30-3`, `42-8`) and `"chicago"` applies different sub-100 rules; use **`minimal-two`**. (Rare 4-digit ranges may diverge slightly from CJPS's literal wording; immaterial for political-science pagination.)

---

## 4. Bilingual English / French-Canadian Conventions

CJPS-RCSP is jointly sponsored by the CPSA/ACSP and the SQSP; its Instructions for Contributors are a **parallel bilingual document** (English "Instructions for Contributors" / French "Directives aux auteur·e·s").

**What is mandated of authors (narrow):** submissions accepted in **English or French**; a 150-word abstract + up to 5 keywords, translated into the other official language on acceptance (the Journal supplies the translation, or authors may); special-issue proposals must declare the article language(s); separate English- and French-language book-review editors.

> *(The abstract/keyword and word-count details are submission mechanics, not citation rules — see the quarantined note at the end of this section. They do not affect the CSL.)*

**Critical gap (confirmed verbatim):** **No CJPS document codifies how to format French-language reference entries.** The French *Directives de style* section states only: *"Contactez les rédacteurs·trices en chef pour obtenir des instructions détaillées sur les directives de style."* There is therefore **no official CJPS rule** for French title capitalization, `dir.`, `coll.`, `s.l.`, `s.d.`, guillemets, or `et`-joined author lists. Francophone authors follow standard **Quebec/Canadian (OQLF)** convention.

**English editorial base is Canadian, not US:** style follows *The Canadian Style* (Ottawa) and *Gage Canadian Dictionary*; Canadian/British spelling is mandated (behaviour, labour, centre, organize/-ize, single-l *fulfil*, double-l *travelled/modelled/labelled*). Latin abbreviations are avoided ("for example," not "e.g."). Do not capitalize *anglophone/francophone/allophone*; retain French accents in proper nouns (*Bloc Québécois, Parti Québécois*).

**French bibliographic conventions (external authority — OQLF — applied by convention, not by CJPS mandate):**

| French term | Meaning |
|---|---|
| `dir.` / `sous la dir. de` | editor/director of a collective work |
| `coll.` | collection (series) |
| `s.l.` (*sine loco*) | place of publication unknown |
| `s.d.` (*sine die*) | date unknown |
| `et` | joins the last two authors |
| `et al.` | many authors (**not translated** — it is Latin) |

French titles use **sentence case** (capitalize only the first word and proper nouns), unlike CJPS's English title-case rule; OQLF also uses **guillemets « »** and a **spaced colon/semicolon**.

### 4.1 Worked French reference entries *(best-effort, editor-deferred)*

Because CJPS defers French formatting to its editors, the following follow **OQLF convention** and are **not** validated against a CJPS mandate. They are provided so the CSL's fr-CA output can be sanity-checked.

**Book (fr-CA):**
```
Gagnon, Alain-G. et Raffaele Iacovino. 2007. De la nation à la multination :
les rapports Québec-Canada. Montréal : Boréal.
```

**Chapter in a collective work (fr-CA):**
```
Montigny, Éric. 2011. « La discipline de parti au Québec. » Dans Les partis
politiques québécois dans la tourmente, dir. Réjean Pelletier, 45-72.
Québec : Presses de l'Université Laval.
```

**Journal article (fr-CA):**
```
Gélineau, François et Éric Bélanger. 2005. « Électoralisme et performance
économique : le cas des provinces canadiennes. » Revue canadienne de science
politique 38 (2) : 411-35.
```

> **Where the current CSL's French output diverges from OQLF (concrete gaps for maintainers):**
> - The `container-title` macro hardcodes the literal **`"In"`** (`<text value="In" …/>`), so a chapter renders **"In"** even under `fr-CA` — it should be a localizable term to produce **"Dans."**
> - The chapter editor uses `label form="verb"`, which resolves to CSL's built-in fr-CA **"édité par"**, not the OQLF **"dir."** / "sous la dir. de."
> - The `title` macro uses `quotes="true"`, yielding **straight English quotation marks**, not **guillemets « »**.
> - French **spaced punctuation** (thin space before `:` and `;`) is not applied.
>
> These mean French rendering is currently **only partially localized**; because CJPS itself defers French style, this is acceptable as best-effort, but the hardcoded "In" is a genuine bug worth fixing.

**Repository CSL `fr-CA` locale mapping** (terms the .csl renders when the French locale is active):

| Term | en-CA | fr-CA |
|---|---|---|
| editor (short, sing./pl.) | ed. / eds. | éd. / éds. |
| translator | trans. | trad. |
| "and" | and | et |
| et-al | et al. | et al. |
| no date (short) | n.d. | s.d. |
| accessed | accessed | consulté le |
| presented at | Paper presented at the | Communication présentée au |

The default locale is `en-CA`; the et-al mechanics (`et-al-min="3"`, `et-al-use-first="1"`) are language-independent.

> **Quarantined (non-citation, non-CSL):** submission limits are *manuscript* rules, not citation rules — noted here only to prevent confusion. **Word limit:** 2008 guide = 8,000 words; current Instructions = **10,000** (research articles), 6,000 (research notes), 2,000 (currents / dataset / replication). **Abstract:** 150 words + ≤5 keywords, translated on acceptance (2008 required only a translated abstract, no keywords). None of this affects the CSL.

---

## 5. Legislation & Legal-Source Citation Specifics

1. **No dedicated legal apparatus.** No table of authorities, no McGill-Guide block built into the journal style; everything folds into author-date.
2. **Endnotes, never footnotes.** Substantive statutory/legal discussion (beyond an author-date pointer) goes in **endnotes**; the reference list follows the endnotes.
3. **Court cases are reference-list items** (see §3.10) — this is settled, with verbatim CJPS templates. In-text they are cited by **party names + year** with enough context to reach the entry: `(Operation Dismantle v. The Queen, 1985)`.
4. **Institutional-author treatment.** Government bodies (Parliament, departments, Statistics Canada, royal commissions, municipalities) act as the "author": `(City of St. Catharines, 1982: 2)`.
5. **Colon-before-page** applies to legal cites too: `(City of St. Catharines, 1982: 2)`.
6. **Court-report reference format:** court (or citing justice) → case name (party v. party) → year/reporter volume/reporter abbreviation/first page (+ optional pinpoint `at 672`).
7. **Full statute form** (when needed): McGill Guide — `Federal Accountability Act, Statutes of Canada 2006, c. 9`; `RSC 1985`; neutral citations (`2015 SCC 12`). CJPS does not itself mandate this form.
8. **Parliamentary/Hansard sources** follow a jurisdiction-body-title (or named-speaker) pattern: `Alcock, Reg. 2001. Canada. House of Commons Debates. November 27, 7576.`

---

## 6. Implications for the CSL File — Rules Checklist

Items marked **[CJPS-specific]** diverge from a stock Chicago author-date CSL; items marked **[FIX]** are places where the *current shipped CSL* does not yet match the CJPS examples above.

**In-text / citation layer**
- [ ] **[CJPS-specific] Page affix uses a colon, not a comma:** render `(Author, YEAR: PAGE)` — comma delimiter between name and year *and* a colon prefix on the locator (reverse of stock Chicago). *(Present in shipped CSL.)*
- [ ] **[CJPS-specific] et-al threshold at 3+:** `et-al-min="3"`, `et-al-use-first="1"` for citations (do **not** use CMOS's 4). *(Present.)*
- [ ] Two authors joined by "and"; three+ collapse to first + "et al."
- [ ] Multiple different-author cites in one parenthesis: **semicolon delimiter, alphabetical**; multiple years for one author collapse under the name (`Meisel, 1967, 1975`) via `collapse="year"`. *(Present.)*
- [ ] Suppress author when named in text; omit page for whole-work references.
- [ ] Year disambiguation with **a/b/c** (and **n.d.-a/-b**) suffixes (`disambiguate-add-year-suffix="true"`), consistent between cite and list. *(Present.)*
- [ ] Non-page locators emit the short label after the colon (`chap.`, `sec.`, `n.`). *(Present via `locator` macro.)*

**Reference-list layer**
- [ ] Only the first author inverted (`name-as-sort-order="first"`); all authors listed (`et-al-min="99"`), joined with "and." *(Present.)*
- [ ] Year renders immediately after author(s). Sort: surname → year (ascending) → year-suffix. *(Present.)*
- [ ] **Do NOT use the three-em-dash** — CJPS **repeats** the author name each time. Ensure `subsequent-author-substitute` is unset. *(Correct in shipped CSL.)*
- [ ] Titles: article/chapter in quotes; book/journal italic; English title case.
- [ ] **[FIX] Journal volume in boldface** — add `font-weight="bold"` to the `volume` variable; the shipped CSL currently renders it plain. Format: `**Journal Title** **Volume** (Issue): page-range`, colon before pages, no `pp.`
- [ ] DOI as `https://doi.org/…` — **optional/CMOS-only**, not shown in CJPS examples; keep as graceful add-on when the field exists.
- [ ] **[FIX] [CJPS-specific] Chapter editor label = "ed." not "edited by"** — change `container-contributors` from `label form="verb"` to `form="short"` to match `In …, ed. Editor`. (Whole-volume editor already uses `form="short"` → "ed./eds." ✓)
- [ ] `In` before the edited-book title; **[FIX]** make it a localizable term (currently a hardcoded literal, blocking French "Dans").
- [ ] **[FIX] Page-range elision:** add **`page-range-format="minimal-two"`** to the `<style>` element — the shipped CSL sets no format, so `100-123`/`527-566` print unelided. `minimal-two` yields `100-23`, `527-66`, while keeping `30-33`, `196-201` full.
- [ ] Government/official docs: jurisdiction as leading author element, issuing body as sub-element.
- [ ] Thesis/dissertation, conference paper, working-paper, magazine/newspaper item types each need a layout branch (templates in §3.6–3.9); the shipped CSL covers most but confirm thesis (`Doctoral dissertation.`) and working-paper (`Discussion paper series. No. …`) rendering.
- [ ] **[FIX] Legal case order:** render `Authority (court) → Case name (italic) → reporter citation` — the current `legal_case` branch leads with the case name and must be re-ordered (see §3.10).
- [ ] Undated sources: `n.d.` (`s.d.` in fr-CA). Online sources: CJPS places the access date in trailing parentheses `(Month Day, Year)` — distinct from CMOS's leading `Accessed …`; confirm the `access`/`webpage` branch matches CJPS's parenthetical form.

**Locale layer**
- [ ] `default-locale="en-CA"` plus the `fr-CA` block mapping the §4 terms (`éd./éds.`, `trad.`, `and`→`et`, accessed→`consulté le`, no-date→`s.d.`, "presented at"→`Communication présentée au`). Keep `et al.` untranslated. *(Present; but see the hardcoded-"In" and editor-verb gaps flagged in §4.1.)*

---

## 7. Open Questions / Uncertainty

| # | Question | Status |
|---|---|---|
| 1 | **et-al threshold: 3+ or 4+?** | **Resolved for this style.** CPSA 2008 (verbatim: "more than two authors") + current Instructions + repo CSL = **3+**. CMOS base = 4+. Encode 3+. |
| 2 | **Colon-before-page** | **Confirmed verbatim** from the committed 2008 guidelines (`(Cairns, 1968: 78)`, `(Tanguay and Gagnon, 1996: 3)`, `(City of St. Catharines, 1982: 2)`). No longer speculative. |
| 3 | **Issue number & bold volume in journal entries** | **Resolved.** Current Instructions require **bold volume + issue in parens** (`**35** (3): 527-66`); the 2008 guide omitted both. **DOI is CMOS-only**, absent from all CJPS examples → optional. |
| 4 | **Chapter page ranges** | CJPS examples omit them; safe to omit. |
| 5 | **French reference-entry formatting** | **CJPS does not codify it** and defers to its editors (confirmed verbatim in the *Directives de style*). The §4.1 worked entries follow OQLF and are best-effort. The CSL's hardcoded "In", editor "verb" form, and straight quotes are known partial-localization gaps. |
| 6 | **Reference-list author cap** | CJPS lists **all** authors ("all authors' full names must be included"); no cap. CMOS's 10/first-7 rule is a fallback only. |
| 7 | **"ed." vs "edited by"** | **Resolved.** CJPS uses **"ed." / "eds."** throughout (verbatim). The shipped CSL still renders "edited by" for chapters — a **[FIX]** (§6). |
| 8 | **Legal sources in the reference list** | **Resolved.** Court cases **are** reference-list items with verbatim CJPS templates (§3.10); they are **not** notes-only. The CSL `legal_case` branch needs re-ordering to court-first. |
| 9 | **Dataset template** | Not documented by CJPS; §3.12 is best-effort. Verify against a recent issue before relying on it. |

---

## 8. Sources

*(Deduplicated across all six analyst reports; primary CJPS documents read directly from the repository's committed copies for this revision.)*

**Primary CJPS / CPSA / Cambridge (read verbatim from committed copies in `References/`)**
- CJPS Editorial Style Guidelines 2008 (CPSA-ACSP) — `References/CJPS-RCSP - Editorial Style Guidelines 2008.pdf` — https://www.cpsa-acsp.ca/documents/pdfs/Editorial%20Style%20Guidelines%202008.pdf
- CJPS-RCSP Instructions for Contributors / Directives aux auteur·e·s (Cambridge Core) — `References/CJPS-RCSP - Instructions for Contributors.pdf` / `.md` — https://www.cambridge.org/core/journals/canadian-journal-of-political-science-revue-canadienne-de-science-politique/information/instructions-for-contributors-directives-aux-auteurs-es
- CJPS Style Quick Guide (Memorial University Libraries) — `References/CJPS-RCSP - Style Quick Guide.pdf` — https://www.library.mun.ca/media/MUNLibrary/pdf/CJPSQuickGuide.pdf
- CJPS Author Instructions: Preparing Your Materials (Cambridge Core) — https://www.cambridge.org/core/journals/canadian-journal-of-political-science-revue-canadienne-de-science-politique/information/author-instructions/preparing-your-materials
- Canadian Journal of Political Science — journal home (Cambridge Core) — https://www.cambridge.org/core/journals/canadian-journal-of-political-science-revue-canadienne-de-science-politique
- Canadian Journal of Political Science (IPSA journal page) — https://www.ipsa.org/na/journal/canadian-journal-political-science-4

**Library guides (CJPS style)**
- Citing Sources in CJPS Style (St. Thomas University) — https://www.stu.ca/media/stu/site-content/current-students/academic-support/docs/CJPS-Style.pdf
- Citing — Political Studies Research Guide (University of Saskatchewan) — https://libguides.usask.ca/politicalstudies/citing

**Chicago Manual of Style (author-date base)**
- CMOS 18th ed. — Author-Date Sample Citations (official) — https://www.chicagomanualofstyle.org/tools_citationguide/citation-guide-2.html
- Chicago Author-Date System (UVM Libraries) — https://libraries.uvm.edu/research-guides/chicago-author-date
- Chicago: Author-Date, 18th (Williams College Libraries) — https://libguides.williams.edu/citing/chicago-author-date
- Basic Author-Date Rules, CMOS 18th — Cheat Sheet (NAFSA, PDF) — https://www.nafsa.org/sites/default/files/media/document/CMOS-18-Cheat-Sheet.pdf
- Chicago Style 18th ed. — What's New / Journal Articles (University of Portland) — https://libguides.up.edu/chicago/whats_new
- Chapter in an Edited Book — CMOS (Thompson Rivers University) — https://libguides.tru.ca/chicago/book-chapter
- Chicago Author-Date: A Complete Guide (Scribbr) — https://www.scribbr.com/chicago-style/author-date/
- Formatting a Bibliography — Chicago Style (University of Northern Colorado) — https://libguides.unco.edu/c.php?g=500182&p=11218817

**Legal / government-document citation**
- Chicago Author-Date — Legal, Public and Unpublished Materials (Purdue OWL) — https://owl.purdue.edu/owl/research_and_citation/chicago_manual_17th_edition/cmos_formatting_and_style_guide/legal_public_and_unpublished_materials.html
- Chicago Author-Date — Cite Government and Legal Documents (SMU Libraries) — https://guides.smu.edu/c.php?g=1206606&p=8837969
- Citing Canadian Government Documents: APA (SFU Library) — https://www.lib.sfu.ca/help/cite-write/citation-style-guides/apa/gov-docs-apa
- Citation of Canadian Legislation (overview, Wikipedia) — https://en.wikipedia.org/wiki/Citation_of_Canadian_legislation
- Citing Canadian Government Publications (Queen's University Library) — https://guides.library.queensu.ca/gov/thematic-guides/citation

**French / bilingual conventions**
- OQLF — Formules et abréviations en usage dans les notices bibliographiques (Vitrine linguistique) — https://vitrinelinguistique.oqlf.gouv.qc.ca/23242/la-redaction-et-la-communication/bibliographie-et-citations/bibliographie-et-notices-bibliographiques/formules-et-abreviations-en-usage-dans-les-notices-bibliographiques

**Repository implementation**
- `canadian-journal-of-political-science.csl` — CSL implementation with en-CA and fr-CA locales — https://github.com/patrick-mikk/CJPS-RCSP
- `CJPS-RCSP - Style Guide and Notes.md` — repository maintainer's consolidated style guide (committed)
