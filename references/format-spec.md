# Stonko CV format specification

Extracted programmatically from the canonical file (CVStonko8_2026.xlsx). All values are exact; do not approximate.

## 1. Workbook shape

Single worksheet named `Sheet1`. All content lives in columns A–D; columns E+ are never used. Content begins at row 2.

## 2. Element styles

Everything is **Book Antiqua 14 pt** unless noted. No fills anywhere. Colors are RGB hex.

| Element | Where | Style |
|---|---|---|
| Title "CURRICULUM VITAE" | A2, merged A2:D2 | bold, centered |
| Institution subtitle | A3, merged A3:D3 | regular, centered (the person's institution, e.g. "The Johns Hopkins School of Medicine") |
| Signature rule | C7 | literal `______________________________________`, **Calibri 14** (the one non–Book Antiqua cell) |
| Date cell | D7 | formula `=TODAY()`, underline single |
| Name line | C8 | bold ("First M. Last, MD, MS, …") |
| Date label | D8 | regular, "Date of this version" |
| Section header (level 1) | column A | regular weight, valign center, **thin bottom border applied across A:D of that row** (the underline spans the row, not just the text cell) |
| Subsection header (level 2) | column B | **bold, color FF0070C0** (blue) |
| Sub-subsection header (level 3) | column B | bold, black (e.g. "Formal Teaching", "Podium") |
| Entry number | column A | bold, e.g. `1.` `2.` … (also `1a.`/`1b.` for related patents) |
| Entry text | column B, merged B:D over two rows | regular, wrap text on, halign left, valign center |
| Label/value pairs (personal info, licenses) | label in B (valign center), value in C (halign left) | regular |
| Annotation in section header row | column D | e.g. `* denotes presenter`, `*denotes equivalent authorship status` — regular, same row as the section/subsection header |

## 3. Geometry and print setup

- Column widths: **A = 3.83, B = 22.66, C = 93.66, D = 45.0** (Excel width units).
- Row heights: **22.0 pt, customHeight, every populated row** (sheet default 19.0).
- Page: portrait, **scale 57 %** (fixed scale — not fit-to-page), margins left/right ≈ **0.0084 in** (effectively zero), top **0.45 in**, bottom **0.75 in**, header 0, footer 0.
- Footer (odd, right): `Electronically signed:\n{Name, degrees}\n&D, &T` — the `&D, &T` codes stamp print date/time.
- Manual row page breaks supplement automatic pagination; place per SKILL.md Step 3.

## 4. Entry block pattern

Every numbered entry occupies exactly **two rows**:

```
row N   : A = "7."  (bold)   B:D merged over rows N..N+1, wrap, valign center — full citation text
row N+1 : (covered by the merge)
```

Spacing: entries follow one another directly (merge-block to merge-block). Leave **one blank 22 pt row** before each level-2/3 subheader and **two blank rows** before each level-1 section header. Numbering restarts at 1 within each subsection that is itself a numbered list.

## 5. Citation and content conventions

- AMA-ish author-first citations: `Author AB, Author CD. Title. Journal. Year;Vol(Iss):pages. doi:…` — no bolding of the CV owner's name, no italics.
- `*` after an author name = presenter (presentations sections) or equal contribution (publications; noted in column D of the subheader row).
- Grand rounds / talks: `Name. Title. Venue; City, ST. Month D, YYYY.`
- Code/data entries: `Name. Repo/dataset name: one-line description. Version/Year. Accessed date. URL`.
- Grants subsections use unnumbered label lines in B ("Funding: …", "Status: …", "Date awarded: …") beneath a bolded title line.
- Date cell must remain `=TODAY()`; Excel recalculates it on open.

## 6. Canonical section taxonomy (order is fixed)

1. **Personal and Demographic Information** — Business Address (multi-line in C), Telephone, E-mail, NPI, ORCID ID
2. **Education and Surgical Training** — Undergraduate / Graduate and Doctoral / Postdoctoral (rows: date range in B, institution+role in C, city/state in D)
3. **Licenses and Certifications** — Medical License / Board Certification label-value rows
4. **Refereed Publications** — "Available online:" line, then subsections: Original Research Articles (ORA) · Systematic Reviews, Meta-analyses and Other Review Articles (RA) · Case Reports, Letters, Invited Commentary, and Correspondence (CRLC) · Book Chapters · Animal Laboratory Protocols and Methods Articles (ALP, not formally refereed) · Other Published Data Sources and Code (non-refereed) · Technical Reports (TR; non-refereed) · Masters Thesis (internally refereed only) · Undergraduate Senior Thesis (not refereed)
5. **Intellectual Property** — patents, numbered `1a.`/`1b.` for related applications
6. **Research Presentations** (`* denotes presenter` in D) — Grand Rounds Talks · National and International Conference Presentations (Podium · Selected Posters and E-Posters) · Local, Regional and Invited Conference and Other Research Presentations (Selected Podium and Other Research Talks · Selected Posters)
7. **Professional, Research and Organizational Experience** — Investment Analysis and Venture Capital · Biomedical and Surgical Device Design, Contracted Research and Consultation · Major Research Experiences · Provided NIH Grant Reviewership · Ad Hoc Provided Peer-Reviewership · Professional Societies
8. **Security Clearance / Background Checks**
9. **Intramurally Obtained Research Funding** — per-grant blocks with Date awarded / Funder / Funding / Status lines
10. **Honors and Awards** — year in B, description in C
11. **Other Scholarships and Funding**
12. **Surgical Education, Teaching and Mentoring** — Formal Teaching · Formal Lectures · Animal Labs · Mock Oral Board Examiner · Curriculum Development · Mentoring (mentee name in B, description in C)
13. **Other Skills and Certifications** — prose lines in B (AI skills, computer skills, etc.)

Rename discipline-specific headers sensibly for non-surgeons (e.g. "Education and Surgical Training" → "Education and Clinical Training") only if the user approves; default is to keep headers verbatim.

## 7. Safe editing (openpyxl hazards)

**Printer settings are load-bearing — never lose them.** The workbook embeds `xl/printerSettings/printerSettings1.bin` (referenced from `<pageSetup r:id=…>`), which carries the paper size and printer configuration Excel uses to compute rows-per-page. **openpyxl silently drops this part on save.** Without it, Excel recomputes page capacity against the local default printer and every page break lands wrong, even when break positions are correct. After ANY openpyxl save, restore it by zip surgery: copy the .bin part back in, re-add its relationship in `xl/worksheets/_rels/sheet1.xml.rels`, re-attach `r:id` to `<pageSetup>` (declare `xmlns:r` inline on the element — openpyxl does not declare it on the worksheet root), and ensure `[Content_Types].xml` has the `bin` → printerSettings Default entry. When creating a NEW workbook from the template, carry the template's printerSettings part forward the same way.

openpyxl's `insert_rows`/`delete_rows` also corrupt this file's merge map (off-by-one shifts, lost anchor values). Always:

1. Snapshot all merged ranges; `unmerge_cells` every one.
2. Perform insertions/deletions bottom-up (highest row index first).
3. Re-merge every snapshot range with offsets computed from the ORIGINAL coordinates.
4. Rebuild `ws.row_breaks` the same way (openpyxl never shifts page breaks).
5. Set `row_dimensions[r].height = 22.0` on new rows; copy cell styles with `copy.copy` per attribute (font, alignment, border, fill, number_format, protection) from a sibling entry.
6. Verify after save: merge count, numbering continuity, breaks list, and that anchor values (subheaders) survived.
7. Verify the printerSettings part survived (see above) — it never does; restore it.

## 8. Pagination rules

Page capacity is set by the embedded printer settings; empirically the canonical layout fits **60 rows of 22 pt per page**. Take pagination fully manual: place a `<brk>` every ≤60 rows at a safe boundary — a blank row or the last row of a merge block, never the first row of a two-row merge, and never leaving a section/subsection header as the last content on a page. Verify by rendering to PDF and checking every boundary.
