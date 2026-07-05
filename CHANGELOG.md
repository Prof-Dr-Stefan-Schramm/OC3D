# Changelog

All notable changes to OC3D are documented in this file.
The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/); versioning follows [Semantic Versioning](https://semver.org/).

## [1.0.0] — 2026-07-05

First public release (corresponds to internal development build v18.4).

### Included in this release
- 33 didactically sequenced interactive 3D modules covering a complete introductory organic chemistry (OC-I) curriculum
- 9 animated reaction mechanisms (S<sub>R</sub>, S<sub>N</sub>1, S<sub>N</sub>2, E1, E1cb, E2, A<sub>E</sub>, S<sub>E</sub>Ar, A<sub>N</sub>) with synchronized 3D morphing, 2D electron-pushing diagrams, and energy profiles
- Conformational analysis (Newman/energy coupling, cyclohexane ring inversion, Newman–sawhorse–Fischer projection module)
- Stereochemistry suite (enantiomer comparison, live CIP R/S assignment from 3D coordinates, cis/trans and E/Z)
- Electronic-structure modules (hybridization, σ/π, ±I/±M, resonance, ion stability, pK<sub>a</sub> ladder, leaving groups)
- Reactivity tools (interactive S<sub>N</sub>/E decision tree, Hückel aromaticity explorer, directing effects, addition/S<sub>E</sub>Ar/carbonyl portfolios)
- Full trilingual interface: German (source language), English, French — 1,210 translation entries per language, key-for-key parity validated
- Subscript notation for mechanism labels (S<sub>N</sub>, A<sub>E</sub>, S<sub>E</sub>Ar) throughout the UI

### Pre-release development (internal builds v1–v18.4, not publicly released)
- v1–v12: initial prototypes, testing basic functions
- v12–v14: module build-out, mechanism animation engine, energy-profile synchronization, quiz elements
- v15–v17: English translation layer and inverse-dictionary language switching; geometry validation across all 37 3D scene definitions
- v18.0–v18.4: French translation (1,210 entries), trilingual parity validation, subscript notation, Unicode edge-case fixes in the dictionary lookup (non-breaking space, soft hyphen, thin space), 18 new translation keys, expanded automated validation pipeline (syntax, structural, geometric, i18n coverage/parity, headless smoke and regression tests)
