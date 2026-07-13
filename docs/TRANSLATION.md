# Internationalization (i18n) Architecture & Translation Guide

OC3D ships in four languages (German · English · French · Spanish). This document explains how the translation layer works and how to contribute a new language.

## 1. How it works

- **German is the source language.** All UI text exists in German directly in the markup.
- **German strings are the dictionary keys.** Two dictionaries, `TR.en` and `TR.fr`, map each German string to its translation:

  ```js
  TR.en = {"Übersicht":"Overview", "Mechanismen":"Mechanisms", ...};
  TR.fr = {"Übersicht":"Aperçu",   "Mechanismen":"Mécanismes",  ...};
  ```

- **Language switching** (`setLang()`) walks the translatable DOM nodes and replaces text via the dictionary. Switching *away* from a non-German language back-translates through an **inverse dictionary** (translation → German), then forward into the target language.
- Because lookup is **exact-match on the full string**, keys must match the markup byte-for-byte. A normalized fallback handles known invisible-character hazards (see §4).

> **Golden rule: every translation is made from the German source string (the key).** English, French, and any future language are parallel target layers of equal rank — none of them is ever the source for another. The inverse-dictionary mechanics above are purely a runtime implementation detail of language switching, not a translation workflow.

## 2. Hard rules

1. **Key-for-key parity.** Every language dictionary must contain **exactly the same keys in the same order** as `TR.en` (currently 1,233 entries). No additions, omissions, or reordering — parity is validated automatically and a mismatch is a release blocker.
2. **Translate values, never keys.** The German keys are shared infrastructure.
3. **Preserve embedded HTML.** Many values contain markup (`<b>`, `<sub>`, `<span class=...>`). Keep tags, attributes, and structure identical; translate only the human-readable text between them.
4. **Escape `</` as `<\/`** inside dictionary values (they live inside a `<script>` block).
5. **Do not change `data-mechchip` attribute identifiers** or other `data-*`/`id` values — they are program logic, not display text (display text may show S<sub>N</sub>; the identifier stays `SN`).
6. **Chemistry terminology must be reviewed by a chemist** familiar with the target language's IUPAC conventions. Machine-drafted entries must be flagged as such until reviewed.

## 3. Per-language convention sheet

Translating the German source into a new language is not string-by-string dictionary work — before starting, define a **convention sheet for the target language** and apply it consistently. To be clear: these conventions govern *how German is rendered in the target language*; the existing English or French layers are **not** the source and set no precedent for other languages. Each new language makes its own decisions in the following categories:

- **IUPAC nomenclature of the target language** — render German compound names in the target language's official nomenclature, not in loan translations.
- **Established didactic vocabulary** — use the terms actually taught in that language's classrooms, not literal translations of the German words.
- **Note-label conventions** — fixed renderings for the recurring German labels Merksatz / Hinweis / Achtung.
- **Locale typography** — decimal separator, quotation marks, spacing rules, as customary in the target language.
- **Terminology decisions with chemical significance** — made once, applied consistently across all 1,233 entries, and signed off by the domain expert before release.

**Worked example — the French convention sheet** (shown only to illustrate what such a sheet looks like): IUPAC-FR naming (éthanal, halogénoalcane, acide éthanoïque); classroom vocabulary chaise/bateau/demi-chaise, forme mésomère, hybride de résonance, groupe partant, ÉT for transition state; note labels À retenir / Remarque / Attention; decimal comma, « guillemets », non-breaking spaces before tall punctuation; and the expert-approved decision to render German pK<sub>s</sub> as pK<sub>a</sub>, matching international usage.

## 4. Known pitfalls (learned the hard way)

- **Invisible Unicode characters break exact-match lookup.** A stray U+00A0 (non-breaking space) once corrupted the back-translation of a note; U+00AD (soft hyphen) and U+2009 (thin space) are equally dangerous. Author dictionaries in a plain-text editor; run a corruption check that scans for these code points in keys.
- **The `>` character in dictionary values** participates in a known round-trip edge case — avoid raw `>` in new values; prefer `&gt;`.
- **Copy keys from the authoritative source file**, never retype or reconstruct them from memory — a single changed character silently orphans the entry.

## 5. Adding a new language (checklist)

1. Write your convention sheet (§3) and have the domain expert approve the chemistry-significant decisions.
2. Copy the complete `TR.en` block and rename it (e.g., `TR.es`) — **purely as a structural template**, to inherit the exact key set and key order required for parity. Then replace every value with a translation **of the German key** into the target language, following §2 and §3. The English (or French) values may be consulted as an interpretation aid, but the German key is the sole authoritative source. Flag any machine-drafted entries for expert review.
3. Register the new language in the language-switcher UI and in `setLang()` (follow the existing `fr` wiring as the template).
4. Validate: key parity (1,233/1,233 against `TR.en`), no invisible-character corruption, `node --check` passes, language switching round-trips DE → new → EN → new without residue.
5. Have a chemist review all chemistry-bearing entries before release.

Spanish was added in v1.1.0 following exactly this checklist. Based on structural similarity to the existing languages, Brazilian Portuguese and Italian are the most straightforward next candidates. Contributions welcome — please open an issue first.
