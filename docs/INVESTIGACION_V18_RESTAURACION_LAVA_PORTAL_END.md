# Hogar Dorado v18 — restauración de lava, portal y contraste del End

**Fecha:** 2026-10-14  
**Base:** Hogar Dorado v17 — equilibrada corregida.  
**Evidencia:** capturas reales de Ángel en Minecraft Bedrock 26.52.  
**Dispositivo objetivo:** iPhone 14 Plus.  
**Estado:** paquete generado y validado estáticamente; prueba en juego pendiente.  
**Artefacto local:** hogar-dorado-v18-restaurada-PRUEBAS-PERSONALES.mcpack  
**Tamaño:** 171,057 bytes.  
**SHA-256:** 56ad3f1c43496fc113dba063a908af61661a09b51e595aeea75d1aecbc0b236c

## Hallazgos visuales a partir de las capturas

Ángel informó que lava y portal del Nether seguían viéndose mal y que el paquete había perdido impacto visual. La revisión de las imágenes incluidas en el paquete mostró una causa concreta: las texturas base eran variantes estilizadas con bandas diagonales repetitivas y detalle limitado. Las dimensiones de las tiras animadas ya eran correctas en v17; el problema dominante era el contenido visual, no la longitud de las tiras.

## Cambios en v18

### Lava quieta, lava en flujo y portal

Se reemplazaron las tres imágenes base por los recursos equivalentes del paquete de referencia Bedrock 26.50.0 aportado por Ángel:

- textures/blocks/lava_still.png — 16 × 512.
- textures/blocks/lava_flow.png — 32 × 512.
- textures/blocks/portal.png — 16 × 512.

Además, para evitar depender de una mezcla incierta de recursos PBR entre packs, se actualizaron los tres Texture Sets de lava y portal a la definición de formato 1.21.30 de la referencia. Se incorporaron los mapas MERS de lava quieta, lava en flujo y portal, así como los mapas normal de lava quieta y en flujo. Estos archivos son locales a la compilación personal; no se subieron al repositorio. Las imágenes de color y todos los mapas referenciados tienen dimensiones coincidentes.

No se añadió una copia completa de flipbook_textures.json: el paquete no la tenía y las tiras mantienen las dimensiones convencionales para que Bedrock pueda aplicar sus definiciones de animación predeterminadas.

Las tres imágenes de color empaquetadas coinciden byte a byte con sus equivalentes del ZIP de referencia aportado. La validación confirma integridad y rutas, no el resultado del renderizado.

### End: recuperación moderada del contraste

Las capturas muestran una neblina gris-violeta que oculta parte de la isla y un contraste poco contundente. Para no alterar el mundo normal ni el Nether en la misma iteración, el cambio se limitó al End:

- El inicio de la niebla aérea pasa de 0.73 a 0.80.
- La densidad volumétrica máxima de aire pasa de 0.005 a 0.0028.
- Se añadió una gradación específica hogar:end con contraste y matiz frío/índigo moderados; solo se asigna a the_end.client_biome.json.

No se modificaron los ajustes ambientales globales, la luz del Nether, las partículas ni las texturas de otros bloques. La nueva gradación es una hipótesis de dirección artística y necesita prueba visual; no es un resultado ya confirmado.

## Validación estática completada

- Integridad ZIP: correcta.
- JSON: 119 archivos parseados correctamente.
- Archivos totales: 176.
- Texture Sets: 27; se comprobaron las rutas de cada textura de color y mapa asociado.
- Mapas de materiales y normal de cada Texture Set: dimensiones iguales a la textura de color correspondiente.
- Tiras de animación: lava_still 16 × 512; lava_flow 32 × 512; portal 16 × 512.
- Las tres imágenes de color restauradas coinciden byte a byte con la referencia aportada.
- Manifiesto: versión interna [1, 17, 0], UUID nuevos; capability pbr conservada.
- SHA-256: 56ad3f1c43496fc113dba063a908af61661a09b51e595aeea75d1aecbc0b236c.

La validación estática no confirma el renderizado, la animación en tiempo de ejecución ni el rendimiento. No se ha ejecutado Minecraft desde la generación de v18 ni se han medido FPS, memoria o temperatura.

## Prueba en el iPhone

1. Activar únicamente v18 y desactivar las otras copias de Hogar Dorado para evitar conflictos de recursos.
2. En el mismo mundo y con los mismos ajustes, repetir las capturas de lava quieta, lava en flujo y portal del Nether.
3. Visitar el End desde una distancia similar a las capturas anteriores. Evaluar si se distinguen mejor el terreno y las torres, y si el ambiente conserva el carácter misterioso sin verse como una capa gris.
4. No juzgar todavía otros cambios de luz/niebla en el mundo normal o Nether: no se modificaron en esta iteración.
5. Enviar capturas de las mismas escenas; si lava o portal siguen mal, revisar el registro de errores del paquete y los recursos de atlas de la versión instalada antes de cambiar más parámetros.

## Procedencia y distribución

La compilación incluye assets vanilla de referencia aportados para pruebas personales. El archivo .mcpack permanece fuera de GitHub; no publicar ni vender esta compilación. El repositorio conserva únicamente el informe técnico y el registro de los cambios.

## Principios de continuidad

- Mantener v13 como referencia histórica congelada.
- Evitar crear múltiples variantes mientras se diagnostican errores base.
- Separar los defectos de textura de los ajustes de atmósfera.
- No afirmar que algo se corrigió visualmente hasta que haya evidencia de una prueba dentro del juego.
