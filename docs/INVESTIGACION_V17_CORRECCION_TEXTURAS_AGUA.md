# Hogar Dorado v17 — corrección de materiales y visibilidad subacuática

**Fecha:** 2026-10-14  
**Base:** v16 equilibrada probada por Ángel en Minecraft Bedrock 26.52.  
**Dispositivo objetivo:** iPhone 14 Plus.  
**Estado:** paquete v17 generado y validado estáticamente; prueba en juego pendiente.  
**Artefacto local:** `hogar-dorado-v17-equilibrada-CORREGIDA-PRUEBAS-PERSONALES.mcpack`  
**SHA-256:** `53cf8ffe84cfdfd1300d7dbfdef1dd805446e95b9f397c9afb6d56c59d2aab41`

## Evidencia de la prueba v16 equilibrada

Ángel compartió capturas de una sesión real. La observación reportada fue que varias texturas mostraban errores, la lava destacaba demasiado y la visibilidad bajo el agua era muy mala pese a que desde arriba el agua se veía cristalina. También indicó que le gustaban las partículas/destellos presentes en las escenas submarinas; no se cambiaron recursos de partículas en esta corrección.

Las capturas sirven como evidencia visual de la v16, pero no permiten aislar por sí solas cada causa. Los siguientes cambios son una corrección técnica dirigida a dos causas encontradas en los mapas y perfiles del paquete; necesitan confirmación en juego.

## Diagnóstico reproducible de los mapas PBR

En la v16 equilibrada se encontraron estos valores:

- Los mapas MER de glowstone y shroomlight tenían el canal verde de emisión en 150 en todos los píxeles. Sea lantern también tenía todos los píxeles emisivos a 150, aunque la referencia vanilla aporta una máscara espacial con píxeles no emisivos.
- Las superficies MER de los minerales tenían el canal azul (rugosidad) en 40 de forma uniforme, diferente de los valores variables de la referencia vanilla.
- En los mapas MER de lava, el canal verde era distinto de cero en todos los píxeles (mínimos cercanos a 90 y máximos cercanos a 200). En la referencia vanilla aportada, el canal verde de lava estaba en cero; el mapa anterior añadía emisión PBR a toda la superficie animada y podía hacer que se perdiera detalle visual.

## Cambios hechos en la v17

1. **Minerales:** se restauraron los canales de metalness y rugosidad desde los mapas MERS de referencia, conservando la máscara espacial de emisión de v16 con intensidad reducida: 76/255 en minerales normales y 90/255 en redstone (normal y deepslate).
2. **Glowstone, sea lantern y shroomlight:** se restauró la distribución de emisión de la referencia y se amplificó el canal verde de forma moderada (×1.8), en lugar de convertir todos los píxeles en una fuente emisiva uniforme.
3. **Lava:** se restauraron los canales de material de referencia y se puso el canal verde emisivo en cero. La intención es conservar el color y detalle de la textura animada y apoyarse en la iluminación nativa del bloque. El resultado real debe confirmarse en el juego.
4. **Agua bajo el agua:** se suavizó la atenuación volumétrica en los 19 perfiles de niebla. El cambio de densidad de agua es de 0.004 a 0.0015; la absorción cambia de [0.8, 0.4, 0.25] a [0.18, 0.08, 0.04]; la dispersión cambia a [0.003, 0.006, 0.008]; y el final de niebla de agua aumenta de 120 a 160. Es una hipótesis de ajuste para mejorar la visibilidad sin eliminar completamente la sensación acuática, no un resultado visual ya confirmado.
5. **Partículas:** no se alteraron recursos de partículas ni texturas de entidad en esta iteración. Se mantiene la intención de conservar los destellos que Ángel señaló como positivos.

El paquete conserva el perfil de calidad existente. Solo se creó **una** variante equilibrada; no se generó una variante intensa adicional.

## Validación estática v17

- Integridad del ZIP: correcta.
- JSON: 117/117 archivos parsean correctamente.
- Texture Sets: 26.
- Referencias de texturas faltantes: 0.
- Discrepancias de dimensiones entre color y mapas del Texture Set: 0.
- Perfiles de niebla con el ajuste de agua: 19.
- Tamaño comprimido: 116,233 bytes.
- SHA-256 del archivo generado: `53cf8ffe84cfdfd1300d7dbfdef1dd805446e95b9f397c9afb6d56c59d2aab41`.

Estas comprobaciones no equivalen a una sesión de juego: no validan el aspecto final, los errores de runtime ni el rendimiento.

## Próxima prueba en iPhone

1. Importar v17 y activar solo esta versión; desactivar v16 y cualquier otra copia de Hogar Dorado para que no compitan por las mismas rutas.
2. Mantener Vibrant Visuals y los ajustes de vídeo iguales a los usados en las capturas previas.
3. Repetir tres comprobaciones: lava quieta/en flujo; minerales en cueva oscura; agua observada desde la superficie y desde debajo del agua.
4. Confirmar si el agua deja ver el terreno a media distancia, si los minerales conservan su color/pixel art y si la lava mantiene su detalle. Revisar además los destellos que gustaron.
5. Enviar capturas de las mismas escenas y, si algún bloque continúa con la textura equivocada, indicar el nombre del bloque o marcarlo en la captura. Aún no se ha medido FPS, memoria ni temperatura del iPhone.

## Procedencia y distribución

Este paquete de prueba personal contiene assets de referencia vanilla aportados para la investigación. Por esa razón el archivo binario se mantiene fuera del repositorio. **No publicar ni vender esta compilación personal**; el informe solo registra análisis, parámetros y pruebas pendientes.

## Fuentes técnicas

- [Microsoft Learn — Vibrant Visuals Resource Packs](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/vvresourcepacks?view=minecraft-bedrock-stable)
- [Microsoft Learn — Water Effects](https://learn.microsoft.com/en-us/minecraft/creator/documents/deferredlighting/watercustomization?view=minecraft-bedrock-stable)
- [Mojang — Bedrock Samples, Water Settings](https://github.com/Mojang/bedrock-samples/blob/main/documentation/Water.html)
- [Microsoft Learn — Biome Customization](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/biomecustomization?view=minecraft-bedrock-stable)
