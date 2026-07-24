# Submitting this style to the Zotero Style Repository

Zotero (and Mendeley, Papers, etc.) draw their styles from the central
**[Citation Style Language styles repository](https://github.com/citation-style-language/styles)**.
To get *Canadian Journal of Political Science/Revue canadienne de science politique*
listed in Zotero, the file in this repo must be contributed there.

The submission file is **`canadian-journal-of-political-science.csl`** (repository root).

## Validation status

The style was validated against the official CSL 1.0.2 schema — the same checks
[`@csl-bot`](https://github.com/csl-bot) runs on every pull request:

| Check | Result |
|-------|--------|
| RelaxNG schema (`csl.rnc`) | ✅ VALID |
| Schematron rules (`csl.sch`) | ✅ PASS |

You can also confirm interactively at <https://validator.citationstyles.org/>.

## Requirements checklist

Mapped to the repository's
[`STYLE_REQUIREMENTS.md`](https://github.com/citation-style-language/styles/blob/master/STYLE_REQUIREMENTS.md):

| # | Requirement | Status |
|---|-------------|--------|
| 1 | Title is the journal's full name | ✅ `Canadian Journal of Political Science/Revue canadienne de science politique` |
| 2 | Diacritics preserved in title | ✅ |
| 3 | `default-locale` set appropriately | ✅ `en-CA` (resolves to `en-US` via CSL locale fallback) |
| 4 | File name: lowercase `a–z`, digits, single hyphens, `.csl` | ✅ `canadian-journal-of-political-science.csl` |
| 5 | Style ID is `http://www.zotero.org/styles/<file-name>` | ✅ |
| 6 | `self` link equals the ID | ✅ |
| 7 | Licensed CC BY-SA 3.0 with the exact required text | ✅ |
| 8 | `template` link to the style it was derived from | ✅ → `chicago-author-date` |
| 9 | `issn` and `eissn` for journal styles | ✅ `0008-4239` / `1744-9324` |
| 10 | `documentation` link | ✅ Cambridge "Instructions for Contributors" |
| 11 | Subject `category` (no `generic-base` on a derivative) | ✅ `author-date`, `political_science` |
| 12 | Author/contributor recorded | ✅ |
| 13 | 2-space XML indentation, no tabs | ✅ |
| 14 | Validates against the CSL schema | ✅ (see above) |

## How to submit (pull request to the CSL styles repo)

Per the styles repo
[`CONTRIBUTING.md`](https://github.com/citation-style-language/styles/blob/master/CONTRIBUTING.md):

1. Sign in to GitHub and go to <https://github.com/citation-style-language/styles>.
2. Click **"Add file" → "Create new file"** (this is a new *independent* style, so
   stay in the repository root — dependent styles go in `dependent/`).
3. Name the file exactly **`canadian-journal-of-political-science.csl`**.
4. Paste the full contents of this repo's `canadian-journal-of-political-science.csl`.
5. Click **"Commit changes…"**, choose *"Create a new branch and start a pull request"*.
6. Give the PR a title like *"Add Canadian Journal of Political Science"* and submit it.
7. `@csl-bot` runs the validation checks within a few minutes. If it flags anything,
   edit the file **in the same pull request** (do not open a new one) until it passes.
8. A volunteer reviewer merges it, usually within a week or two. After merge, Zotero
   picks the style up automatically on its next repository sync.

> **Note:** Once a PR is merged it can't be reused. For later corrections, always start
> from the *latest* version of the file in the CSL repo and open a new pull request.

## Re-validating locally (optional)

```bash
# fetch the CSL 1.0.2 schema (compact RelaxNG)
base=https://raw.githubusercontent.com/citation-style-language/schema/v1.0.2/schemas/styles
for f in csl csl-choose csl-terms csl-types csl-variables csl-categories; do
  curl -sSO "$base/$f.rnc"
done
curl -sSO "$base/csl.sch"

# convert + validate with Python (lxml + rnc2rng)
pip install lxml rnc2rng
python - <<'PY'
import rnc2rng, lxml.etree as e
from lxml.isoschematron import Schematron
open('csl.rng','w').write(rnc2rng.dumps(rnc2rng.load('csl.rnc')))
doc = e.parse('canadian-journal-of-political-science.csl')
print('RelaxNG:', 'VALID' if e.RelaxNG(e.parse('csl.rng')).validate(doc) else 'INVALID')
print('Schematron:', 'PASS' if Schematron(e.parse('csl.sch')).validate(doc) else 'FAIL')
PY
```
