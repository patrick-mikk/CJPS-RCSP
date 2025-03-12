# Canadian Journal of Political Science Citation Style

This repository contains a Citation Style Language (CSL) file for the Canadian Journal of Political Science (CJPS) citation style. The style follows the author-date system (Chicago Manual of Style author-date variant) as required by the CJPS.

## Table of Contents

- [Installation](#installation)
- [Citation Style Overview](#citation-style-overview)
- [In-text Citations](#in-text-citations)
- [Reference List Format](#reference-list-format)
- [Examples](#examples)
- [Special Formatting](#special-formatting)
- [Troubleshooting](#troubleshooting)
- [License](#license)

## Installation

### Zotero

1. Download the `CJPS-RCSP.csl` file from this repository
2. Open Zotero
3. Go to Preferences (Edit > Preferences)
4. Select the "Cite" tab
5. Click the "+" button under "Style Manager"
6. Navigate to and select the downloaded `CJPS-RCSP.csl` file
7. The style will now appear in your list of citation styles

### Mendeley

1. Download the `CJPS-RCSP.csl` file from this repository
2. Open Mendeley Desktop
3. Go to View > Citation Styles > More Styles
4. Click "Install new style"
5. Navigate to and select the downloaded `CJPS-RCSP.csl` file
6. The style will now be available in your citation styles

## Citation Style Overview

The CJPS style is an author-date system with the following key features:

- In-text parenthetical citations showing author last name and year
- Page numbers following a colon after the year
- Bibliography entries in hanging indent format
- Full author names in bibliography entries
- Titles of containers (journals, books) in italics
- Article titles in quotation marks
- Bilingual support (English and French-Canadian)

## In-text Citations

### Basic Format

- Single author: `(Smith, 2020)`
- Two authors: `(Smith and Jones, 2020)`
- Three or more authors: `(Smith et al., 2020)`
- With page number: `(Smith, 2020: 45)`
- Multiple works: `(Smith, 2020; Jones, 2019)`
- Same author, different years: `(Smith, 2020, 2019)`
- Same author, same year: `(Smith, 2020a, 2020b)`

### Special Cases

- Work cited in text: `According to Smith (2020: 45), the findings suggest...`
- Institution as author: `(Ministry of Education, 2020)`
- No author: Citation uses shortened title in appropriate format
- No date: `(Smith, n.d.)`

## Reference List Format

The reference list appears at the end of the document with the heading "References" and includes only works cited in the text. Entries are formatted with a hanging indent and arranged alphabetically by author's last name.

### Format by Source Type

#### Books

```
Last Name, First Name. Year. Title in Italics. Place: Publisher.
```

#### Journal Articles

```
Last Name, First Name. Year. "Article Title." Journal Name Volume (Issue): Page Range.
```

#### Book Chapters

```
Last Name, First Name. Year. "Chapter Title." In Book Title, edited by Editor Name. Place: Publisher.
```

#### Newspaper Articles

```
Last Name, First Name. Year. "Article Title." Newspaper Name, Month Day, Page.
```

#### Conference Papers

```
Last Name, First Name. Year. "Paper Title." Paper presented at the Conference Name, Location, Date.
```

#### Government Documents

```
Government Agency. Year. Title. Place: Publisher.
```

#### Websites

```
Author/Organization. Year. "Title." URL (Access Date).
```

## Examples

### In-text Citations

- `(Cairns, 1968: 78)`
- `(Tanguay and Gagnon, 1996: 3)`
- `(Lambert et al., 1986)`
- `(Irvine, 1974; Meisel, 1967)`

### Bibliography Entries

- **Book**: 
  ```
  Archer, Keith, Roger Gibbins, Rainer Knopff and Leslie Pal. 1995. Parameters of Power: Canada's Political Institutions. Scarborough: Nelson.
  ```

- **Edited Book**: 
  ```
  Atkinson, Michael M., ed. 1993. Governing Canada: Institutions and Public Policy. Toronto: Harcourt Brace Jovanovich Canada.
  ```

- **Book Chapter**: 
  ```
  Bennett, Colin J. and Robin Bayley. 1999. "The New Public Administration of Information: Canadian Approaches to Access and Privacy." In Public Administration and Policy: Governing in Challenging Times, edited by Martin W. Westmacott and Hugh P. Mellon. Scarborough: Prentice-Hall.
  ```

- **Journal Article**: 
  ```
  Salazar, Debra J. and Donald K. Alper. 2002. "Reconciling Environmentalism and the Left: Perspectives on Democracy and Social Justice in British Columbia's Environmental Movement." Canadian Journal of Political Science 35 (3): 527-66.
  ```

- **Newspaper Article**: 
  ```
  Johnson, A. D. 1998. "Measuring Excellence." Maclean's, November 23, 30-33.
  ```

- **Website**: 
  ```
  Angus Reid Global. "Quebecers, Canadians Split on Proposed Charter of Values." http://angusreid.org/quebecers... (December 16, 2018).
  ```

## Special Formatting

### Author Names

- Use full names as they appear in the source
- For multiple authors, list all names (do not use "et al." in the bibliography)
- Names appear in normal order in citations but first author is reversed in the bibliography

### Page Numbers

- Page ranges use an en-dash: 527-66
- For ranges from 100 and up, do not repeat the first digit(s) in the second number if they are the same (e.g., "100-23" not "100-123")

### Dates

- Full years in ranges: 1980-1993 (not 1980-93)
- For specific dates: Month Day, Year (e.g., July 1, 2003)
- Decades: 1990s (not 1990's)

### Titles

- Book titles and journal names in italics
- Article titles in quotation marks
- Capitalize all major words in titles

## Troubleshooting

### Common Issues

- **Page numbers not formatting correctly**: Ensure page numbers are entered in the "Pages" field, not in "Extra" or "Notes"
- **Missing colon before page numbers**: Check for proper locator formatting in your reference manager
- **Author names not displaying correctly**: Ensure author names are properly entered in your reference database with the last name in the last name field

### Validation

If the CSL file is not working correctly, you can validate it using the [CSL Validator](https://validator.citationstyles.org/).

## License

This CSL style is released under a [Creative Commons Attribution-ShareAlike 3.0 License](http://creativecommons.org/licenses/by-sa/3.0/).

## Acknowledgments

This CSL file was created by *Patrick A. Mikkelsen* based on the *Canadian Journal of Political Science* citation requirements. 

### Repository Structure

This repository includes several resources for understanding and implementing the CJPS citation style:

1. **CJPS-RCSP.csl** - The Citation Style Language file for use with reference managers.

2. **Documentation:**
   - `CJPS-RCSP - Style Guide and Notes.md` - Comprehensive guide to CJPS citation style and formatting
   - `CJPS-RCSP - Instructions for Contributors.md` - Reproduction of the official instructions from the journal as markdown

3. **Reference Materials:**
   - `CJPS-RCSP - Style Quick Guide.pdf` - Quick reference guide from Memorial University Library
   - `CJPS-RCSP - Editorial Style Guidelines 2008.pdf` - Official CPSA editorial style guidelines
   - `CJPS-RCSP - Instructions for Contributors.pdf` - Guidelines for preparing materials for submission

### External Resources

1. [CJPS Instructions for Contributors](https://www.cambridge.org/core/journals/canadian-journal-of-political-science-revue-canadienne-de-science-politique/information/instructions-for-contributors-directives-aux-auteurs-es)
2. [CPSA Editorial Style Guidelines (2008)](https://www.cpsa-acsp.ca/documents/pdfs/Editorial%20Style%20Guidelines%202008.pdf)
3. [CJPS Quick Guide (Memorial University Library)](https://www.library.mun.ca/media/MUNLibrary/pdf/CJPSQuickGuide.pdf)
4. [Canadian Journal of Political Science (Cambridge Core)](https://www.cambridge.org/core/journals/canadian-journal-of-political-science-revue-canadienne-de-science-politique)
5. [Editorial Manager System for CJPS](https://www.editorialmanager.com/cjps-rcsp/)

### Citation Style Language Resources

1. [Citation Style Language](https://citationstyles.org/)
2. [Citation Style Language Validator](https://validator.citationstyles.org/)
3. [Citation Style Language Documentation](https://docs.citationstyles.org/)
4. [Citation Style Language GitHub](https://github.com/citation-style-language/styles)

