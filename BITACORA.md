# Bitácora de Hogar Dorado

Chronological record of meaningful milestones. Exact dates are only recorded when known.

## 2026-10-15 — Propuesta de catálogo central y generación de biomas

- Se documentó `docs/PROPUESTA_CATALOGO_BIOMAS_Y_GENERADOR.md`, con modelo de datos sugerido, ejemplo conceptual, responsabilidades de catálogo/perfiles, generador determinista, validaciones, pruebas y posible CI mediante GitHub Actions.
- Se eligió JSON como punto de partida por ser el formato del pack y evitar una dependencia adicional de YAML; la decisión debe contrastarse con el script actual de Claude.
- Se revisó el estado del repositorio: actualmente no contiene el source pack ni el script de construcción, así que no se implementó un generador desconectado de la fuente de verdad.
- La propuesta recomienda extraer los valores reales desde v13 y el script existente; la lista de biomas y paletas del documento de investigación incluye referencias de v8 y no se debe usar sin verificación como fuente activa.
- Se definieron comprobaciones para IDs duplicados, perfiles y rutas rotas, colores inválidos, biomas preview/unverified, generación determinista, archivos obsoletos y pruebas en Creator Tools/Minecraft.
- No se modificó ningún archivo del resource pack ni se implementó código de generación. Próximo paso: revisión conjunta de Claude y Ángel antes de migrar.

## 2026-10-15 — Investigación III: compatibilidad, Creator Tools y auditoría de v12

- Se creó `docs/INVESTIGACION_III_COMPATIBILIDAD_CREATOR_TOOLS_Y_V12.md`.
- Se inspeccionó estáticamente el MCPack v12 adjunto: 97 entradas, 62 archivos client biome, 19 configuraciones de niebla, dos perfiles de color grading y cuatro mapas MER; no se encontraron archivos .texture_set.json ni local_lighting/local_lighting.json en esta copia.
- Se identificó como experimento prioritario la influencia de los colores de agua por bioma mediante biome_water_color_contribution, que falta en la configuración de agua inspeccionada.
- Se identificó un riesgo de asignación de atmósfera/iluminación por bioma: los archivos client biome de v12 no contienen los identificadores correspondientes, aunque el pack tiene perfiles globales.
- Se documentó la regla oficial de apilado PBR: Texture Sets del mismo recurso no se fusionan entre packs y sus imágenes referenciadas deben existir en el mismo pack.
- Se preparó un flujo de inspección con Minecraft Creator Tools para obtener un reporte base de v12 y luego comparar v13, conservando GitHub como fuente de continuidad.
- No se modificó ningún recurso del pack, no se ejecutó Minecraft y no se midió rendimiento. Todos los hallazgos sobre v12 deben cotejarse con v13 antes de implementar cambios.

## 2026-10-15 — Investigación II: arquitectura visual y técnicas avanzadas

- Se añadió `docs/INVESTIGACION_II_ARQUITECTURA_VISUAL_Y_TECNICAS.md` como segunda ronda de investigación independiente.
- Se distingue la emisión PBR en texturas de la iluminación local coloreada con `local_lighting.json`. En los packs revisados, BSLB y Solar incluyen este archivo; Dark Fantasy no lo contiene en esta copia inspeccionada.
- Se documentaron perfiles ambientales compartidos por bioma, cobertura de `dappled_forest` y `sulfur_caves`, valores fallback de PBR, subpacks con overrides pequeños, keyframes, SSS y cubemap.
- Se propone usar primero una prueba de luz cálida y una prueba de redstone, después revisar biomas nuevos y solo más tarde estudiar SSS/cubemap.
- Se incluyó una auditoría estática de archivos y rutas de recursos, además de una posible ruta de validación con Minecraft Creator Tools y un futuro validador automatizado.
- Los datos de los packs son observaciones del contenido de los archivos y no equivalen a pruebas de rendimiento o compatibilidad. No se ejecutó Minecraft y no se modificaron activos ni código del pack.
- Próximo paso: Claude debe comparar las propuestas con la referencia v13, verificar la versión objetivo y elegir con Ángel un experimento pequeño y reversible.

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
