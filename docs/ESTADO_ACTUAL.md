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
Se generó una única compilación de prueba v17 a partir de los errores visuales reportados por Ángel en v16. El informe docs/INVESTIGACION_V17_CORRECCION_TEXTURAS_AGUA.md registra los cambios, las comprobaciones estáticas y los límites; el aspecto final de v17 aún requiere prueba en el juego.

## Próximas tres tareas
1. Probar únicamente v17 en el iPhone 14 Plus y repetir las escenas de lava, minerales y agua documentadas en el informe.
2. Registrar las capturas comparables y precisar cualquier textura que siga fallando antes de hacer otra modificación.
3. Después de validar la apariencia, medir FPS/temperatura y contrastar los cambios con la referencia histórica v13 antes de considerar publicar.

## Decisiones que requieren aprobación de Ángel
- Aprobación final del logotipo y la portada.
- Cualquier publicación pública del pack.
- Cambios de licencia, visibilidad del repositorio o distribución.
- Decisiones importantes que alteren la visión artística o impliquen costes.
- Decisiones de diseño que alteren de forma relevante los brillos, la atmósfera o el rendimiento objetivo.

## Registro técnico
- **Pruebas de gameplay:** Ángel probó v16 equilibrada y v17, y compartió capturas; v18 permanece pendiente.
- **Benchmarks de rendimiento:** no realizados.
- **Cambio de esta actualización:** se generó v18 como paquete personal local y se documentaron las observaciones de v17; los binarios no se subieron al repositorio.
