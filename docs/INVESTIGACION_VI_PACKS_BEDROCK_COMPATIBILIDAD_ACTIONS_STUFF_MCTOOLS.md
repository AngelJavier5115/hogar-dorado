# Investigación VI — Packs Bedrock, compatibilidad con Actions & Stuff y Minecraft Creator Tools

**Fecha:** 15 de octubre de 2026  
**Proyecto:** Hogar Dorado · Minecraft Bedrock indicado por Ángel como 26.52 · Vibrant Visuals · iPhone 14 Plus  
**Estado:** auditoría estática de ocho archivos aportados; revisión documental y dos rondas de Inspector documentadas para Lush Visuals y v20-A. Las pruebas visuales y de compatibilidad dentro de Minecraft siguen pendientes.

## 1. Objetivo y conclusión ejecutiva

Ángel estudió conversaciones y comentarios de creadores en español, inglés y portugués, y observó repetidas preguntas sobre compatibilidad con **Actions & Stuff**. Es una señal cualitativa valiosa, pero aún no una medición de demanda de mercado. Los archivos aportados permiten dar el siguiente paso: estudiar cómo otros packs separan visuales, texturas, animaciones y modos de compatibilidad.

Conclusión principal: Hogar Dorado debe priorizar un modo **ambiental y PBR**, sin sustituir innecesariamente modelos, texturas de criaturas, attachables ni controladores de animación. La compatibilidad no debe ser solo una promesa: debe tener modos claros, una matriz de pruebas y versiones documentadas.

El propio Actions & Stuff 1.12 ya incluye el subpack **“Custom Experience — Compatible with other texture packs”**. Además, los packs analizados incluyen distintos ejemplos de compatibilidad explícita o de variantes que preservan texturas vanilla. Eso respalda la hipótesis de diseño de Ángel; no demuestra por sí solo el tamaño de la demanda.

## 2. Inventario de los ocho archivos

Tamaños sin comprimir calculados sumando los tamaños registrados en ZIP. Son inventario estático, no benchmark de memoria ni rendimiento. Las cifras de rutas con “subpacks” incluyen variantes alternativas; no todas se cargan simultáneamente.

| Archivo aportado | Nombre real en el manifiesto/idioma | Tamaño comprimido | Tamaño sin comprimir | Contenido relevante observado |
|---|---|---:|---:|---|
| `Prizma Visuals 2.2 (resources).mcpack` | Prizma Visuals 2.2 | 39.6 MB | 60.0 MB | 9.822 archivos; PBR, atmosféricos, gradación, agua, iluminación, además de varios modos |
| `Vivencia (resources)(1).mcpack` | **Vibrance** | 43.0 MB | 122.4 MB | 15.567 archivos; PBR, iluminación y varios modos de configuración |
| `Sueños Luminosos v1.1 (resources).mcpack` | Luminous Dreams v1.1.0 | 6.1 MB | 7.1 MB | 1.574 archivos; PBR y ajustes ambientales, con variantes 16x/32x |
| `Actions Stuff 1.12 (resources).mcpack` | Actions & Stuff 1.12 | 33.9 MB | 63.5 MB | 19.324 archivos; entidades, geometría, animaciones, controladores, attachables, partículas, sonidos y subpacks |
| `Prizma Visuals 2.2 (skin_pack)(1).mcpack` | Skin pack de Prizma | 0.03 MB | 0.03 MB | 15 archivos, 11 skins; es un skin pack separado, no un pack de iluminación |
| `Visuales de Fantasía Oscura 2.0 (resources) 2.mcpack` | **Chroma Visuals 1.1 Resource Pack** | 2.9 MB | 3.7 MB | 3.292 archivos; contiene un modo A&S Compatibility y variantes de mapas de altura, variaciones y gradación |
| `Visuales de Fantasía Oscura 2.0 (resources).mcpack` | **Dark Visuals Resource Pack** | 139.7 MB | 157.7 MB | 18.355 archivos; modos Classic y Enhanced, con/sin sombras suaves |
| `Visuales exuberantes (resources)(1).mcpack` | **Lush Visuals Resource Pack** | 1.9 MB | 1.9 MB | 1.853 archivos; pack de perfil ambiental relativamente pequeño |

### Importante: los nombres de archivo no reflejan siempre el nombre real

- El archivo “Vivencia” se identifica internamente como **Vibrance**.
- El archivo “Visuales de Fantasía Oscura 2.0 (resources) 2” se identifica como **Chroma Visuals 1.1**, no como una segunda versión de Dark Visuals.
- El archivo “Visuales de Fantasía Oscura 2.0 (resources)” se identifica como **Dark Visuals**.
- El skin pack de Prizma es un paquete separado de 11 skins; no aporta sus configuraciones PBR/atmosféricas.

No subimos estos paquetes ni sus recursos binarios al repositorio: son materiales de terceros que Ángel aportó para estudio.

## 3. Hallazgos que ayudan a diseñar la compatibilidad

### 3.1 Actions & Stuff: el subpack de compatibilidad es una decisión de producto

El manifiesto de Actions & Stuff declara tres modos:

| Opción del pack | Descripción de su selector | Uso en esta investigación |
|---|---|---|
| SP0 — Custom Experience | “Compatible with other texture packs” | Primer modo que se debe probar al combinar A&S con packs visuales |
| SP1 — Vanilla Experience | Texturas vanilla, animaciones y efectos personalizados | Referencia para conservar texturas familiares sin renunciar a animaciones |
| SP2 — Full Experience | Experiencia completa de Actions & Stuff | Requiere mayor cuidado con cambios de modelos, texturas, attachables y efectos |

El modo SP0 sigue conteniendo una cantidad importante de definiciones de entidad, attachables, modelos y controladores. Por tanto, “compatible” no significa que todos los recursos sean neutrales ni garantiza que cualquier pack funcione; es una variante diseñada para mejorar la convivencia con otros packs.

En los archivos revisados aparecen definiciones de entidades que identifican explícitamente a `minecraft:player` y otras criaturas. Varios nombres de archivo de A&S están abreviados/obfuscados, por lo que un simple recuento de rutas idénticas entre paquetes **no puede demostrar** que dos packs no vayan a interferir a nivel de identificadores o de recursos en ejecución.

### 3.2 Chroma Visuals: modo A&S explícito y prioridad documentada

El selector de Chroma Visuals incluye opciones “A&S Compatibility”. Su texto de configuración indica que añade soporte a VFX y partículas personalizadas cuando se usa Actions & Stuff, que Chroma Visuals debe colocarse por encima de A&S en el orden de recursos, y que no se debe activar ese modo sin A&S porque los visuales del jugador se romperían.

El modo A&S contiene, entre otras diferencias, una variante de `entity/player.entity.json` que referencia efectos de partículas de Chroma Visuals. Esto enseña una lección concreta: **si un pack de ambiente incluye efectos que necesitan una definición de entidad, la compatibilidad puede requerir una variante específica y documentada**, no un “compatible con todo” genérico.

Ese modo debe estudiarse con el stack correcto y no validarse en aislamiento. La prioridad que documenta Chroma corresponde a su propia implementación; no se debe copiar automáticamente como regla para Hogar Dorado sin probar nuestro conjunto de archivos.

### 3.3 Vibrance: el ejemplo de modo ambiental más útil

El archivo se llama “Vivencia”, pero su nombre interno es **Vibrance**. Sus tres modos son:

- **LOW:** iluminación + texturas vanilla.
- **MEDIUM:** iluminación + texturas de Vibrance.
- **HIGH:** iluminación + texturas de Vibrance + animación de follaje.

El texto de su idioma inglés indica expresamente que **LOW es el modo para usarlo por encima de otro texture pack**. Este es el patrón más cercano a la línea que podemos adoptar: separar un modo que aporte iluminación ambiental sin competir con las texturas ni las animaciones de otro paquete.

### 3.4 Prizma: no combinar indiscriminadamente dos sistemas de animación

Prizma contiene tres opciones:

- **CLASSIC:** iluminación y texturas de Prizma.
- **VANILLA:** iluminación de Prizma con texturas vanilla.
- **PLUS:** experiencia completa, con animaciones personalizadas y objetos 3D.

La variante VANILLA es la candidata razonable para estudiar como referencia ambiental con A&S. PLUS merece pruebas separadas porque añade animaciones y objetos 3D, ámbitos donde un segundo pack de animaciones/modelos puede interferir.

El skin pack de Prizma se evalúa aparte: su manifiesto es de tipo `skin_pack`, tiene 11 skins y no incluye la estructura ambiental/PBR del paquete de recursos.

### 3.5 Dark Visuals: el selector separa claramente el ambiente y la experiencia de modelos

El paquete grande ofrece cuatro subpacks:

- Dark Fantasy Classic.
- Dark Fantasy Classic (Smooth Shadows).
- Dark Fantasy Enhanced.
- Dark Fantasy Enhanced (Smooth Shadows).

El inventario del archivo muestra que las dos opciones Classic contienen unas 222 rutas cada una, concentradas en perfiles de bioma, niebla, atmósfera, iluminación, color grading, agua y recursos ambientales. Las opciones Enhanced contienen miles de archivos por variante, incluidos modelos, entidades, texturas, partículas, animaciones, controladores, attachables y render controllers.

**Para aprender compatibilidad con A&S, primero conviene estudiar Classic.** Enhanced constituye una prueba más amplia, con más superficies de interacción y una carga descomprimida de alrededor de 158 MB para el archivo completo, superior a la recomendación de menos de 100 MB sin comprimir indicada en la guía oficial de rendimiento de Microsoft.

### 3.6 Lush Visuals y Luminous Dreams: buenos candidatos para pruebas iniciales

**Lush Visuals** es el candidato técnico más ligero para empezar con Inspector: 1.9 MB sin comprimir. Contiene perfiles de atmósfera, niebla, iluminación, agua, color grading, iluminación local y Texture Sets PBR. En las rutas inspeccionadas no aparecen carpetas de animaciones o controladores, y los pocos recursos bajo texturas de entidad son elementos como soporte para armaduras, vagonetas y brazos de pistón. El enfoque parece ambiental antes que un reemplazo del sistema de animación de mobs.

**Luminous Dreams** ocupa unos 7.1 MB sin comprimir, define PBR y configuración ambiental, y ofrece variantes de 16x y 32x. Tampoco aparecieron archivos de animaciones/controladores en el inventario del paquete. Es otro candidato útil para comparar la prioridad de texturas y perfiles sin comenzar con un pack enorme.

Estos son hallazgos de estructura, no pruebas de funcionamiento conjunto con A&S.

## 4. Lo que dicen las colisiones estáticas —y lo que no dicen

Se normalizaron nombres de archivos quitando el prefijo de subpack para identificar posibles rutas compartidas entre las variantes inspeccionadas y Actions & Stuff. Los solapamientos observados se concentran principalmente en nombres de texturas y archivos comunes como `textures_list.json`, textos o metadatos. El recuento combina todas las variantes de cada pack, por lo que no representa la colisión real de una pareja de modos activos.

No debemos inferir incompatibilidad solo porque dos packs comparten nombres de textura, ni compatibilidad porque no comparten nombres de archivo. En Bedrock también cuentan:

- El orden de prioridad de los packs.
- La variante/subpack elegida.
- Las texturas que realmente referencia una definición de entidad.
- Los identificadores de entidad, animaciones, geometría y controladores.
- La definición del Texture Set que queda activa para una textura.
- Los perfiles de iluminación, atmósfera, niebla, agua y PBR que compiten por el mismo recurso.

Por esa razón, el siguiente paso es el **Inspector de Minecraft Creator Tools** y pruebas visuales con combinaciones controladas, no una conclusión automática a partir de rutas.

## 5. Plan de pruebas con Minecraft Creator Tools

Fuente oficial: https://mctools.dev  
Documentación de Mojang/Microsoft: https://learn.microsoft.com/es-mx/minecraft/creator/documents/mctoolsoverview?view=minecraft-bedrock-experimental

La documentación actual indica que Creator Tools acepta MCPack, MCAddon y ciertos ZIP; sus comprobaciones funcionan en la página web o mediante CLI y los archivos procesados desde el sitio no se envían al servidor. La guía de rendimiento recomienda mantener el contenido por debajo de 100 MB sin comprimir siempre que sea posible y advierte de presión de memoria con packs grandes.

### 5.1 Primera sesión desde el iPhone

1. Abrir https://mctools.dev en el navegador.
2. Usar la opción para abrir/añadir un proyecto existente y elegir el archivo desde Archivos. Si el selector solo acepta ZIP en la interfaz actual, conservar el MCPack original y preparar una copia ZIP para Inspector; no renombrar extensiones como sustituto de convertir.
3. Abrir la herramienta **Inspector**. Si las reglas no aparecen completas, revisar el modo de proyecto **Focused / Full / Raw** y escoger el conjunto de validación de Resource Pack o el destino de Bedrock que corresponda.
4. Registrar los errores/avisos exactos y exportar el informe si la página lo permite. No asumir que un pack es válido porque abre en Minecraft, ni que es inválido solo por un aviso no revisado.
5. No cambiar recursos después del informe inicial; primero guardar evidencia y después corregir un problema por experimento.

La interfaz puede cambiar. Si la versión móvil no permite seleccionar el MCPack directamente, la documentación también permite trabajar con un archivo ZIP que contenga el proyecto.

### 5.2 Orden para no saturar el dispositivo

| Orden | Archivo | Motivo |
|---|---|---|
| 1 | Lush Visuals (1.9 MB descomprimido) | Baseline pequeño, ambiental y fácil de procesar |
| 2 | Chroma Visuals (3.7 MB) | Comparar las reglas y el modo A&S Compatibility; probar con Actions & Stuff activo |
| 3 | Luminous Dreams (7.1 MB) | Otro pack principalmente ambiental/PBR |
| 4 | Prizma (60 MB) o Actions & Stuff (63.5 MB) | Validar el contenido más complejo después de verificar la ruta básica |
| 5 | Vibrance (122.4 MB) y Dark Visuals (157.7 MB) | Dejar para después por su tamaño descomprimido; evaluar memoria/tiempo de proceso |

No ejecutar todos los packs simultáneamente en la primera sesión. Inspector debe ser una prueba de validación por archivo, no un intento de importar todos a la vez.

### 5.3 Prueba de compatibilidad inicial en Minecraft

Después de registrar la validación inicial, usar un mundo de prueba y un conjunto pequeño de packs:

**Prueba A — prioridad ambiental:** Vibrant Visuals activo + Actions & Stuff SP0 (Custom Experience) + Lush Visuals. No activar otros packs visuales. Capturar la misma escena y comprobar si la atmósfera cambia sin perder los efectos/animaciones de A&S.

**Prueba B — ejemplo de compatibilidad explícita:** Actions & Stuff activo + Chroma Visuals en una opción “A&S Compatibility”. Seguir el orden que declara Chroma —por encima de A&S— y no activar ese modo si A&S no está activo. Es una prueba del patrón de otro creador, no un componente que se vaya a copiar.

**Prueba C — contraste de modo:** Actions & Stuff SP0 + Prizma VANILLA o Dark Visuals Classic, un pack a la vez. Evitar los modos PLUS/Enhanced en el primer ciclo.

Registrar versión de Minecraft; Vibrant Visuals; orden exacto; subpack de A&S; subpack del pack visual; escena/hora/bioma; capturas; errores en consola/Inspector; textura/modelo/animación que falla; y cualquier medida de rendimiento.

### 5.4 Matriz de resultados

La compatibilidad no se considera aprobada hasta que se haya probado:

- Importación/validación en Inspector.
- Activación en Minecraft con Vibrant Visuals disponible.
- Texturas de bloques y materiales PBR.
- Agua y lava.
- Jugador, herramientas/objetos 3D y armadura cuando A&S esté activo.
- Mobs y sus animaciones, especialmente al elegir SP0 frente a SP2.
- Ausencia de errores visuales como texturas rosas, modelos invisibles, duplicación de efectos o animaciones rotas.
- Estabilidad aceptable en el iPhone, medida por separado; no se estima FPS a partir del tamaño del archivo.

## 6. Implicaciones para Hogar Dorado

### Ruta sugerida

**Modo 1 — Hogar Dorado Ambiental:** iluminación, atmósfera, niebla, color grading, agua y ajustes PBR que sean necesarios; no sustituir modelos/animaciones/attachables de mobs de A&S. Será el primer modo a validar.

**Modo 2 — Acentos Emisivos Propios:** probar emisión de mobs por separado. Gólem y piglin zombificado requieren máscaras pequeñas; para Enderman debemos revisar la región emisiva ya existente antes de tocarla. Si las texturas de A&S usan archivos o UV diferentes, hará falta un método específico para esa experiencia y no debemos afirmar que el modo ambiental incluye esos acentos.

**Modo 3 — Compatibilidad probada:** solo después de tener una combinación reproducible en iPhone, documentar qué versión de Actions & Stuff, qué subpack, qué orden y qué versión de Minecraft han sido probados. No prometer compatibilidad universal con todos los packs.

No hace falta crear un parche específico de A&S si Hogar Dorado no altera la definición de jugador/mobs; la compatibilidad debe ganarse con una lista mínima de recursos modificados. Si más adelante Hogar Dorado añade VFX que dependen de una definición de entidad, se deberá considerar un subpack de compatibilidad explícito.

### Lo que no vamos a hacer

- No copiar los archivos de terceros a GitHub.
- No incorporar código, texturas o definiciones de otros packs al producto público sin permiso/licencia compatible.
- No cambiar varias categorías al mismo tiempo y después intentar atribuir los resultados a una sola.
- No dar por validada la compatibilidad usando solamente la documentación o la estructura del ZIP.
- No modificar ni reemplazar la referencia histórica v13 congelada.

## 7. Raíces del proyecto que se mantienen

- **Identidad:** “Una nostalgia que parece un sueño”.
- **Atmósfera sobre espectáculo:** la iluminación acompaña al mundo en vez de competir con todo.
- **Contraste selectivo:** luces cálidas, sombras con información, noches índigo y un End de carácter propio.
- **Fidelidad a Minecraft:** el juego debe seguir siendo reconocible.
- **Compatibilidad como atributo comprobable:** no es un reclamo publicitario sin pruebas; tiene versiones, modos, órdenes y resultados reproducibles.
- **Investigación de la comunidad:** las observaciones de Ángel orientan hipótesis; el tamaño de la demanda debe medirse antes de declarar una conclusión comercial.
- **Trazabilidad:** separar hechos del archivo, inferencias y resultados que solo una prueba en juego puede confirmar.

## 8. Fuentes y límites

### Fuentes oficiales externas

- Mojang — Minecraft Creator Tools, repositorio: https://github.com/Mojang/minecraft-creator-tools
- Microsoft Learn — Minecraft Creator Tools overview: https://learn.microsoft.com/es-mx/minecraft/creator/documents/mctoolsoverview?view=minecraft-bedrock-experimental
- Microsoft Learn — rendimiento y uso de recursos: https://learn.microsoft.com/en-us/minecraft/creator/documents/practices/improvingperformanceandresourceusage?view=minecraft-bedrock-stable
- Microsoft Learn — Vibrant Visuals resource packs y PBR: https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/vvresourcepacks?view=minecraft-bedrock-stable

### Evidencia propia de la auditoría

- Inventario de ocho archivos aportados: manifiestos, nombres localizados, tamaños, conteos de recursos, modos de subpack y carpetas relevantes.
- Textos integrados de Actions & Stuff 1.12 y del selector de Chroma Visuals.
- Inspección de las configuraciones ambientales y PBR representativas de los packs.

### Límites

- No se ha ejecutado el Inspector web en esta sesión y no existe todavía un informe suyo. Los pasos de la sección 5 son el protocolo para la primera prueba.
- No se ha probado ninguna combinación de esos paquetes en Minecraft durante esta auditoría.
- Los recuentos de rutas compartidas son estáticos y normalizan variantes de subpack; no prueban conflictos en ejecución.
- El estudio de comentarios de comunidad compartido por Ángel es una observación cualitativa. No tenemos todavía una muestra cuantitativa, análisis de ventas/descargas o estimación del mercado.
- No se midieron FPS, temperatura, memoria ni batería.

## Decisión

La primera combinación a probar será **Vibrant Visuals + Actions & Stuff SP0 + Lush Visuals**, con el Inspector de Creator Tools utilizado antes de entrar al mundo. Después se comparará la variante A&S de Chroma Visuals para aprender su patrón de compatibilidad. En paralelo, las pruebas emisivas de Hogar Dorado seguirán aisladas del trabajo ambiental; primero máscara MERS de ojos del gólem y piglin zombificado y, por separado, ajuste de la emisión ya marcada del Enderman.

El recurso público de Hogar Dorado no recibirá archivos binarios de estos packs de terceros. Todos los hallazgos confirmados, las decisiones y los límites deben permanecer registrados aquí para que otro colaborador pueda retomar el trabajo.


## 9. Anexo posterior — primer paquete aislado y paso inmediato de Inspector

Tras completar el inventario se preparó, fuera del repositorio, el paquete personal `hogar-dorado-v20-A-golem-ojos-PRUEBAS-PERSONALES.mcpack`. Cambia únicamente dos píxeles emisivos de los ojos del gólem frente a v19 y mantiene fuera del cambio los piglins, Enderman, lava y perfiles ambientales.

Validación estática comprobada localmente:
- ZIP íntegro; 178 archivos.
- 120 JSON interpretados sin errores de sintaxis.
- Manifiesto y módulo de recurso en versión 1.20.0, UUIDs únicos.
- TGA MERS de 128×128 con solo las dos posiciones objetivo modificadas.
- SHA-256: `00de46bc9fa3ca216e6e6138dc352c8e8d3b9fa349979ff79d417d7025c14a33`.

Este control estático no valida las reglas de Creator Tools ni el aspecto real en juego. En la primera sesión con el sitio conviene validar primero **Lush Visuals** como muestra pequeña del material aportado y después v20-A. Para cada uno: abrir mctools.dev, añadir/abrir el archivo MCPack, entrar en Inspector, ampliar el conjunto desde Focused a Full/Raw si los resultados parecen incompletos, exportar CSV/HTML si aparece la opción y anotar errores frente a avisos/recomendaciones. La guía oficial confirma que la validación del sitio admite MCPack y que los archivos se procesan localmente en el navegador: https://learn.microsoft.com/es-mx/minecraft/creator/documents/mctoolsoverview?view=minecraft-bedrock-experimental.

A día de esta actualización no se ha ejecutado Inspector ni se ha probado v20-A dentro de Minecraft. No registrar un resultado como aprobado hasta recibir el informe y las capturas de la prueba real.


## 10. Resultados reales del Inspector — 15 de octubre de 2026

Ángel compartió los informes HTML de **Lush Visuals** y **Hogar Dorado v20-A**. A partir de aquí el estado cambia de “Inspector pendiente” a “primera ronda ejecutada”; estos informes son validaciones estáticas, no pruebas de renderizado dentro de Minecraft.

### 10.1 Lush Visuals — reporte

- Tamaño descomprimido: 1,876,410 bytes (≈1.79 MiB decimal/≈1.79 MiB); 1,853 archivos, 897 texturas, 88 archivos de bioma, 22 nieblas, 7 configuraciones de iluminación y 776 Texture Sets.
- Recuento de Inspector: 683 errores, 41 advertencias; 39 comprobaciones exitosas y 4 comprobaciones fallidas.
- Las cuatro comprobaciones fallidas se distribuyen así:

| Regla | Conteo | Interpretación |
|---|---:|---|
| NOBOM — Byte Order Marks | 551 | Muchos JSON, especialmente Texture Sets, contienen BOM. El parser de JSON reporta cero errores de sintaxis, pero la regla NOBOM falla y los archivos no siguen la regla estricta del Inspector. |
| TEXTURELIST | 103 | El validador informa que imágenes asociadas con Texture Sets no deben estar registradas como entradas en `texture_list.json`. No significa que debamos borrar los mapas MERS: el problema indicado está en la lista. |
| PATHLENGTH | 27 | Las rutas completas del informe incluyen el directorio raíz derivado del nombre largo del archivo importado (“Visuales exuberantes (resources)(1)”). Hay que repetir con un nombre corto antes de atribuir todos estos errores al contenido interno del pack. |
| UNKJSON | 2 | Uno es `local_lighting/local_lighting.json`, ruta reconocida por la documentación oficial de Vibrant Visuals; el otro es `pdr/global.json`, que no coincide con la ruta oficial `pbr/global.json` y parece un posible error tipográfico. |

- Advertencias: 16 sobre la propiedad `weather` dentro de `volumetric.density`; 7 sobre valores MERS de tipo array; 13 archivos de textura sin referencias detectadas; 4 advertencias acerca de texturas de tablas de flechas/forja que podrían verse afectadas en una actualización futura; y 1 aviso de presupuesto de mip más alto en `textures/caustics.png` (el reporte indica 6.3 MB frente a un presupuesto de 4 MiB).

**Contraste con documentación oficial actual:** los documentos oficiales de niebla admiten `weather` dentro de `minecraft:fog_settings.volumetric.density`; las guías de Texture Sets admiten un array de tres valores numéricos para MER uniforme; y Microsoft documenta `local_lighting/local_lighting.json`. Por tanto, las advertencias/falla de “JSON desconocido” asociadas con esos elementos requieren distinguir entre el esquema de validación de la herramienta y las funciones soportadas por la versión objetivo. Las dos primeras son indicios de validación desactualizada, no razones suficientes para borrar propiedades válidas de los archivos. El caso de `pdr/global.json` sí merece revisión de la ruta.

Fuentes: https://learn.microsoft.com/en-us/minecraft/creator/reference/content/fogsreference/fogs?view=minecraft-bedrock-stable ; https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/lightingcustomization?view=minecraft-bedrock-stable ; https://learn.microsoft.com/en-us/minecraft/creator/documents/backups/texturesetsreference/texturesetsconcepts/texturesetsintroduction

### 10.2 Hogar Dorado v20-A — reporte

- Tamaño descomprimido: 459,602 bytes; 176 archivos y 57 texturas.
- Capacidad `pbr` detectada; un manifiesto de resource pack; cero errores de parseo JSON, cero errores de manifiesto, cero dependencias faltantes y cero errores de procesamiento interno reportados.
- Recuento de Inspector: 20 errores, 21 advertencias; 39 comprobaciones exitosas, 1 comprobación fallida y 14 no aplicables.
- El único test fallido es `PATHLENGTH`, con 20 rutas. En el campo absoluto, el Inspector antepone `/hogar-dorado-v20-A-golem-ojos-PRUEBAS-PERSONALES/` al recurso y el total supera 100 caracteres. La longitud se ve fuertemente influida por el nombre largo con que se cargó el archivo; la ruta interna relativa es más corta.
- Las 21 advertencias son: 19 instancias de `weather` en `volumetric.density`, 1 aviso sobre el objeto `subpacks[0]` del manifiesto y 1 sobre el formato de keyframes en `lighting/global.json`.

**Contraste con documentación oficial actual:**
- `weather` dentro de la densidad volumétrica aparece en el esquema oficial de niebla.
- El manifiesto de subpacks documentado por Microsoft utiliza objetos con campos `folder_name`, `name` y `memory_tier`; por tanto, el aviso que espera una cadena en vez de un objeto no coincide con el formato oficial de subpacks.
- `lighting/global.json` tiene `format_version: 1.26.0`; la documentación oficial indica que `sky.intensity` admite keyframes a partir de 1.26.0, así que el aviso necesita clasificarse como una discrepancia del validador frente al esquema actual.

Fuentes: https://learn.microsoft.com/en-us/minecraft/creator/documents/buildingsubpacks?view=minecraft-bedrock-stable ; https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/lightingcustomization?view=minecraft-bedrock-stable ; https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/keyframejsonsyntax?view=minecraft-bedrock-stable

### 10.3 Acción inmediata para repetir la prueba v20-A

Se creó una copia exacta del mismo archivo llamada `HD-v20A.mcpack` para volver a abrirlo en Inspector con un nombre corto. No se modificó el contenido del paquete: el SHA-256 sigue siendo `00de46bc9fa3ca216e6e6138dc352c8e8d3b9fa349979ff79d417d7025c14a33`. Así podremos comprobar si desaparece la única comprobación fallida sin introducir cambios en los JSON ni en las texturas.

No se deben “arreglar” a ciegas las 21 advertencias de v20-A: primero se confirma la discrepancia de esquema con las fuentes oficiales, y se mantiene la prueba como experimento visual aparte. Después de repetir Inspector, todavía hace falta cargar el pack en Bedrock 26.52 y comprobar los ojos del gólem; el informe estático no confirma que se vean ni demasiado brillantes ni lo suficientemente visibles.

### 10.4 Lecciones para nuestra herramienta de construcción

- Nombrar los paquetes de prueba con nombres cortos (`HD-v20A.mcpack`, no un nombre largo con explicación completa) antes de ejecutar validadores que incorporan el directorio importado a la ruta.
- Aceptar que un test puede no reconocer propiedades o formatos recientes, pero validar cada falso positivo contra la documentación oficial. No quitar `weather`, keyframes válidos o subpacks con formato oficial solo para bajar el contador.
- En los futuros archivos JSON creados por nosotros, usar UTF-8 sin BOM y evitar entradas de `texture_list.json` para las imágenes de Texture Set cuando la regla lo prohíba.
- Mantener separados tres estados: (1) sintaxis/estructura estática correcta, (2) reglas del Inspector satisfechas, y (3) aspecto visual/compatibilidad probada en Minecraft. No confundirlos.

 
## 10.5 Segunda ejecución de Inspector — alias corto `HD-v20A.mcpack` (15 de octubre de 2026)

Ángel compartió el nuevo informe HTML generado después de reabrir la copia con nombre corto. Esta segunda ejecución confirma que el único fallo anterior de v20-A dependía del nombre largo de importación:

| Indicador | Primer informe (`hogar-dorado-v20-A-golem-ojos-PRUEBAS-PERSONALES`) | Segunda ejecución (`HD-v20A.mcpack`) |
|---|---:|---:|
| Errores | 20 | **0** |
| Advertencias | 21 | 21 |
| Pruebas exitosas | 39 | **40** |
| Pruebas fallidas | 1 | **0** |
| No aplicables | 14 | 14 |
| Errores de procesamiento interno | 0 | 0 |

- La comprobación `PATHLENGTH` deja de fallar: el nuevo informe no registra errores y muestra cero errores de procesamiento interno, cero errores de parseo JSON, cero manifiestos inválidos y cero dependencias faltantes.
- El informe nuevo registra 177 archivos y 460,020 bytes de contenido descomprimido. Son los conteos del Inspector de esta ejecución; los inventarios locales anteriores usan su propio criterio de conteo y se conservan como observaciones separadas.
- Las 21 advertencias se distribuyen en 19 casos `COMJSON131` para la propiedad `weather` dentro de `minecraft:fog_settings.volumetric.density`, un caso `JSON114` sobre `subpacks[0]` en el manifiesto y un caso `JSONF110` en `lighting/global.json` para `minecraft:lighting_settings.sky.intensity`.
- La documentación oficial actual de niebla incluye el campo `weather` en densidad volumétrica; el formato de subpacks usa objetos que contienen `folder_name`, `name` y `memory_tier`; y el esquema de keyframes permite `sky.intensity` keyframed desde `format_version` 1.26.0. Por eso estos 21 avisos se mantienen clasificados como posibles discrepancias de esquema del validador, no como defectos confirmados de los archivos.
- El nuevo informe enumera componentes de bioma y la capacidad PBR. Esa enumeración no es prueba de renderizado ni de compatibilidad con otros packs.

**Conclusión técnica:** la validación estática de v20-A queda aprobada en la ronda actual del Inspector usando el nombre corto: 0 errores y 0 fallos. Las 21 advertencias permanecen registradas sin cambios de contenido. Esto no constituye aprobación artística, verificación del brillo de los ojos, prueba de FPS ni certificación de compatibilidad con Actions & Stuff.

### Siguiente prueba

1. Importar `HD-v20A.mcpack` en Minecraft Bedrock 26.52.
2. Comparar la misma escena con v20-A, v19 y la referencia histórica v13 sin modificar la referencia; registrar packs activos, orden, bioma, hora y capturas.
3. Evaluar si los ojos se perciben sutilmente emisivos, si el modelo conserva el aspecto original y si la diferencia resulta visible sin exageración.
4. No empezar aún los cambios de piglin zombificado o lava; esperar la evidencia visual de esta máscara aislada.
