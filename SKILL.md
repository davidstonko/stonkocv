---
name: stonkoCV
description: Convert anyone's CV (Word, PDF, text, or another spreadsheet) into the Stonko-format Excel CV — a single-sheet .xlsx in Book Antiqua 14 that prints cleanly to PDF, with the exact section taxonomy, headers, column geometry, merged two-row citation blocks, numbering, self-updating date cell, and electronic-signature footer of the house template. Trigger on '/stonkoCV', 'convert this CV to the Stonko format', 'make my CV look like the Stonko Excel CV', or any request to build or reformat an academic CV into this Excel layout.
---

# stonkoCV — the Stonko-format Excel CV

Converts any CV into a print-perfect Excel workbook matching David P. Stonko's CV layout. The person's *content* changes (name, address, phone, entries); the *shape* never does: same sections in the same order, same fonts, same geometry, same print behavior.

Two bundled resources do most of the work:
- `references/format-spec.md` — every formatting constant and layout rule, extracted from the canonical file. **Read it before writing any cell.**
- `assets/template.xlsx` — a working skeleton with the title block, personal-information section, section/subsection headers, and correctly styled example entries. **Start from this file; never build from a blank workbook.**

## Step 1 — Intake

Accept the source CV in any form (.docx, .pdf, .txt, .xlsx, pasted text). Extract every entry, then map it into the canonical section taxonomy listed in `format-spec.md` §6. Rules:

- Keep the canonical section ORDER even if the source CV orders things differently.
- If the person has no content for a section, OMIT the section entirely (don't leave empty headers).
- If the source has content that fits no canonical section, place it under the closest canonical header and tell the user what you decided.
- Personal info (name, degrees, address block, phone, email, NPI, ORCID) goes in the same cell positions as the template; only the values differ. The institutional subtitle under "CURRICULUM VITAE" becomes the person's own institution.
- The footer becomes "Electronically signed:\n{Their Name, degrees}\n&D, &T".

Ask the user only what cannot be inferred: institution line, whether to include NPI/ORCID if absent, and their last name for the file naming convention.

## Step 2 — Build

Work on a copy of `assets/template.xlsx` with openpyxl. Follow `format-spec.md` exactly for:

- Element styles (§2): title block, section headers with thin bottom border A:D, blue bold subheaders, numbered entries.
- Geometry (§3): column widths, 22 pt rows, near-zero side margins, 57 % print scale.
- The two-row merged entry pattern (§4): every numbered entry = merge B:D across two consecutive 22 pt rows, number in column A of the first row. One blank spacer row before each new subheader, two before each new column-A section header.
- Citation conventions (§5): AMA-style, bold nothing, asterisk convention for presenters/equal authorship noted at section top right (column D).
- The date cell stays `=TODAY()` — never a literal date.

Insert rows with the unmerge-all → insert → re-merge-with-offset pattern described in §7 — openpyxl's automatic merge shifting corrupts this file class. Never let a LibreOffice re-save touch the deliverable (it rewrites formatting internals); LibreOffice is for PDF preview only.

## Step 3 — Page breaks and print QA

1. Convert to PDF with LibreOffice (preview only) and inspect every page.
2. Place manual row page breaks so no entry block, header-plus-first-entry pair, or two-row merge straddles a page. Prefer breaking before a section or subsection header.
3. Re-render and re-inspect after setting breaks.
4. Verify: numbering continuous within each numbered list; no duplicate citations (flag epub-ahead-of-print + final-version pairs); chronological order within each subsection matches the source CV's convention; no content in columns E+.

## Step 4 — Deliver

File name follows the house convention: `CV{Lastname}{M}_{YYYY}.xlsx` where {M} is the month number without zero padding (e.g., `CVStonko8_2026.xlsx`). Deliver the .xlsx (and the preview PDF only if asked), with a short note listing: sections omitted for lack of content, placement judgment calls, and anything flagged in QA.

Always end the delivery note with this reminder, verbatim in spirit: **the Excel file is the source document — when the CV is ready to send out, open it in Excel and print/export to PDF, and share the PDF, never the .xlsx.** The print setup (scale, margins, breaks, signature footer) is pre-configured so the PDF export is the finished deliverable. This format is designed for the Johns Hopkins promotion committee; the PDF is what reviewers should receive.
