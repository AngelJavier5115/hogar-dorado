# Investigación IV — Auditoría del Vanilla Resource Pack 26.50.0

**Fecha de auditoría:** 2026-10-15  
**Archivo examinado:** MCBVanillaResourcePack-26.50.0.zip, aportado por Ángel.  
**Versión de Minecraft que Ángel indica tener:** Bedrock 26.52.  
**Estado:** inspección estática local, investigación documental y propuestas; no se editó el pack.

> **Importante sobre procedencia y alcance:** el ZIP es un paquete comunitario de recursos vanilla de Bedrock, no el código fuente del motor de Minecraft. Su README declara que los assets pertenecen a Mojang y Microsoft y que el repositorio pasó a ser una plantilla porque sus autores no son dueños de esos recursos. Este informe registra estructura, patrones y conteos; no copia imágenes, archivos vanilla ni recursos de terceros al repositorio Hogar Dorado. Antes de distribuir cualquier asset vanilla, revisar las Minecraft Usage Guidelines y los permisos correspondientes.

Fuentes:
- [Repositorio MCBVanillaResourcePack y README](https://github.com/ZtechNetwork/MCBVanillaResourcePack)
- [Minecraft Usage Guidelines](https://www.minecraft.net/en-us/usage-guidelines)
- [Microsoft Learn — Vibrant Visuals Resource Packs](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/vvresourcepacks?view=minecraft-bedrock-stable)
- [Microsoft Learn — Client Biomes JSON and Overview](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/clientbiomesreference/examples/clientbiomesoverview?view=minecraft-bedrock-stable)
- [Minecraft Bedrock 26.52 Hotfix Changelog](https://feedback.minecraft.net/hc/en-us/articles/49175370527501-Minecraft-Bedrock-Edition-26-52-Hotfix-Changelog)

## 1. Resumen ejecutivo

La principal lección del paquete vanilla no es “crear miles de mapas PBR”. Es aprender cómo vincula cada textura a un Texture Set, cómo separa materiales de entidades y bloques, cómo usa la emisión por píxel, cómo combina archivos de bioma con perfiles ambientales y qué detalles debemos verificar automáticamente.

Hallazgos destacados:

1. El archivo contiene 18.417 archivos y ocupa aproximadamente 269,5 MiB descomprimidos. Es un paquete muy completo, no una plantilla ligera que debamos copiar como base de distribución.
2. Encontré 3.130 archivos Texture Set. Los 3.130 declaran una propiedad MER/MERS y un nombre de textura de color; las rutas de color se resuelven con archivos PNG o TGA. La mayoría usa mapas MERS y una definición por recurso.
3. Los Texture Sets existen también para entidades, criaturas, objetos, partículas, modelos y otros recursos. La documentación oficial confirma que Vibrant Visuals admite PBR para bloques y para entidades, mobs, partículas e ítems.
4. La emisión está diseñada por material y por píxel. En los mapas examinados, la redstone vanilla no emite por su canal verde; el pack BSLB sí activa un conjunto selectivo de píxeles del mineral. El enderman vanilla presenta emisión en una parte diminuta de su mapa, mientras el beacon tiene emisión máxima en toda su imagen. No todo necesita la misma intensidad.
5. Las hojas de roble del paquete vanilla tienen un mapa de emisión con el canal verde en cero, pero valores variables en el canal alfa del MERS. Esto demuestra que la información de dispersión subsuperficial y la emisión son cosas distintas.
6. La carpeta de referencia tiene 89 archivos client biome y varios perfiles atmosféricos, de iluminación, agua y gradación. No aparece local_lighting/local_lighting.json ni pbr/global.json en esta copia; esos sistemas no son obligatorios en toda arquitectura y debemos elegirlos según la intención de Hogar Dorado.
7. La copia del manifiesto incluida en este repositorio comunitario no declara la capacidad pbr, aunque contiene muchos Texture Sets. La documentación oficial actual pide declarar pbr para un pack Vibrant Visuals. Por lo tanto, no debemos copiar su manifest como plantilla de publicación.

## 2. Inventario del archivo

La comprobación de la estructura ZIP indicó:

| Métrica | Resultado |
|---|---:|
| Entradas ZIP totales | 18.903 |
| Archivos no directorio | 18.417 |
| Tamaño comprimido de entradas | Aproximadamente 160,6 MB |
| Tamaño descomprimido | Aproximadamente 269,5 MB |
| Archivos de textura (*.texture_set.json) | 3.130 |
| Archivos client biome | 89 |
| Archivos MERS TGA | 3.130 |
| Archivos fog | 80 |
| Archivos atmospherics | 17 |
| Archivos lighting | 15 |
| Archivos color grading | 14 |
| Archivo local_lighting/local_lighting.json | No encontrado |
| Archivo pbr/global.json | No encontrado |

Los conteos y tamaños son inventario estático de la copia aportada, no benchmarks ni una auditoría de rendimiento. El paquete contiene recursos gráficos y muchas otras categorías —incluidos sonidos, interfaz, modelos y animaciones— por lo que su volumen total no es comparable directamente con el tamaño de Hogar Dorado.

### Versiones de esquema

Los 3.130 Texture Sets de esta copia declaran format_version 1.21.30. En cambio, los archivos client biome usan varias versiones:
- 87 archivos: 1.21.120.
- sulfur_caves: 1.26.0.
- dappled_forest: 1.26.50.

Esto refuerza un principio para el catálogo y generador que propusimos: la versión de cada archivo debe corresponder al esquema/función que utiliza. No hay que reemplazar mecánicamente todos los format_version por la versión completa del juego. Confirmar siempre la versión compatible con la instalación objetivo y los campos que realmente se usan.

La versión de juego de la copia es 26.50.0, mientras Ángel indica que tiene 26.52. Las notas oficiales de 26.52 mencionan, entre otras correcciones, detección de cubemap arrays en equipos Windows antiguos y un error visual de ítems del inventario; no anuncian un cambio del esquema PBR. Aun así, esta copia no es una fuente exacta de todos los recursos de 26.52: es una referencia próxima, que se debe contrastar con Creator Tools y el juego antes de asumir compatibilidad idéntica.

## 3. Cómo están vinculados los Texture Sets

### Patrón observado

En los 3.130 Texture Sets:
- Todos contienen color.
- Todos declaran la propiedad metalness_emissive_roughness_subsurface.
- 3.129 referencian un mapa por nombre y la entrada restante usa valores uniformes para flame_atlas.
- Los nombres de color se resuelven con texturas compañeras PNG o TGA.
- Las referencias a mapas de material se resuelven en archivos del paquete.
- Solo cuatro Texture Sets inspeccionados incluyen la propiedad normal; ninguno declara heightmap en esta copia.

Un archivo típico declara un nombre de color y un nombre de mapa MERS, sin incluir las extensiones en los valores. En el paquete vanilla, algunas texturas de color son TGA en lugar de PNG.

### Consecuencia directa para nuestro validador

**No validar rutas suponiendo que todas las texturas de color terminan en .png.** Esa regla produciría falsos errores con texturas legítimas TGA. El futuro validador debe resolver las extensiones admitidas y comprobar exactamente el directorio y el nombre de cada recurso. También debe distinguir:
- una imagen realmente ausente;
- una imagen existente con extensión distinta;
- una propiedad de material uniforme que no necesita un mapa externo;
- una referencia rota;
- un nombre duplicado o ambiguo.

No se deben declarar todos los mapas válidos solo porque el archivo exista. También hay que comprobar el esquema JSON, las dimensiones y que el resultado se vea correctamente dentro del juego.

### MER/MERS: funciones distintas

En el mapa de material:
- canal rojo: metalness;
- canal verde: emisión;
- canal azul: roughness;
- canal alfa en los MERS que lo incluyen: subsurface scattering.

La extensión o el nombre del archivo no cambia por sí solo la función de los canales. No confundir el alfa del mapa MERS con el alfa de la textura de color usada para transparencia o con la técnica entity_emissive_alpha.

Fuente: [Microsoft Learn — Vibrant Visuals Resource Packs](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/vvresourcepacks?view=minecraft-bedrock-stable).

## 4. Hallazgo principal: emisión selectiva, no emisión indiscriminada

La inspección de los mapas de color/materia mostró diferencias claras entre el pack vanilla y los packs comunitarios.

| Recurso examinado | Canal verde emisivo observado | Lección para Hogar Dorado |
|---|---|---|
| Redstone ore vanilla | Todos los píxeles en 0 | El mineral vanilla no necesita emisión PBR para existir; emitirlo es una decisión de estilo del pack. |
| Redstone ore de BSLB | 39 de 256 píxeles tienen emisión; el máximo del canal verde es 177 en la imagen examinada | La emisión se limita a detalles concretos en vez de llenar toda la textura. |
| Redstone ore de Dark Fantasy | El máximo observado del canal verde es 2 | Tener un archivo MER no implica que el efecto luminoso vaya a ser intenso. |
| Enderman vanilla | 6 de 2.048 píxeles tienen emisión; valor máximo observado 32 | Los detalles emisivos pueden ser muy pequeños: se conserva casi toda la textura original del mob. |
| Spider vanilla | 20 de 2.048 píxeles tienen canal verde distinto de cero; máximo observado 41 | El brillo de los ojos no necesita iluminar toda la criatura. |
| Glowstone vanilla | El canal verde observado se encuentra entre 21 y 41 en la muestra examinada | Un bloque completo puede tener emisión moderada sin usar una emisión máxima uniforme. |
| Shroomlight vanilla | Valor verde observado 13 en toda la textura muestreada | El valor es distinto entre recursos; no reutilizar una intensidad como regla universal. |
| Beacon vanilla | Canal verde 255 en toda la imagen examinada | Hay materiales cuya función visual sí justifica una emisión extrema. |
| Hojas de roble vanilla | Canal verde emisivo en 0; alfa del MERS variable | La dispersión subsuperficial en follaje es una dimensión distinta de la emisión. |

Estos valores se calcularon leyendo las imágenes del ZIP local y contando canales de píxeles. No equivalen a una medida directa de brillo en pantalla; la apariencia final depende del motor, la iluminación y el resto de la configuración.

### Qué aprendemos de la comparación

- La emisión debe diseñarse de acuerdo con la forma de la textura, no simplemente con el nombre del bloque.
- Una textura set válido puede usar un mapa de emisión casi apagado; que exista un archivo no demuestra que el efecto sea perceptible.
- Una emisión alta y uniforme en todos los píxeles solo se justifica en casos concretos, por ejemplo un beacon; para un mineral, puede borrar visualmente sus detalles.
- El mismo objeto puede necesitar perfiles diferentes según su función: un mineral es un detalle de descubrimiento; un farol ilumina un espacio; los ojos de una criatura ayudan a identificarla.
- El canal de subsurface scattering puede utilizarse en materiales adecuados sin que eso implique convertirlos en emisores.

### Comparación con el Hogar Dorado v12 adjunto previamente

La copia v12 compartida anteriormente contenía cuatro mapas llamados lava_still_mer.png, lava_flow_mer.png, glowstone_mer.png y sea_lantern_mer.png, pero no incluía archivos .texture_set.json. Por ello, los mapas por sí solos no prueban que estuvieran conectados correctamente al motor.

Los mapas de glowstone y sea_lantern en esa v12 tenían el canal verde uniforme en 190 para los 256 píxeles. Es una emisión mucho más uniforme que la variedad que observamos en los mapas vanilla de glowstone o redstone comunitaria. Eso no prueba cómo se vería en juego, pero sí justifica que Claude revise si esa uniformidad es intencional, si los Texture Sets de v13 los enlazan y si el resultado conserva la textura original.

Los mapas MER de lava en aquella v12 tenían las mismas dimensiones globales que sus imágenes de color asociadas: lava_still 16×320 y lava_flow 32×512. Eso es una buena señal estructural para una animación, pero sigue pendiente verificar los enlaces en la versión actual y comprobar visualmente la animación dentro de Minecraft.

**No asumir que estos detalles de v12 siguen presentes en v13.** Claude debe inspeccionar la referencia más reciente antes de decidir qué cambiar.

## 5. Entidades y criaturas: corregir una suposición demasiado general

El paquete vanilla contiene Texture Sets dentro de textures/entity para entidades como enderman, spider, cave spider, blaze, glow squid e iron golem. Sus mapas MERS se enlazan desde los Texture Sets de esos recursos.

Microsoft Learn confirma que Vibrant Visuals permite PBR en bloques, entidades, mobs, partículas e ítems, siguiendo las reglas de Texture Sets en los directorios correspondientes.

Por tanto, el enfoque anterior de que para todas las criaturas se debe utilizar solo entity_emissive_alpha era demasiado general. Esa técnica es una opción, pero no es la única técnica documentada para los casos adecuados.

### Regla de decisión sugerida

Para cada criatura candidata:
1. identificar la textura de color que la versión objetivo realmente usa;
2. inspeccionar si hay Texture Set y un mapa MERS;
3. decidir si conviene emisión PBR por píxel o el método entity_emissive_alpha de acuerdo con su material/renderizado;
4. cambiar una criatura por vez;
5. revisar transparencia, mapas de color, animaciones y compatibilidad con packs de entidades;
6. probar el resultado en el juego.

No copiar texturas ni mapas de Mojang al pack distribuible sin confirmar los permisos. El objetivo es adoptar el patrón técnico, no volver a empaquetar assets vanilla.

## 6. Biomas, atmósfera y archivos heredados

Esta copia contiene dos estrategias de biomas al mismo tiempo:
- 89 archivos individuales .client_biome.json.
- un archivo antiguo biomes_client.json que identifica sus valores como “Vanilla 1.20.0”.

La documentación oficial afirma que los archivos individuales client biome son el lugar preferido para la configuración por bioma y que el archivo biomes_client.json dejó de cargarse desde el Vanilla resource pack incluido con el juego a partir de 1.21.40. El soporte de ese archivo para contenido externo tiene matices históricos, así que no se debe asumir que está inutilizado en todos los contextos; para trabajo nuevo, utilizar client biome files y probarlos.

### Componentes que observé

Los 89 archivos client biome incluyen fog_appearance y water_appearance. La mayoría también tiene referencias de atmósfera, color grading, iluminación y sonidos ambientales. Hay excepciones:
- dappled_forest usa format_version 1.26.50 y define color de hierba, follaje, follaje seco, cielo, agua y niebla, sin todos los perfiles ambientales que llevan la mayoría.
- sulfur_caves usa format_version 1.26.0 y añade perfiles ambientales, iluminación, agua, sonidos bajo el agua y música de bioma.

La lección no es replicar estos valores de color. Es estudiar cómo una versión puede tener excepciones justificadas y cómo los nuevos biomas incorporan componentes compatibles con su esquema. Un generador debe permitir que un bioma omita un componente si quiere usar el comportamiento predeterminado; no debe forzar todos los campos en todos los registros.

El catálogo propuesto para Hogar Dorado debería extraer la configuración real de v13, mantener un inventario de biomas versionado y añadir validaciones para perfiles inexistentes y biomas pendientes. No utilizar la lista de colores de Hogar Dorado v8 como fuente activa.

Fuente: [Microsoft Learn — Client Biomes JSON and Overview](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/clientbiomesreference/examples/clientbiomesoverview?view=minecraft-bedrock-stable).

## 7. Arquitectura vanilla versus referencias comunitarias

En este ZIP no encontré local_lighting/local_lighting.json ni pbr/global.json. En cambio, BSLB y Solar sí contienen local_lighting y distribuyen perfiles ambientales y subpacks. La conclusión prudente es:
- el paquete vanilla que examinamos no usa esos archivos;
- los packs de la comunidad aportan sistemas adicionales para su propia dirección visual;
- no hay una única arquitectura que debamos copiar por completo.

Para Hogar Dorado, separar funciones puede seguir siendo útil:
- ambiente global/per-bioma para cielo, luz, niebla, agua y gradación;
- Texture Sets en recursos elegidos cuando haya una ganancia artística real;
- iluminación local coloreada solo si se desea ese comportamiento y la prueba confirma el coste/resultado;
- perfiles y recursos con namespace propio para reducir colisiones con otros packs.

La ausencia de local_lighting en el ZIP tampoco prueba que el motor no pueda iluminar bloques por otras vías. Solo describe esta copia concreta del recurso vanilla.

## 8. Lección de estructura y licencia

El repositorio MCBVanillaResourcePack indica expresamente que dejó de considerarse un pack mantenido y que pasó a ser una plantilla, porque quienes lo publican no son propietarios de los assets; su README atribuye los assets a Mojang y Microsoft. Por tanto:
- no subir el ZIP de 158 MB a hogar-dorado;
- no copiar su atlas, texturas de color, mapas MERS, sonidos, iconos u otros assets al repositorio del proyecto para distribuirlos;
- no incluir texturas vanilla extraídas en el MCPack público solo porque el archivo estuviera disponible;
- conservar únicamente notas técnicas, nombres de estructuras y conclusiones propias de investigación;
- consultar las directrices oficiales y los permisos antes de redistribuir cualquier recurso.

El archivo no es el código fuente del renderizador de Minecraft: es una distribución comunitaria de recursos vanilla y configuraciones. Es valioso para observar nombres, estructuras y enlaces, pero no revela por sí solo cómo está implementado internamente el motor gráfico.

## 9. Implicaciones de programación y validación

El futuro validador de Hogar Dorado debería:
- buscar texture sets y confirmar que el color se resuelve como PNG o TGA;
- aceptar mapas MER/MERS externos o valores uniformes válidos según el esquema utilizado;
- distinguir MER de MERS y validar combinaciones compatibles;
- comprobar dimensiones de imágenes, sobre todo en texturas animadas;
- registrar qué canales de emisión tienen valores no nulos, como dato diagnóstico, sin imponer un umbral artístico fijo;
- comprobar que cada textura set está junto al recurso correcto y que el motor lo reconoce;
- verificar entidades por separado de bloques;
- reconocer client biome files y no tratar la existencia de biomes_client.json como prueba de que la configuración moderna está presente;
- no usar el parseo JSON genérico como único criterio de validez: varios archivos del ZIP no pasaron un parser JSON estricto, por lo que la validación debe corresponder a las convenciones reales de Bedrock y usar Creator Tools como segunda capa;
- diferenciar fallos de estructura, avisos, resultados visuales y rendimiento.

La documentación oficial de Vibrant Visuals pide la capacidad pbr en el manifiesto y el min_engine_version apropiado. El manifest incluido en este paquete comunitario no declara capabilities; no copiarlo como plantilla sin corregirlo y verificarlo.

Fuente: [Microsoft Learn — Vibrant Visuals Resource Packs](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/vvresourcepacks?view=minecraft-bedrock-stable).

## 10. Experimentos concretos para la próxima versión

| ID | Pregunta | Prueba mínima | Decisión |
|---|---|---|---|
| EMIT-01 | ¿La redstone se reconoce mejor con emisión selectiva? | Comparar referencia, emisión tenue y emisión más intensa | Conservar solo si mejora reconocimiento sin borrar el pixel art |
| EMIT-02 | ¿Glowstone y sea lantern necesitan el mismo mapa? | Comparar mapas actuales de v13 y versiones con emisión por píxel | Evitar una intensidad uniforme si domina la textura |
| ENTITY-01 | ¿Qué método de emisión funciona mejor para un Enderman? | Revisar Texture Set/MERS actual y probarlo frente al método alternativo adecuado | Elegir por resultado, no por receta única |
| ENTITY-02 | ¿Los ojos de la araña pueden emitir sin afectar el cuerpo? | Una entidad y mapa selectivo | Confirmar áreas, transparencia y prioridad de packs |
| SSS-01 | ¿El follaje gana profundidad en el atardecer? | Probar subsurface bajo y medio en un solo tipo de hoja | No activarlo en todas las plantas sin evidencia |
| ANIM-01 | ¿Los mapas animados están correctamente vinculados? | Verificar que lava color/MERS y sus dimensiones coinciden en v13 | Corregir si hay desajuste de frames/rutas |
| VALID-01 | ¿Creator Tools detecta los enlaces y las extensiones correctas? | Ejecutar Inspector sobre copia del MCPack de trabajo | Guardar y comparar reporte sin sobrescribir original |
| COMP-01 | ¿Los Texture Sets de Hogar Dorado se comportan bien junto a otros packs? | Una combinación externa, probando distinto orden | Documentar el ámbito de compatibilidad real |

La prueba debe registrar versión del juego (26.52 según Ángel), commit/versión del pack, configuración gráfica, escena y captura real. No se afirma que estos experimentos se hayan ejecutado.

## 11. Prioridades recomendadas

1. Claude inspecciona la referencia v13 y confirma la estructura actual de los Texture Sets antes de reutilizar las observaciones de v12.
2. Validar la cadena de color + Texture Set + MERS en redstone, glowstone y lava.
3. Elegir un solo caso de criatura que permita comparar técnicas emisivas.
4. Integrar Creator Tools como validación previa; guardar el reporte separado del pack original.
5. Corregir el validador/generador para reconocer PNG y TGA y para permitir valores uniformes válidos.
6. Revisar client biome files y cubrir biomas recientes, usando la configuración actual de v13.
7. Medir en iPhone 14 Plus y probar compatibilidad con otros packs antes de decidir nuevas variantes.
8. Mantener los assets vanilla fuera del repositorio y de cualquier distribución de Hogar Dorado, salvo que se determine una autorización clara.

## 12. Fuentes y límites

### Fuentes oficiales
- [Vibrant Visuals Resource Packs](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/vvresourcepacks?view=minecraft-bedrock-stable)
- [Texture Sets Overview](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/texturesetsreference/texturesetsconcepts/texturesetsintroduction?view=minecraft-bedrock-stable)
- [Client Biomes JSON and Overview](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/clientbiomesreference/examples/clientbiomesoverview?view=minecraft-bedrock-stable)
- [Minecraft Bedrock 26.52 Hotfix Changelog](https://feedback.minecraft.net/hc/en-us/articles/49175370527501-Minecraft-Bedrock-Edition-26-52-Hotfix-Changelog)
- [Minecraft Usage Guidelines](https://www.minecraft.net/en-us/usage-guidelines)

### Fuente comunitaria auditada
- [ZtechNetwork/MCBVanillaResourcePack](https://github.com/ZtechNetwork/MCBVanillaResourcePack) — su README advierte que los assets pertenecen a Mojang y Microsoft y que el repositorio es una plantilla.

### Límites
- Se inspeccionaron estructuras y estadísticas de archivos localmente; no se ejecutó Minecraft ni se midieron FPS, memoria, temperatura o rendimiento.
- El paquete es la versión 26.50.0, no un volcado exacto de 26.52.
- El inventario no garantiza que cada recurso se use efectivamente por el motor; algunas propiedades requieren validación y prueba visual.
- Las métricas de emisión describen los valores de los canales de las imágenes, no el brillo final en pantalla.
- No se guardaron assets vanilla ni mapas/imágenes de los packs de referencia en GitHub.
- Esta investigación no modifica Hogar Dorado y no afirma que la v13 actual tenga los mismos problemas observados en la copia v12.

## 13. Instrucciones de continuidad para Claude

Al retomar:
1. Leer este informe y la investigación PBR previa.
2. Contrastar cada observación con la referencia v13 y CAMBIOS.md.
3. Revisar los Texture Sets y los mapas actuales para confirmar qué enlaces ya están resueltos.
4. Evaluar el redstone como primer ensayo de emisión selectiva y elegir una criatura para la prueba PBR/entidad.
5. Asegurarse de que el validador busca PNG y TGA, distingue recursos faltantes de recursos uniformes, y no depende solo del parseo JSON genérico.
6. Ejecutar Minecraft Creator Tools en una copia del pack, conservar el informe y validar los cambios en Minecraft antes de marcarlos como completos.
7. No copiar recursos vanilla del ZIP a la distribución pública; implementar solo las técnicas verificadas y los recursos que tengamos derecho a distribuir.

**Principio rector:** aprender la estructura y la intención técnica de los recursos vanilla, y aplicar solo las técnicas que refuercen la identidad de Hogar Dorado, con pruebas y una carga de recursos razonable.
