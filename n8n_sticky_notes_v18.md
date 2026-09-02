# n8n sticky notes — Legal parser EN v18

Two stickies. Paste each block into its own sticky note in the canvas.

---

## Sticky 1 — next to the parser node

```markdown
## 1 · Parse structure (article/contenu) — v18

Turns Mistral OCR output into `{article, contenu}` rows.
Config knobs are in the CFG block at the top of the node.

**1. Read** — pull markdown from `pages[]` (or a raw string).
**2. Split bilingual** — EN/FR column-major docs (Canada); keeps EN.
**3. Tag headings** — US docs: save Mistral's `#`/`##` before markdown is stripped.
**4. Clean** — strip markdown, page furniture, headers/footers, repeated lines.
**5. Classify document** — primary / subsidiary / judgment / gazette / standard.
**6. Classify dialect** — UK, CA, AU, IE, IN, ZA, HK, AE, **US**.
**7. Pick family** — class + dialect → the marker table to use.
**8. Strip by dialect** —
   · CA: fold marginal notes, drop French lines
   · US: rejoin orphan `(v)`, fold split titles, drop contents runs, tag center heads
**9. Read the numbering plan** (US) — one of:
   · `N.0` top level → `1.0`, `2.0` are articles
   · depth 1 → `1.`, `2.` are articles
   · depth 2 → `21.15`, `552.001` are articles
   · markdown headings, if Mistral marked them across the doc
**10. Build blocks** — the nearest preceding title becomes the article; every
    enumeration below it (`(a)`, `(1)`, `(A)`, `(i)`, `1.1`) is content.
**11. Explode** — one row per paragraph, splitting at enumerations and at
    printed sub-numbers on consecutive lines.
**12. Emit** — `{article, contenu, doc_class, dialect, family, source}`.

Routing goes to the sheet named after `family` (switch node downstream).

⚠ `dewrap: true` for hard-wrapped pdftotext · `dewrap: false` for Mistral OCR
⚠ Two-column PDFs: use reflow, not `pdftotext -layout` (columns interleave)
```

---

## Sticky 2 — next to the Switch node

```markdown
## If a sheet comes out empty
Check `doc_class` first — a mis-class sends every row to the wrong tab.
(v18 fixed one: a section titled "Judgment of diver…" classified a whole
WAC chapter as a court judgment → 0 rows.)
```
