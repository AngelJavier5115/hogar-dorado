# Bitácora de Hogar Dorado

Chronological record of meaningful milestones. Exact dates are only recorded when known.

## 2026-10-15 — Investigación técnica y optimización

- Ángel pidió que los hallazgos sobre Hogar Dorado se documenten en GitHub para que Claude pueda revisarlos al regresar y valorar su implementación en la siguiente versión.
- Se creó `docs/INVESTIGACION_TECNICA_OPTIMIZACION_PBR.md`, con una lista de comprobación para texture sets, mapas MER, emisión en bloques y entidades, optimización móvil, escenas de prueba y recomendaciones para el siguiente ciclo.
- La investigación cruza la guía de texturas y el resumen de la comunidad de creadores compartidos por Ángel, las referencias BSLB Dreamy Visuals, Solar Shaders Visuals y Dark Fantasy Visuals, y documentación oficial de Microsoft Learn/Mojang.
- Prioridad sugerida: verificar primero la cadena PBR/emisión con un experimento pequeño de redstone; después probar una criatura candidata; consolidar ambiente por escenas; medir el rendimiento en el iPhone 14 Plus antes de decidir si hacen falta subpacks ligeros.
- Se clasificaron las conclusiones entre documentación oficial, observaciones de los materiales entregados e hipótesis que aún requieren prueba. No se afirma que se hayan ejecutado pruebas de gameplay ni benchmarks.
- No se modificó el código ni los archivos del pack; el cambio de este hito es documentación para continuidad entre Ángel, Claude y Atlas.

## 2026-10 — Initial project and publishing exploration

- Ángel began experimenting with Claude to create a Minecraft Bedrock resource pack with a dreamcore and nostalgia direction.
- A beta package named Hogar Dorado v12 was examined as project context. Its stated direction includes golden sunsets, indigo nights, soft water, and depth fog. The package is still experimental; this log does not certify its correctness or compatibility.
- The artistic vision was clarified: the pack should feel warm and familiar while retaining a subtle mystery, and should complement rather than overwhelm Minecraft.
- CurseForge creator/rewards setup was explored. The account console showed the account enrolled in the Rewards Program and 2FA enabled. This does not guarantee that a specific project or any downloads qualify for rewards.
- MCPEDL was already connected to the CurseForge account according to the account screen.
- The project creation form was explored but public project creation/release was intentionally paused because the pack is early beta and actual in-game preview images were not yet ready.
- A first cover-art concept was generated: large golden voxel lettering over a blocky sunset landscape with an indigo sky. It is a visual direction reference, not an in-game screenshot or approved final logo.
- Ángel created the private GitHub repository AngelJavier5115/hogar-dorado to act as the shared project record for Claude and Atlas.

## Current next steps

1. Establish the visual identity and decide whether the generated cover concept should be refined into a logo, project cover, or both.
2. Keep improving and testing the pack before publishing; capture genuine in-game screenshots when ready.
3. Maintain concise documentation of significant changes, tests, decisions, and economic hypotheses.
4. Review the technical research in `docs/INVESTIGACION_TECNICA_OPTIMIZACION_PBR.md` before planning the next beta iteration; verify which proposals remain relevant against the current pack files.
