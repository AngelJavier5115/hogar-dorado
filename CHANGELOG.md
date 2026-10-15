# Changelog

## 2026-10-15 — Hogar Dorado v16 personal test variants

- Added docs/INVESTIGACION_V16_PRUEBAS_EMISION.md documenting the v14/v15 comparison, two local v16 emission variants, entity Texture Sets/MERS, static validation, and an A/B gameplay test protocol.
- Generated local test-only mcpack files with fresh UUIDs and manifest version [1, 15, 0]; no vanilla binary assets or pack files were committed.
- Static checks passed: ZIP integrity, strict JSON parsing, 26 Texture Sets, no missing texture references, and no color/MER dimension mismatches.
- Gameplay visuals and iPhone performance remain untested; no final-release status is claimed.

Meaningful project changes only. Distinguish implemented work from proposals and tests.

## 2026-10-15 — Investigation IV: vanilla resource pack audit

- Added `docs/INVESTIGACION_IV_AUDITORIA_VANILLA_26_50.md` with static inventory of MCB Vanilla Resource Pack 26.50.0, Texture Set/MERS patterns, selective emission examples, biome formats, validator implications, and provenance/licensing cautions.
- Linked the report from README and updated the current state and project log.
- No vanilla assets were copied into Hogar Dorado or committed to this repository. No gameplay session or performance benchmark was performed. Findings must be compared against Claude's v13 and tested in Minecraft before implementation.

## 2026-10-15 — Biome catalog and generator design

- Added `docs/PROPUESTA_CATALOGO_BIOMAS_Y_GENERADOR.md` with a JSON source-of-truth proposal, shared profiles, deterministic generation, validation/test rules, migration phases, and optional GitHub Actions workflow.
- Updated README, current state, and project log with the design and handoff notes.
- Explicitly documented that the repository does not yet contain the source resource pack or Claude's build script; generator implementation is intentionally pending comparison against v13. No pack code/assets were changed.

## 2026-10-15 — Research III: compatibility and Creator Tools

- Added `docs/INVESTIGACION_III_COMPATIBILIDAD_CREATOR_TOOLS_Y_V12.md` with a static audit of the uploaded v12 package, current official guidance for water color contribution and per-biome visual assignments, Texture Set stacking behavior, and a proposed Inspector workflow.
- Linked the report from README and updated the current-state and project-log documents.
- The audited file was v12 only; v13 has not been independently inspected in this task. No pack assets/code were changed, no game session was run, and no benchmark is claimed.

## 2026-10-15 — Advanced research II

- Added `docs/INVESTIGACION_II_ARQUITECTURA_VISUAL_Y_TECNICAS.md` with static inspection of reference packs and research on colored local lighting, shared biome profiles, PBR fallbacks, keyframes, subsurface scattering, cubemaps, subpacks, validation tools, and proposed experiments.
- Linked the report from the README and updated the project state and bitácora for Claude handoff.
- Marked all findings as research or proposals; no gameplay changes, pack asset changes, or performance benchmarks are claimed.

## 2026-10-15 — Research documentation

- Added `docs/INVESTIGACION_TECNICA_OPTIMIZACION_PBR.md` covering PBR texture sets, MER/emission, entity-emission checks, mobile optimization, performance measurement, environment criteria, and proposed experiments.
- Cross-referenced official Microsoft Learn/Mojang documentation and the technical guides and community reference packs supplied by Ángel.
- Updated the README, current-state record, and project bitácora so Claude can find and continue the research when available.
- Marked recommendations and test plans clearly as pending; recorded that no gameplay testing or performance benchmark was performed in this documentation task.

No resource-pack code, pack assets, or gameplay behavior were changed in this documentation update.

## Unreleased — initial documentation
- Established the project's artistic and economic-experiment goals.
- Documented the early-beta status and decision to delay public release until real in-game previews are available.
- Recorded the first generated cover concept as a visual reference, not an official final logo.
- Added a shared collaboration and handoff protocol for Claude and Atlas.

No gameplay changes or compatibility tests are claimed by the initial documentation release.
