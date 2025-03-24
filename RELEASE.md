# CJPS-RCSP Citation Style Release v1.0.0

## Release Description

### Overview
This is the first stable release of the Canadian Journal of Political Science (CJPS) citation style for use with reference managers supporting the Citation Style Language (CSL).

### Features
- Follows the CJPS author-date system (Chicago Manual of Style author-date variant)
- Provides correct formatting for in-text citations and bibliography entries
- Supports all major source types including books, journal articles, book chapters, and more
- Includes bilingual support (English and French-Canadian)

### Installation
1. Download the CJPS-RCSP.csl file
2. Import it into your reference manager (Zotero, Mendeley, etc.)
3. Select it as your citation style when writing papers for CJPS or following their guidelines

### License
Released under Creative Commons Attribution 4.0 International License (CC BY 4.0)

## Creating the GitHub Release

Follow these steps to create a release on GitHub:

1. Go to your GitHub repository: https://github.com/patrick-mikk/CJPS-RCSP
2. Click on the "Releases" section in the right sidebar (or click on "tags" and then "Releases")
3. Click on "Draft a new release"
4. In the "Choose a tag" dropdown, select the existing tag "v1.0.0"
5. Set the release title to "CJPS-RCSP Citation Style v1.0.0"
6. Copy and paste the release description from above into the description field
7. If desired, you can check the "Set as the latest release" option
8. Click "Publish release"

Once published, users will be able to download the CJPS-RCSP.csl file directly from the releases page, making it easier for them to use your citation style.

## Future Releases

For future releases:

1. Make your changes to the CSL file and other repository content
2. Commit and push your changes
3. Create a new tag with an incremented version number:
   ```
   git tag -a v1.1.0 -m "Description of changes"
   git push origin v1.1.0
   ```
4. Follow the same release procedure as above, selecting the new tag 