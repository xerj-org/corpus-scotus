# corpus-scotus

U.S. Supreme Court slip opinions, October Terms 2019–2025, as plain text —
one file per opinion part (opinion of the Court, separately filed
concurrences and dissents, statements, appendices), for retrieval /
reference-corpus use.

## Scope

Every case with a published opinion in the supremecourt.gov slip-opinion
archive for October Terms 2019 through 2025 (term 2025 = the 2025–2026
sittings published to date). Counted from the archive listing pages:

| October Term | Cases | Opinion files |
|---|---:|---:|
| 2019 | 63 | 172 |
| 2020 | 68 | 161 |
| 2021 | 66 | 168 |
| 2022 | 58 | 153 |
| 2023 | 60 | 161 |
| 2024 | 67 | 170 |
| 2025 | 67 | 173 |
| **Total** | **449** | **1,158** |

By part type: 458 opinions of the Court (incl. per curiams, decrees and
orders in original-docket cases), 302 concurrence files, 294 dissent files,
86 separate opinions whose printed marker gives no derivable type (see
below), 17 appendices, 1 statement.

## Source and download lane

**Lane used: supremecourt.gov opinion PDFs** (born-digital, text extracted
with pypdf 6.19.0), downloaded 2026-10-04.

- Per-term listing: `https://www.supremecourt.gov/opinions/slipopinion/<N>`
  (one row per case: docket, decided date, author initials, citation,
  PDF link).
- 371 cases came from individual slip-opinion PDFs
  (`/opinions/<NN>pdf/<docket>_<hash>.pdf`).
- 78 cases came from 7 bound **Preliminary Print** volumes
  (`/opinions/preliminaryprint/<VOL>US<P>PP_web.pdf#page=N`, volumes
  589–592 U.S.), split at the per-case page anchors listed on the site.

**CourtListener API was tried first and rejected, honestly:** on 2026-10-04
`api.courtlistener.com` did not resolve (NXDOMAIN at the authoritative DNS),
and the API at `https://www.courtlistener.com/api/rest/v3/` returned
HTTP 403 — "Anonymous users don't have permission to access the API."
The build policy for this corpus forbids creating an account, so the API
lane was abandoned rather than worked around.

## File format

`opinions/<term>/<docket>-<case-slug>.txt` for the opinion of the Court,
`…-<surname>-<concurring|dissenting|concurring-in-part|…|statement>.txt`
for separately filed opinions, `…-appendix.txt` for appendices.

Each file starts with a header block:

```
Dobbs v. Jackson Women’s Health Organization — No. 19-1392 — October Term 2021 — decided 6/24/22 — opinion of the Court — author Alito (listing code A)
Citation: 597 U.S. 215
Source: https://www.supremecourt.gov/opinions/21pdf/597us1r58_gebh.pdf (retrieved 2026-10-04)
Licence: US Government work / court opinion — public domain (17 U.S.C. § 105; case law not copyrightable)
```

then a blank line and the opinion text.

Rendering decisions (recorded so downstream users know what is lossy):

- **Included:** opinions of the Court (majority, per curiam, decree, order),
  separately filed concurrences / dissents / statements as their own files,
  appendices as their own files.
- **Excluded:** syllabi, Reporter's Notes, counsel listings, and volume
  front matter ("CASES ADJUDGED", PRELIMINARY PRINT covers).
- Line breaks are as extracted; hyphenation at line ends is **preserved as
  printed** ("The Con-stitution" spans two lines).
- Running heads are dropped ("Cite as: …", page-number heads, case-name
  heads), as is the "Page Proof Pending Publication" footer line.
- 86 separate-opinion files carry the suffix `-separate-opinion`: the PDF's
  printed marker names the justice but not the opinion type (common for
  "Opinion of ALITO, J." concurring blocks in the bound volumes). The raw
  marker line is kept verbatim in the file header, so nothing is lost.
- **Ligature repair.** The Reports-format fonts in the Preliminary Print
  volumes drop fi/fl/ffi ligature glyphs on text extraction, producing
  "offcers", "frst", "affrmed", "fnd", "offcial". Repaired with a
  vocabulary-guided insertion pass against the clean slip-format vocabulary
  plus a final force pass over the affected word families (1,272 token
  replacements across 533 files in the final pass alone). Verification: a
  corpus-wide scan for 40 known corrupted forms
  (`offcer|offcial|frst|affrm|fnal|fnding|efect|ofense|afrm|suficient|diferent|infuence|…`)
  returns **0 occurrences**.

## Author-code mapping

The archive listing gives author initials; mapped as follows (occurrence
counts across the 449 cases): PC = Per Curiam (66), R = Roberts (47),
T = Thomas (44), NG = Gorsuch (45), SS = Sotomayor (42), EK = Kagan (43),
BK = Kavanaugh (42), A = Alito (39), AB = Barrett (35), KJ = Jackson (22),
B = Breyer (17), G = Ginsburg (6, term 2019–2020),
D = original-docket Decree row (1: Texas v. New Mexico, No. 141, Orig.).
The mapped author appears in the header of every opinion-of-the-Court file;
separate-opinion files carry their own justice surname in the filename and
header.

## MANIFEST

`MANIFEST.tsv` — columns: `path` (file in this repo), `source_pdf` (the
exact supremecourt.gov PDF, with `#page=` anchor for the bound volumes),
`part_marker` (the running-head marker the split was made at),
`pdf_start_page` (1-based page in that PDF where the part starts).

## Licence

Public domain.

- **17 U.S.C. § 105**: "Copyright protection under this title is not
  available for any work of the United States Government." Supreme Court
  opinions are works of the U.S. Government.
  Verify: <https://www.law.cornell.edu/uscode/text/17/105>
- Court opinions are not copyrightable independently of § 105:
  *Wheaton v. Peters*, 33 U.S. (8 Pet.) 591 (1834) ("no reporter has or can
  have any copyright in the written opinions delivered by this Court").
- Source: <https://www.supremecourt.gov/opinions> (official court site;
  its opinions are released without any reuse restriction).

## Reproduction

```sh
# 1. parse the per-term listing pages /opinions/slipopinion/<N>
# 2. download each row's PDF (individual slip PDF or Preliminary Print
#    volume + #page= anchor)
# 3. split at running-head part markers (Syllabus / Opinion of the Court /
#    "<Name>, J., concurring|dissenting" / Appendix), drop syllabi,
#    Reporter's Notes and counsel pages
```

Mirror built 2026-10-04 for the XERJ corpus hub.
