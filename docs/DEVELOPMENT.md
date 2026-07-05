# Development Methodology

OC3D was created through an **expert-directed, AI-assisted development process**. This document describes the workflow, the division of responsibilities, and the quality-assurance pipeline. It exists both as project documentation and as supporting material for the accompanying publication.

## 1. Roles and responsibilities

| Role | Responsibility |
|---|---|
| **Domain expert (author)** | Specification of all chemical content, didactic structure, module sequence, and design decisions; review and verification of every build; final approval of all chemistry-significant changes |
| **AI assistant (Claude, Anthropic)** | Iterative implementation of the specified functionality: HTML/CSS/JavaScript, Three.js scene construction, animation logic, i18n infrastructure, validation tooling |
| **External expert reviewer** | Independent chemical review of 2D drawings and visualizations (Isabelle Navizet, theoretical chemistry) |

Two standing rules governed the collaboration:

1. **Chemistry-significant decisions always require explicit expert approval** (e.g., terminology such as pK<sub>s</sub> vs. pK<sub>a</sub> in translated content, energy values, mechanistic depictions). Purely technical decisions (refactoring, tooling) could proceed autonomously.
2. **Never ship a regression.** Every build had to pass the full validation pipeline before delivery; a hard baseline of expected results was enforced (see §3).

## 2. Iterative workflow

Development proceeded in short specification–implementation–verification cycles with explicit version tracking (internal builds v1 → v18.4). A typical cycle:

1. The expert specifies a module or change in chemical/didactic terms (target concept, correct geometry/energetics, intended interaction) and ideally also provides high quality material such as lecture slides.
2. The AI implements the change as a patch to the monolithic single-file application.
3. The full validation pipeline runs on the patched build.
4. The expert reviews the rendered result, flags chemical or language imprecision, and either approves or iterates.

All AI-authored content requiring domain judgment was **explicitly flagged as unreviewed** until checked by the expert — machine-generated chemistry text was never silently treated as verified.

## 3. Automated validation pipeline

Every build passed the following gates before release:

1. **Syntax check** — `node --check` on the extracted JavaScript.
2. **Structural validation** — a Python validator verifying the document structure (module containers, navigation entries, required UI elements).
3. **Geometry validation** — automated checks of all 37 3D scene definitions across the 33 modules (bond-length and connectivity sanity against the standard values used by the app).
4. **Translation coverage and parity** — the English and French dictionaries must be key-for-key identical in set and order (1,210/1,210 entries); missing or orphaned keys fail the build.
5. **Headless smoke and i18n tests** — the app is loaded in `jsdom` with a Three.js proxy stub; language switching is exercised programmatically and the DOM is checked for untranslated or corrupted strings. 2D canvas drawings were additionally rendered headlessly (`@napi-rs/canvas`) for visual verification before delivery.
6. **Hard regression baseline** — in the headless (non-WebGL) environment exactly 12 modules are expected to fail for WebGL-only reasons. Any deviation from this exact baseline — in either direction — blocks the release, because it indicates an unintended change.

## 4. Characteristic AI error classes caught by the pipeline

Documenting failure modes is part of the methodology's value. Recurring classes included:

- **Invisible Unicode characters** breaking exact-match dictionary lookups: a U+00A0 (non-breaking space) introduced during editing corrupted the inverse-dictionary back-translation of one note; U+00AD (soft hyphen) and U+2009 (thin space) caused similar issues. Mitigation: normalized fallback lookup plus automated corruption checks.
- **Escaping errors** in HTML-bearing dictionary values (`</` must be written `<\/` inside script-embedded JSON).
- **Silent scope creep**: patches touching more than the specified strings. Mitigation: parity counts and the hard regression baseline.
- **Plausible-but-imprecise chemical language** in machine-drafted translations. Mitigation: flagging of all AI-authored entries for expert review; chemistry-significant terminology decisions reserved for the expert.

## 5. Architectural decisions

- **Single-file monolith by design.** One HTML file is trivially distributable (email, LMS upload, USB stick), auditable, versionable, and immune to broken-dependency rot on the user side. The costs (large file, patch-based editing) are borne by the development process, not by users.
- **External runtime dependencies** are limited to Three.js r128 (cdnjs CDN) and Google Fonts. Everything else — module data, translations, styling, logic — is embedded.
- **No telemetry.** The app contains no analytics, cookies, or network calls beyond loading the two dependencies above.

## 6. Reproducibility note

The validation scripts referenced above are part of the development environment and are not yet bundled in this repository; they may be added in a future release. The methodology itself — expert specification, flagged AI drafts, automated parity/geometry/regression gates, expert sign-off — is tool-agnostic and reproducible with any capable LLM.
