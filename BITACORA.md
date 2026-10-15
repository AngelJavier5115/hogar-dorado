## 2026-10-15 — Hogar Dorado v19: transparencia, lava +20 % y cielo índigo del End

- Ángel probó v18. Confirmó que la textura/animación de portal y lava ya estaban restauradas, pero pidió más transparencia en el portal, lava un 20 % más brillante y señaló que el cielo del End seguía mal. También advirtió que el pack había perdido su temática.
- Se volvió a fijar como criterio artístico la raíz «Una nostalgia que parece un sueño»: familiaridad cálida, atardeceres dorados, noches índigo, misterio sutil y preservación de la identidad de Minecraft.
- Se generó un solo paquete local v19. Portal: alfa ×0.80. Lava quieta y en flujo: RGB ×1.20, sin elevar emisión PBR. End: cielo #7560B8, niebla #302344, fog_start 0.90 y densidad aérea 0.0008.
- Validación estática: ZIP íntegro, 176 archivos, 119 JSON válidos, tiras de textura conservan dimensiones correctas. No se ha probado v19 en juego ni se midió rendimiento.
- Paquete: `hogar-dorado-v19-identidad-restaurada-PRUEBAS-PERSONALES.mcpack`; SHA-256 `1530bddfe295169a1b6c032437f90ddd97fc218ce9d1240f0352dbcbf9543a7a`.
- Informe: `docs/INVESTIGACION_V19_IDENTIDAD_PORTAL_LAVA_END.md`. El binario no se subió a GitHub por contener assets vanilla de referencia.

## 2026-10-14 — Hogar Dorado v18: restauración de lava/portal y contraste del End

- Ángel probó la v17 y reportó que lava y portal del Nether seguían viéndose mal y que el pack había perdido impacto visual.
- La auditoría de píxeles confirmó que las texturas base de lava quieta, lava en flujo y portal eran variantes estilizadas de franjas repetitivas, no la textura de referencia Bedrock compartida por Ángel. Sus dimensiones animadas ya eran correctas en v17; el problema dominante era el contenido visual, no la longitud de las tiras.
- Se creó una única compilación local v18. Se restauraron textures/blocks/lava_still.png, lava_flow.png y portal.png desde la referencia 26.50.0; dimensiones 16×512, 32×512 y 16×512. También se actualizaron sus Texture Sets al formato 1.21.30 de referencia y se añadieron mapas MERS de lava y portal y mapas normal de ambas texturas de lava.
- Para explorar la pérdida de impacto en el End sin alterar el mundo normal ni el Nether, se añadió gradación hogar:end con un matiz frío/índigo moderado y se redujo la densidad aérea del End de 0.005 a 0.0028, con inicio de niebla de 0.73 a 0.80.
- Validación estática: ZIP íntegro, 119 JSON válidos, 176 archivos, 27 Texture Sets con referencias completas; las tres imágenes son idénticas byte a byte a la referencia aportada y coinciden las dimensiones de color/mapas de material.
- Paquete local: hogar-dorado-v18-restaurada-PRUEBAS-PERSONALES.mcpack; SHA-256 56ad3f1c43496fc113dba063a908af61661a09b51e595aeea75d1aecbc0b236c.
- No se subió el mcpack al repositorio porque contiene assets vanilla de referencia. **Pendiente:** prueba visual de v18 en Minecraft Bedrock 26.52; no se midieron FPS, memoria ni temperatura.
- Informe: docs/INVESTIGACION_V18_RESTAURACION_LAVA_PORTAL_END.md.

## 2026-10-14 — Hogar Dorado v17: corrección de texturas y visibilidad del agua

- Ángel probó la v16 equilibrada y compartió capturas reales; señaló pérdida de detalle en texturas y poca visibilidad debajo del agua. Le gustaron los destellos/partículas de las escenas submarinas.
- Al revisar los mapas PBR se encontró emisión verde uniforme en todos los píxeles de glowstone, sea lantern y shroomlight; mapas de lava con emisión PBR no nula en toda la superficie; y rugosidad de minerales uniforme en 40.
- Se generó una única compilación local v17 equilibrada. R/B de los mapas MER de bloques toman la respuesta de material de los mapas MERS de referencia; las máscaras de minerales se mantienen con emisión reducida (76, redstone 90), los focos recuperan la máscara espacial original multiplicada por 1.8 y el canal emisivo de lava se pone en cero.
- Se redujo la densidad/absorción volumétrica del agua en los 19 perfiles de niebla para intentar mejorar la visibilidad submarina sin eliminar por completo la atmósfera acuática.
- Validación estática: ZIP íntegro, 117/117 JSON válidos, 26 Texture Sets, cero referencias de textura faltantes y cero discrepancias dimensionales de color/mapa.
- Artefacto local: `hogar-dorado-v17-equilibrada-CORREGIDA-PRUEBAS-PERSONALES.mcpack`; SHA-256 `53cf8ffe84cfdfd1300d7dbfdef1dd805446e95b9f397c9afb6d56c59d2aab41`.
- El paquete contiene assets de referencia vanilla y no se subió al repositorio. **Pendiente:** prueba visual en Bedrock 26.52 con Vibrant Visuals activo; no se han medido FPS, memoria ni temperatura en iPhone.
- Informe: docs/INVESTIGACION_V17_CORRECCION_TEXTURAS_AGUA.md.

# Bitácora de Hogar Dorado

## 2026-10-15 — Hogar Dorado v16: pruebas personales de emisión

- Se compararon localmente v14 y v15. v15 ya resuelve las referencias de sus Texture Sets de bloques y corrige las dimensiones de lava, portal y sea lantern.
- Se generaron dos archivos locales v16 de prueba personal: emisión equilibrada e intensa. Cada uno tiene UUID nuevos y versión de manifest [1, 15, 0].
- Se añadieron Texture Sets/MERS para Enderman, Blaze y Magma Cube usando las rutas de textura de la referencia vanilla; se aumentó solo el canal verde de emisión y se conservó la máscara espacial.
- Validación estática de ambas variantes: ZIP íntegro, JSON válido, 26 Texture Sets, cero referencias faltantes y cero discrepancias de dimensiones entre color y mapas.
- Las variantes contienen assets vanilla y son solo para pruebas personales; no se subieron los mcpack ni recursos binarios al repositorio.
- **Pendiente:** importar y comparar en Minecraft Bedrock 26.52, con Vibrant Visuals activo, en el mismo escenario y luego comprobar rendimiento/temperatura en iPhone 14 Plus. No se afirma que el resultado visual ni el rendimiento ya estén probados.
- Informe: docs/INVESTIGACION_V16_PRUEBAS_EMISION.md.

Chronological record of meaningful milestones. Exact dates are only recorded when known.

## 2026-10-15 — Investigación IV: auditoría de Vanilla Resource Pack 26.50.0

- Se inspeccionó estáticamente el archivo MCBVanillaResourcePack-26.50.0.zip aportado por Ángel. El ZIP contiene 18.417 archivos y 3.130 Texture Sets; no se extrajeron ni se añadieron assets al repositorio de Hogar Dorado.
- Se documentó `docs/INVESTIGACION_IV_AUDITORIA_VANILLA_26_50.md`, con inventario, patrones de Texture Sets, mapas MERS, biomas, limitaciones, licencia/procedencia y próximos experimentos.
- Hallazgo útil para validación: la textura de color puede ser PNG o TGA; una comprobación que busque solo PNG crea falsos errores.
- Hallazgo útil para diseño: el canal verde emisivo varía por recurso. La redstone vanilla examinada tiene el canal verde en cero, mientras BSLB activa un subconjunto de píxeles; los mapas de enderman y spider vanilla iluminan detalles diminutos. No usar una intensidad uniforme para todos los materiales.
- Se corrigió la suposición de que todas las criaturas necesariamente requieren un único método de emisión: el paquete vanilla contiene Texture Sets y mapas MERS de entidades; evaluar el método por recurso y versión.
- Se constató que el repositorio MCBVanillaResourcePack declara que los assets pertenecen a Mojang/Microsoft y que se trata de una plantilla. El informe guarda observaciones técnicas, no recursos binarios.
- La copia auditada corresponde a 26.50.0 y no se ejecutó dentro de Minecraft; todos los hallazgos deben contrastarse con la referencia v13 y probarse antes de implementarlos.

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
