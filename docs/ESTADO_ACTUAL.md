# Estado actual — Hogar Dorado

Este archivo es el punto de entrada para retomar el trabajo en otra conversación. Actualizarlo después de tareas significativas.

## Estado general
- **Fase:** exploración artística, investigación técnica y desarrollo temprano.
- **Producto:** resource pack para Minecraft Bedrock.
- **Versión del juego indicada por Ángel:** 26.52.
- **Dispositivo prioritario para pruebas:** iPhone 14 Plus.
- **Publicación:** no listo para lanzamiento público.
- **Repositorio:** privado, AngelJavier5115/hogar-dorado.
- **Responsable:** Ángel.

## Investigación VI — packs Bedrock, Actions & Stuff y Minecraft Creator Tools (15–16 de octubre de 2026)

- Se auditaron estáticamente los ocho archivos de packs aportados por Ángel. El archivo llamado “Vivencia” es internamente **Vibrance**; “Visuales de Fantasía Oscura 2.0 (resources) 2” es **Chroma Visuals 1.1**, con modo A&S Compatibility; el otro archivo Fantasía Oscura es **Dark Visuals**; Prizma también contiene un skin pack separado de 11 skins.
- Se documentaron inventarios, tamaños descomprimidos, subpacks, fuentes relevantes y una matriz de pruebas en [Investigación VI](INVESTIGACION_VI_PACKS_BEDROCK_COMPATIBILIDAD_ACTIONS_STUFF_MCTOOLS.md).
- Hallazgos útiles: Actions & Stuff 1.12 incluye SP0 “Custom Experience — Compatible with other texture packs”, SP1 “Vanilla Experience” y SP2 “Full Experience”. Chroma tiene un modo A&S separado e indica que debe ir encima de A&S para esa variante. Vibrance LOW se describe como iluminación con texturas vanilla para colocarse sobre otro pack. Prizma VANILLA y Dark Visuals Classic son candidatos de coexistencia antes que variantes con modelos/animaciones.
- Esto respalda una dirección de diseño, no demuestra aún el tamaño de la demanda comunitaria. No se ha validado ninguna combinación de estos packs en el juego.
- **Resultados del Inspector recibidos:** Lush Visuals reportó 683 errores (551 BOM, 103 referencias de Texture Set en `texture_list.json`, 27 rutas largas dependientes del nombre importado y 2 JSON desconocidos) y 41 avisos. Su reporte tiene 39 comprobaciones exitosas y 4 fallidas. Dos de sus avisos sobre esquema se contrastaron con documentación oficial actual y parecen discrepancias del validador; `pdr/global.json` merece revisión como posible error de ruta.
- v20-A, primer informe: Creator Tools detectó capacidad PBR, JSON/manifiesto válidos y cero dependencias faltantes; 39 comprobaciones exitosas, 1 fallida y 14 no aplicables. La única prueba fallida era PATHLENGTH en 20 rutas que incluían el nombre largo de importación. Se repitió el Inspector usando el alias corto `HD-v20A.mcpack`: resultado nuevo, 0 errores, 21 advertencias, 40 pruebas exitosas, 0 fallidas y 14 no aplicables. La ruta corta elimina el fallo de PATHLENGTH. Las 21 advertencias son 19 avisos de esquema sobre `weather` en la niebla volumétrica, uno sobre objetos `subpacks` y uno sobre keyframes de `sky.intensity`; la documentación oficial respalda esos formatos, así que no deben corregirse a ciegas.
- Se creó una copia exacta llamada `HD-v20A.mcpack` (mismo SHA-256, sin cambios de contenido). **Resultado confirmado:** Inspector con nombre corto arroja 0 errores y 0 pruebas fallidas; queda pendiente probar v20-A dentro de Minecraft Bedrock 26.52. El informe no confirma el aspecto visual de los ojos del gólem.

## Estado de experimento local v20-A — ojos del gólem

- Se generó una compilación personal, fuera del repositorio porque incluye recursos vanilla de referencia: `hogar-dorado-v20-A-golem-ojos-PRUEBAS-PERSONALES.mcpack`.
- Único cambio intencional frente a v19: dos píxeles emisivos pequeños en los iris rojos de la textura del gólem, mediante `textures/entity/iron_golem_mers.tga` y un Texture Set específico; no se cambiaron mapas de piglins, Enderman, lava ni perfiles atmosféricos.
- Versión del manifiesto: 1.20.0. UUIDs nuevos respecto a las versiones anteriores.
- **Validación estática:** ZIP íntegro, 178 archivos, 120 JSON válidos, TGA de MERS en 128×128, SHA-256 `00de46bc9fa3ca216e6e6138dc352c8e8d3b9fa349979ff79d417d7025c14a33`.
- **Inspector (15 de octubre de 2026):** con el alias de nombre corto `HD-v20A.mcpack`, 0 errores, 21 advertencias, 40 pruebas exitosas, 0 fallidas y 14 no aplicables. El único fallo del informe anterior (PATHLENGTH, 20 rutas) desapareció sin cambiar el contenido del paquete. No hay validación visual, medida de FPS, memoria, temperatura o batería. No es un cambio aceptado hasta que Ángel observe el resultado en el iPhone.
- El siguiente pack no modificará piglins ni Enderman todavía: primero hay que confirmar la convención y el resultado de esta máscara aislada.

## Investigación V — Eclipse Shader y emisión selectiva (15 de octubre de 2026)

- Ángel reportó después de probar v19 que persistían los problemas visuales y que la lava seguía plana/apagada; también señaló que el Blaze se percibía demasiado brillante. La v19 no se considera una corrección aceptada.
- Se creó [Investigación V](INVESTIGACION_V_ECLIPSE_EMISION_DIRECCION_ARTISTICA.md), que audita el repositorio Eclipse Shader, su código relevante y licencia, y propone un catálogo de emisión selectiva para mobs y una dirección de color/sombra/atmósfera.
- En la inspección estática del paquete de referencia 26.50.0, los mapas MERS principales de gólem de hierro, piglin y piglin zombificado tienen el canal verde emisivo a cero. Los mapas de lava quieta/en flujo también tienen el canal verde a cero; elevar el RGB no añade emisión PBR.
- Eclipse contiene 36 fuentes Photonics en el archivo inspeccionado y son fuentes para bloques, no luces configuradas para mobs. La clasificación de entidades en entity.properties es de dispersión bajo la superficie y no un catálogo de emisión.
- Próximo enfoque recomendado: no generar todavía una nueva compilación combinada. Crear máscaras MERS pequeñas para ojos del gólem y piglin zombificado; revisar la emisión ya localizada en la TGA/MERS del Enderman sin duplicarla; experimentar con lava en un cambio independiente.
- **Límites:** auditoría de repositorio y archivos estáticos; v13 sigue congelada como referencia histórica. El Inspector se ejecutó para v20-A, pero no se ha validado el aspecto de la emisión dentro de Minecraft.

## Estado de corrección v19 (15 de octubre de 2026)
- Ángel confirmó que v18 restauró la animación y mejoró lava/portal, pero la transparencia del portal necesita ajuste, la lava se ve plana/apagada, el cielo del End sigue gris y la identidad artística se estaba perdiendo.
- Se generó una única compilación local: `hogar-dorado-v19-identidad-restaurada-PRUEBAS-PERSONALES.mcpack`, fuera de GitHub por contener recursos vanilla de referencia.
- Portal: alfa de la textura multiplicado por 0.80; animación y dimensiones preservadas.
- Lava: canales RGB de lava_still y lava_flow multiplicados por 1.20, limitados a 255; emisión PBR sin aumentar para evitar lavar el detalle.
- End: sky_color #7560B8, fog_color #302344, inicio de niebla 0.90 y densidad volumétrica aérea 0.0008. Es una hipótesis más marcada, no una corrección visual confirmada.
- Validación estática: ZIP íntegro, 176 archivos, 119 JSON válidos; portal 16×512, lava_still 16×512, lava_flow 32×512. No se han medido FPS, memoria o temperatura.
- **Resultado de prueba visual de Ángel:** tras probar v19, reportó que persistían los mismos problemas; la lava aún se veía plana/apagada. También indicó que el Blaze brillaba demasiado y parecía raro al ser el único mob con brillo destacado. Por tanto, v19 no se considera aprobada visualmente. La siguiente fase se documenta en Investigación V y separa la emisión de mobs del rediseño de lava. La raíz artística sigue siendo «Una nostalgia que parece un sueño»: atardeceres dorados, noches índigo, misterio sutil y fidelidad a Minecraft.

## Estado de corrección v18 (14 de octubre de 2026)
- Ángel probó la v17 y reportó que lava y portal del Nether seguían viéndose mal; también percibió pérdida del impacto visual del paquete. Las capturas muestran texturas base con bandas diagonales repetitivas.
- Se generó una única compilación local: hogar-dorado-v18-restaurada-PRUEBAS-PERSONALES.mcpack. El archivo queda fuera de GitHub porque contiene recursos vanilla de referencia.
- Se restauraron las imágenes de lava quieta, lava en flujo y portal desde el ZIP de referencia 26.50.0 compartido por Ángel. Se actualizaron sus tres Texture Sets al formato 1.21.30 de referencia y se incorporaron mapas MERS para lava/portal y mapas normal para ambas texturas de lava. Las dimensiones de color y mapas coinciden; las tiras se mantienen en 16×512, 32×512 y 16×512.
- Para recuperar contraste sin alterar el mundo normal ni el Nether, el End recibió un perfil de gradación frío/índigo y menos niebla volumétrica aérea: fog_start 0.80 y max_density 0.0028.
- Validación estática: ZIP íntegro, 119 JSON válidos, 176 archivos, 27 Texture Sets con referencias completas y dimensiones de color/mapas coincidentes; las tres imágenes de color restauradas son idénticas a la referencia.
- **Pendiente:** probar v18 en Bedrock 26.52 con Vibrant Visuals activo y capturar de nuevo lava quieta/en flujo, portal y el End. No se han medido FPS, memoria ni temperatura. Uso personal únicamente; no distribuir.

## Estado de pruebas v16 (prueba visual de Ángel)
- Ángel probó la variante equilibrada v16 en Minecraft Bedrock 26.52 y compartió capturas reales.
- Hallazgos reportados: texturas que pierden detalle, emisión demasiado intensa en algunas superficies/lava y visibilidad subacuática insuficiente; indicó que le gustan los destellos/partículas de las escenas submarinas.
- Solo se probó la variante equilibrada; la variante intensa no se probó y no continuará como línea de trabajo por ahora.
- El informe técnico original de v16 está en docs/INVESTIGACION_V16_PRUEBAS_EMISION.md.
- Las comprobaciones estáticas anteriores de v16 siguen válidas (ZIP, JSON y 26 Texture Sets), pero no garantizaban el aspecto visual. No hay medidas de FPS, memoria, temperatura ni comodidad visual.

## Estado de corrección v17 (14 de octubre de 2026)
- Se generó una sola compilación local: `hogar-dorado-v17-equilibrada-CORREGIDA-PRUEBAS-PERSONALES.mcpack`, fuera de GitHub porque contiene recursos vanilla de referencia.
- Cambios principales: restauración de rugosidad/metalness desde mapas de referencia; emisiones selectivas y reducidas en minerales; máscaras de emisión espaciales recuperadas para glowstone, sea lantern y shroomlight; emisión PBR de lava apagada en el mapa material para evitar lavar la textura; absorción volumétrica submarina reducida en los 19 perfiles de niebla.
- Validación estática: ZIP íntegro, 117/117 JSON válidos, 26 Texture Sets, cero referencias faltantes y cero discrepancias dimensionales de mapas.
- **Resultado de prueba visual:** Ángel volvió a probar v17 y confirmó que la lava y el portal del Nether seguían mal; además notó menos impacto visual del esperado. Los problemas llevaron a preparar v18. No se midieron FPS, memoria ni temperatura en el iPhone 14 Plus.

## Contexto confirmado
- La intención artística combina nostalgia, dreamcore, atardeceres dorados, noches índigo y profundidad atmosférica.
- La prioridad es preservar la identidad de Minecraft y ofrecer armonía visual, no solo efectos espectaculares.
- El proyecto es también un experimento de aprendizaje sobre creación digital, colaboración con IA y posible monetización; los ingresos no están garantizados.
- La cuenta de CurseForge se mostró inscrita en Rewards Program y con 2FA habilitado; esto no garantiza elegibilidad de Hogar Dorado ni recompensas.
- MCPEDL ya aparecía vinculado en la cuenta revisada.
- La creación/publicación se pausó para no presentar una beta temprana sin capturas reales.
- Existe una primera portada conceptual, pendiente de aprobación y refinamiento.
- Ángel compartió guías técnicas de Bedrock y packs comunitarios para que la investigación y el desarrollo se basen en ejemplos concretos, respetando los derechos de sus creadores.
- Claude informó que guardó una referencia de v13, el script de construcción y `CAMBIOS.md` en `referencia/`; verificar esos archivos directamente antes de retomar la implementación.

## Investigación añadida
Consulta `docs/INVESTIGACION_TECNICA_OPTIMIZACION_PBR.md` para ver:
- Revisión de texture sets, textura de color, MER y rutas.
- Plan de prueba de emisión empezando por redstone y una entidad candidata.
- Técnicas y criterios de optimización: tamaño descomprimido, cantidad de archivos, texture handles, presupuesto de texturas y pruebas de dispositivo.
- Separación propuesta entre ambiente, emisión y ajustes de calidad.
- Cinco escenas reproducibles y matriz de experimentos.
- Fuentes oficiales de Microsoft Learn y Mojang, además de materiales que Ángel ya había aportado.

## Investigación técnica II — arquitectura visual y técnicas avanzadas
Consulta `docs/INVESTIGACION_II_ARQUITECTURA_VISUAL_Y_TECNICAS.md` para ver:
- Distinción entre emisión de textura PBR y luz local coloreada mediante `local_lighting.json`.
- Hallazgos estáticos de BSLB, Solar y Dark Fantasy sobre iluminación local, perfiles por bioma, fallback PBR, subpacks y recursos.
- Criterios para biomas recientes, keyframes, subsurface scattering y cubemap.
- Propuesta de validación con Minecraft Creator Tools y futuro validador automatizado.
- Experimentos priorizados y fuentes oficiales.

El informe incluye recuentos estáticos de los packs, no benchmarks. No se ha ejecutado Minecraft ni se han probado estos cambios en el dispositivo. Verificar las características contra la versión de Bedrock indicada por Ángel (26.52) y la referencia v13 antes de implementarlas.

## Investigación III — compatibilidad, Creator Tools y auditoría de v12
Consulta `docs/INVESTIGACION_III_COMPATIBILIDAD_CREATOR_TOOLS_Y_V12.md` para ver:
- Auditoría estática del archivo v12 compartido, claramente separada de la referencia v13.
- Posible mejora del agua con biome_water_color_contribution (Bedrock 1.26.0+) y pruebas sugeridas.
- Revisión de los perfiles de iluminación/atmósfera a nivel de bioma.
- Compatibilidad en la pila de recursos: los Texture Sets no se fusionan entre packs.
- Flujo sugerido para usar Minecraft Creator Tools Inspector en una copia del MCPack.
- Oportunidades de mantener un catálogo único de biomas y futuras comprobaciones automáticas.

No se ejecutó Minecraft ni Creator Tools desde esta actualización; los resultados de v12 son inventario estático. Contrastar todo con la referencia v13 antes de modificar archivos.

## Propuesta de catálogo de biomas y generador
Consulta `docs/PROPUESTA_CATALOGO_BIOMAS_Y_GENERADOR.md` para ver una arquitectura propuesta de fuente única JSON, perfiles compartidos, generación determinista, pruebas del generador y futura integración con GitHub Actions.

**Estado:** documentación de diseño solamente. El repositorio actualmente contiene documentación, pero todavía no tiene el resource pack fuente ni el script de construcción. Antes de implementar, Claude debe cotejar la propuesta con el script existente y extraer los valores reales de la referencia v13. No reconstruir el catálogo a partir de la paleta de v8.

## Investigación IV — auditoría de Vanilla Resource Pack 26.50.0
Consulta `docs/INVESTIGACION_IV_AUDITORIA_VANILLA_26_50.md` para hallazgos estáticos sobre:
- 3.130 Texture Sets y uso de rutas de color PNG/TGA.
- Emisión selectiva por canal verde en MER/MERS, incluida la comparación de redstone vanilla vs BSLB y mapas de entidades.
- Texture Sets en bloques, objetos, criaturas, partículas y otros recursos.
- Cobertura de biomas y diferentes format_version.
- Diferencias entre la copia v12 y la referencia que Claude debe comprobar en v13.
- Criterios para Creator Tools, validación de rutas y compatibilidad/licencia.

**Límites:** la fuente aportada es un paquete comunitario vanilla de Bedrock 26.50.0, no el código del motor ni un volcado exacto de la instalación 26.52. No se ejecutó Minecraft ni se midió rendimiento. El repositorio fuente declara que los assets pertenecen a Mojang/Microsoft; no copiar assets binarios al pack final ni al repositorio de Hogar Dorado.

## Evidencia y límites
- La portada generada es una referencia visual, no una captura real del juego.
- La v16 equilibrada sí tiene una prueba visual de Ángel documentada; la v17 aún no se ha probado en Minecraft.
- No se ha confirmado ningún ingreso, recompensa, nivel de descargas ni aceptación de publicación.
- No registrar pruebas como exitosas a menos que se hayan ejecutado y documentado.
- El inventario numérico de los packs de referencia incluido en la investigación es preliminar; no representa un benchmark de rendimiento.
- La única prueba visual de gameplay registrada en este ciclo corresponde a v16; no se midieron FPS/temperatura ni se probó v17.

## Último hito
Se documentó Investigación VI sobre ocho packs Bedrock, modos de compatibilidad de Actions & Stuff y el flujo de Inspector. El paquete personal v20-A pasó 40 pruebas aplicables del Inspector cuando se validó con el nombre corto `HD-v20A.mcpack` (0 errores, 21 advertencias, 0 fallos). Sigue pendiente comprobar el aspecto dentro del iPhone. La v13 continúa congelada como referencia histórica.

## Próximas tres tareas
1. Importar v20-A en un mundo de prueba de Bedrock 26.52 y comparar los ojos del gólem contra v19 y la referencia v13 congelada; registrar capturas, orden de packs y condiciones de iluminación.
2. Revisar las 21 advertencias solo si hay evidencia adicional o incompatibilidad reproducible; no quitar propiedades respaldadas por la documentación oficial únicamente para silenciar al Inspector.
3. Solo después del resultado visual, preparar el experimento aislado del piglin zombificado y otro experimento independiente para lava con máscara MERS y contraste costra/grieta. No publicar recursos vanilla ni activos de terceros sin permiso.

## Decisiones que requieren aprobación de Ángel
- Aprobación final del logotipo y la portada.
- Cualquier publicación pública del pack.
- Cambios de licencia, visibilidad del repositorio o distribución.
- Decisiones importantes que alteren la visión artística o impliquen costes.
- Decisiones de diseño que alteren de forma relevante los brillos, la atmósfera o el rendimiento objetivo.

## Registro técnico
- **Pruebas de gameplay:** Ángel probó v16 equilibrada, v17 y v19 y compartió comentarios/capturas; v19 no fue aprobada visualmente. v20-A obtuvo 0 errores y 0 pruebas fallidas en Creator Tools al importarse con nombre corto; todavía no se ha probado visualmente dentro de Minecraft.
- **Benchmarks de rendimiento:** no realizados.
- **Cambio de esta actualización:** se documentó la segunda ejecución de Inspector de v20-A. El paquete personal binario sigue fuera del repositorio.
