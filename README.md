# stonkoCV — a Claude skill for the Stonko-format academic CV

A [Claude skill](https://docs.claude.com) that takes **anyone's CV** — Word, PDF, plain text, or another spreadsheet — and rebuilds it in my personal CV format: a single-sheet Excel workbook set up for the **Johns Hopkins promotion committee**, with a fixed section taxonomy, Book Antiqua typography, and print geometry tuned so the whole document exports cleanly to PDF.

## Why Excel?

Unusual choice, deliberate reasons. A spreadsheet gives absolute control over layout: fixed column geometry, exact row heights, manual page breaks, and a self-updating "date of this version" cell (`=TODAY()`). Long CVs stay perfectly aligned across dozens of pages in a way that's hard to maintain in Word. The trade-off is that Excel is an uncomfortable *sharing* format — which leads to the one rule of this repo:

> **When your CV is ready to send out: print to PDF from Excel, and share the PDF — never the .xlsx.**
>
> The PDF is the deliverable. The workbook is the source file. The print setup (57 % scale, near-zero side margins, manual page breaks, electronic-signature footer with date/time stamp) is already configured so File → Print → Save as PDF produces the final document with no fiddling.

## What's in this repo

| File | Purpose |
|---|---|
| `SKILL.md` | The skill itself — instructions Claude follows to convert a CV into this format |
| `references/format-spec.md` | Every formatting constant: fonts, colors, column widths, row heights, margins, merge patterns, section order, citation conventions, and the safe row-insertion procedure |
| `assets/template.xlsx` | A styled starter workbook (title block, personal info, example sections and entries) that conversions build from |
| `stonkocv.skill` | The packaged skill (zip) — save this to your Claude account for one-step installation |

## How to use it

**If you use Claude (claude.ai, Claude Code, or Cowork):**

1. Install the skill: save `stonkocv.skill` to your Claude account (or copy this folder into your skills directory for Claude Code).
2. Attach your existing CV in any format and say **`/stonkoCV`** — or just "convert my CV to the Stonko format."
3. Claude maps your content into the canonical section order, builds the workbook from the template, places page breaks so nothing straddles a page, and names the file by the house convention: `CV{Lastname}{Month}_{Year}.xlsx`.
4. Open the result in Excel, proofread, then **print to PDF** to share.

**Without installing:** attach `SKILL.md` and `references/format-spec.md` to any Claude conversation along with your CV and ask for the conversion.

## What stays the same vs. what changes

The *shape* is invariant: section headers and order, fonts, colors, column widths, the two-row merged citation blocks, numbering style, the signature line, and the footer. Your *content* is what changes — name, degrees, address block, phone, and every entry. Sections you have no content for are omitted entirely. Discipline-specific header wording (e.g., "Education and Surgical Training") can be adapted on request.

## Provenance

Format by David P. Stonko, MD, MS (Johns Hopkins). Skill authored with Claude; the formatting spec was extracted programmatically from the live CV so the constants are exact, not approximations.

## Disclaimer

This is a personal formatting tool shared as-is. It is not an official Johns Hopkins template, and promotion packet requirements change — always check your department's current guidance.
