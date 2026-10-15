# Hogar Dorado v16 — preparación y auditoría de pruebas personales

**Fecha:** 2026-10-15  
**Base:** `hogar-dorado-v15-PRUEBAS-PERSONAL` entregado por Ángel.  
**Juego objetivo:** Minecraft Bedrock 26.52 en iPhone 14 Plus.  
**Estado:** paquetes generados y validados estáticamente; prueba dentro del juego pendiente.

## Archivos de prueba generados

Se generaron dos variantes para comparar la intensidad de emisión sin mezclar los resultados:

- `hogar-dorado-v16-equilibrada-PRUEBAS-PERSONALES.mcpack` — emisión más moderada.
- `hogar-dorado-v16-intense-PRUEBAS-PERSONALES.mcpack` — emisión alta para comparación visual.

Ambos usan UUID nuevos, nombre visible distinto y versión interna `[1, 15, 0]`. No deben activarse simultáneamente: son dos variantes del mismo pack para comparar en el mismo escenario.

**Uso estrictamente personal de prueba. No publicar ni vender.** Ambos incluyen texturas vanilla y mapas de referencia de Mojang/Microsoft extraídos del paquete aportado por Ángel. Este informe conserva solo observaciones y datos técnicos, no recursos binarios.

## Comparación de versiones

### v14 → v15

La auditoría local confirmó que v15 corrigió las dimensiones de texturas animadas:
- `lava_still.png` y su mapa: 16×512.
- `lava_flow.png` y su mapa: 32×512.
- `portal.png`: 16×512.
- `sea_lantern.png` y su mapa: 16×80 (5 cuadros).

Las referencias de los 23 Texture Sets de bloques presentes en v15 resuelven a imágenes existentes, y los mapas de material tienen las mismas dimensiones que sus texturas de color. No se encontraron errores JSON estrictos en los archivos de v15.

### v15 → v16

Cambios realizados en las dos variantes:
1. Se actualizó el nombre de manifest y la versión de `[1, 14, 0]` a `[1, 15, 0]`; se generaron UUID distintos para cada variante.
2. Se conservaron los perfiles de ambiente, biomas, niebla, sombras, agua y el mapa emisivo variable de lava.
3. Se mantuvo la máscara espacial de emisión de minerales, modificando solo el canal verde de los mapas MER.
4. Se añadieron Texture Sets y mapas MERS de entidad para Enderman, Blaze y Magma Cube, usando los nombres/rutas de textura correspondientes en la referencia vanilla 26.50.0. Se incrementó el canal verde de emisión sin cambiar la máscara de píxeles, los otros canales del mapa ni el alfa.
5. No se modificaron los JSON de entidad vanilla: estos Texture Sets utilizan las rutas de textura que las entidades vanilla ya referencian. Esto es una prueba PBR de entidades, no una prueba de `entity_emissive_alpha`.

## Parámetros de emisión

| Material | v15 | v16 equilibrada | v16 intensa |
|---|---:|---:|---:|
| Minerales ordinarios | 200 en píxeles emisivos | 130 | 220 |
| Redstone (normal y deepslate) | 200 | 155 | 220 |
| Glowstone | 190 en toda la imagen | 150 | 220 |
| Sea lantern | 190 en toda la imagen | 150 | 220 |
| Shroomlight | 200 en toda la imagen | 150 | 220 |
| Lava | mapa variable, conservado | conservado | conservado |
| Enderman (máximo canal G) | no incluido en v15 | 96 | 160 |
| Blaze (máximo canal G) | no incluido en v15 | 60 | 120 |
| Magma Cube (máximo canal G) | no incluido en v15 | 78 | 130 |

Los números son valores de píxel del canal verde del mapa MER/MERS, no una medición del brillo final en pantalla. La variante intensa se ofrece como extremo comparativo, no como recomendación de calidad.

## Validación estática completada

Para ambas variantes:
- Integridad ZIP: correcta.
- JSON: todos los archivos parsean con un parser JSON estricto.
- Texture Sets: 26 en total (23 de bloques y 3 de entidades).
- Referencias de textura color/MER/MERS: 0 faltantes.
- Dimensiones de color y mapa de material: 0 discrepancias.
- Animaciones verificadas: lava still 16×512; lava flow 32×512; portal 16×512; sea lantern 16×80.
- Manifiesto: capability `pbr` conservada; UUID de header y módulo distintos.

La validación estática no puede garantizar que Minecraft acepte cada componente o que la apariencia sea la esperada. No se ejecutó Minecraft, no se midieron FPS, memoria, temperatura o comodidad visual en el iPhone.

## Protocolo de prueba recomendado

1. Importar ambos paquetes, pero activar **solo una variante a la vez**.
2. Usar el mismo mundo, hora, posición, distancia de cámara y configuración gráfica para ambas pruebas.
3. Con Vibrant Visuals activado, probar una escena con:
   - redstone y deepslate redstone;
   - glowstone, sea lantern y shroomlight;
   - lava quieta y en flujo;
   - Enderman, Blaze y Magma Cube.
4. Tomar capturas desde la misma posición, en un entorno oscuro y en uno iluminado.
5. Registrar si el brillo conserva el pixel art, si las criaturas pierden detalle, si la lava sigue animándose y si aparecen errores en el pack.
6. En iPhone 14 Plus, observar tirones, calentamiento y comodidad visual durante la misma ruta. No inferir rendimiento por el tamaño comprimido del archivo.
7. Si aparecen errores, enviar captura de pantalla y el texto exacto del error. No cambiar otros ajustes entre la prueba equilibrada y la intensa.

## Fuentes técnicas

- [Microsoft Learn — Vibrant Visuals Resource Packs](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/vvresourcepacks?view=minecraft-bedrock-stable)
- [Microsoft Learn — Overview of Physically Based Rendering](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/pbroverview?view=minecraft-bedrock-stable)
- [Microsoft Learn — Texture Set schema 1.21.30](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/visualreference/texture_set.v1.21.30?view=minecraft-bedrock-stable)
- [Minecraft Usage Guidelines](https://www.minecraft.net/en-us/usage-guidelines)

## Resultado y siguiente decisión

El paso siguiente es una comparación visual de las dos variantes en Minecraft 26.52. No debe marcarse ninguna como versión final hasta confirmar la apariencia y estabilidad en el iPhone. Mantener v13 como referencia histórica congelada; estos paquetes son variantes experimentales derivadas de v15.
