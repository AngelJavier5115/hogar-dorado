# Investigación II — Técnicas avanzadas, arquitectura visual y auditoría de referencias

**Fecha:** 2026-10-15  
**Estado:** investigación y propuestas; pendientes de decisión, implementación y prueba.  
**Contexto objetivo declarado por Ángel:** Minecraft Bedrock 26.52, iPhone 14 Plus. Confirmar el comportamiento en esa instalación exacta.

> **Objetivo:** encontrar técnicas adicionales en la documentación y en los packs de referencia que puedan mejorar Hogar Dorado, simplificar su mantenimiento o evitar errores. Este informe amplía la investigación PBR anterior con auditoría interna de referencias, iluminación local coloreada, perfiles por bioma, subsurface scattering, cubemaps y validación reproducible.

## 1. Hallazgo prioritario: separar emisión de textura e iluminación local coloreada

Esta es la oportunidad técnica más importante de esta segunda ronda.

### Dos efectos distintos

**Emisión de material (Texture Set / MER o MERS):**
- Controla qué partes de una superficie se representan como emisivas.
- Es ideal para detalles visibles: vetas de redstone, ojos de criaturas, cristales o píxeles de una textura que queremos destacar.
- No debe confundirse con una promesa de que el material iluminará dinámicamente toda la escena.

**Iluminación local (local_lighting/local_lighting.json):**
- Asocia un color y un tipo de luz a bloques concretos.
- static_light es una iluminación más sencilla, fija y precalculada; no crea los mismos reflejos especulares ni sombras dinámicas que una point light.
- point_light produce efectos de iluminación más sofisticados, pero la documentación indica que consume considerablemente más recursos. Conviene usarlo con cuidado.
- El alcance de luz del bloque es un concepto distinto de la emisión PBR de la textura.

Fuente principal: [Microsoft Learn — Light Sources](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/lightingcustomization?view=minecraft-bedrock-stable).

### Evidencia de los packs compartidos por Ángel

Auditoría estática de los archivos locales:

| Pack | Entradas en local_lighting.json | point_light | static_light | Colores RGB únicos contados |
|---|---:|---:|---:|---:|
| BSLB Dreamy Visuals v1.0.4 | 101 | 60 | 41 | 53 |
| Solar Shaders Visuals v2.0.3 | 109 | 62 | 47 | 69 |
| Dark Fantasy Visuals | No encontrado | — | — | — |

Ejemplos presentes en BSLB y Solar:
- Antorchas y faroles: luz puntual con colores cálidos.
- Antorchas y linternas de almas: luz azul/cian.
- lit_redstone_ore y lit_deepslate_redstone_ore: color rojo en una entrada de iluminación estática.
- Glowstone, shroomlight, lava y otros emisores: entradas de luz estática con colores cálidos.

La presencia de estas entradas confirma que ambos packs utilizan iluminación local además de sus Texture Sets PBR. No certifica que cada entrada funcione en la versión objetivo ni que sus valores sean apropiados para Hogar Dorado.

### Cambio documentado por Mojang/Microsoft

Las notas para creadores de Bedrock 1.26.10 indican que la iluminación estática/colorizada en Vibrant Visuals dejó de estar restringida a Preview; un resource pack PBR la puede activar incluyendo local_lighting.json con entradas static_light. Las mismas notas registran una limitación conocida sobre colores que no se propagan en chunks vacíos. Comprobar el soporte real en la versión de Ángel antes de incorporarlo al pack.

Fuente: [Minecraft Bedrock 1.26.10 — Creator Update Notes](https://learn.microsoft.com/en-us/minecraft/creator/documents/update1.26.10?view=minecraft-bedrock-stable).

### Propuesta para Hogar Dorado

Crear un sistema pequeño de colores reutilizables, en lugar de dar un color exclusivo a cada bloque:
- **Oro cálido:** fuentes de luz domésticas, antorchas o faroles.
- **Azul de almas/índigo:** iluminación de almas o escenas que necesiten un contraste frío.
- **Rojo de redstone:** solo bloques encendidos pertinentes, con una intensidad visual legible.
- **Violeta de amatista:** cristales, si una prueba confirma que encaja con la paleta.
- **Verde/agua:** reservarlo para elementos que lo justifiquen, sin convertir toda el agua en luz artificial.

La paleta es una hipótesis creativa, no una selección final de valores RGB. La documentación oficial recomienda reutilizar colores en varios bloques cuando sea posible, porque una cantidad reducida de colores compartidos puede ayudar al rendimiento. Evitar implementar todos los colores de golpe.

**Experimento recomendado:**
1. Elegir una sola fuente cálida (por ejemplo, farol) para probar point_light.
2. Elegir un bloque de superficie emisiva, o la redstone encendida, para probar static_light más emisión de textura donde proceda.
3. Comparar visualmente en una cueva y al aire libre.
4. Observar paredes, esquinas, zonas de transición y chunks recién cargados.
5. Registrar si el resultado aporta ambiente, si distrae y si aparece un error; no inferir rendimiento a partir del JSON.

No confundir color de iluminación con la emisión de los píxeles de una textura ni con la luz de juego usada por mecánicas como la aparición de mobs.

## 2. Aprovechar perfiles compartidos por bioma en vez de inventar decenas de estilos

La personalización por bioma no obliga a escribir valores ambientales completamente distintos para cada bioma. Los archivos de bioma pueden referenciar identificadores de perfiles compartidos (atmósfera, iluminación, gradación de color, agua y cubemap, según corresponda).

Microsoft documenta que, desde Bedrock 1.21.90, los ajustes de Vibrant Visuals por bioma tienen precedencia sobre los defaults globales y sobre archivos globales del pack. También documenta que el motor mezcla progresivamente atmósfera, gradación, cubemap e iluminación al pasar entre biomas; el agua se mezcla según la ubicación de la geometría, no solo la posición del jugador.

Fuente: [Microsoft Learn — Biome Customization](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/biomecustomization?view=minecraft-bedrock-stable).

### Lo observado en los packs

| Pack | Archivos de bioma examinados | Perfiles de atmósfera referenciados | Perfiles de iluminación referenciados | Perfiles de gradación referenciados |
|---|---:|---:|---:|---:|
| BSLB Dreamy Visuals | 89 | 6 | 8 únicos | 6 únicos |
| Solar Shaders Visuals | 89 | 6 | 7 | 5 únicos |
| Dark Fantasy Visuals | 87 | 11 | 8 únicos en los archivos inspeccionados* | 11 |

*Los resultados de Dark Fantasy son de inspección de archivos; algunos JSON contienen comentarios, así que su validación final debe hacerse con herramientas compatibles con los formatos de Bedrock. No interpretar este conteo como certificado de que todos los identificadores cargan correctamente.*

El patrón útil es que los dos packs modernos examinados comparten un número relativamente pequeño de perfiles visuales a través de cerca de 90 archivos de bioma. Eso facilita una dirección artística coherente y evita inventar una iluminación completamente nueva por bioma.

### Propuesta de arquitectura para Hogar Dorado

Definir primero entre cinco y siete **familias visuales**, que luego se asignan a biomas concretos:
- Familiar/cálido (llanuras y praderas).
- Bosque dorado (bosques y el bosque moteado).
- Fresco/sereno (taigas y biomas fríos).
- Húmedo/profundo (pantanos, costas o biomas exuberantes, según pruebas).
- Mineral/subterráneo (cuevas).
- Nether (calor y peligro).
- End (vacío e inmensidad).

Son categorías de trabajo, no un mapa final. Un mismo perfil puede compartirse entre biomas y ajustarse solo cuando la diferencia tenga un motivo perceptible.

### Restricciones que conviene automatizar

La documentación de mezcla por bioma señala restricciones actuales:
- Los operadores de tone mapping no se mezclan; usar el mismo operador en todos los perfiles de color del pack.
- orbital_offset_degrees no se mezcla; mantener el mismo valor en los perfiles de iluminación.
- Los parámetros de cáusticas del agua no se mezclan.
- El booleano que activa/desactiva las olas tampoco se mezcla.

Estas son candidatas excelentes para un chequeo automático previo a cada beta.

## 3. Nuevos biomas recientes: regresión obligatoria

El archivo biomas-bedrock.md de Hogar Dorado registra el bosque moteado (dappled_forest) y las cuevas de azufre (sulfur_caves) como biomas recientes en el contexto de la versión que Ángel está usando.

En los archivos de referencia inspeccionados:
- **BSLB:** incluye biomes/dappled_forest.client_biome.json y biomes/sulfur_caves.client_biome.json.
- **Solar:** incluye ambos archivos.
- **Dark Fantasy:** no contiene esos dos archivos, algo coherente con que el archivo de referencia proviene de una generación anterior.

BSLB y Solar asignan al bosque moteado colores de hierba/follaje de otoño y niebla con identificador específico; para las cuevas de azufre usan perfiles de niebla más generales. Esto demuestra una técnica de integración sencilla: crear la entrada de bioma y referenciar perfiles existentes cuando no se necesita un sistema nuevo.

### Recomendación

Mantener una lista de cobertura con:
- Todos los biomas activos de la versión objetivo.
- Identificador de Bedrock exacto.
- Perfil ambiental que se asigna.
- Si requiere una prueba visual propia.
- Versión en la que se verificó.

No añadir una entrada para un bioma que todavía aparezca solo en Preview o cuyo identificador no esté confirmado en la instalación objetivo. En biomas-bedrock.md, las cuevas de hielo se marcan como pendientes/no confirmadas; conservar ese estado hasta verificar su disponibilidad e ID exacto.

## 4. PBR fallback: definir una base, no crear textura para todo

pbr/global.json permite proporcionar valores PBR de respaldo para bloques, actores, partículas e ítems cuando no existe un Texture Set específico. Es útil para establecer una dirección general y después añadir detalle por recurso cuando realmente lo necesite.

Fuente: [Microsoft Learn — Light Sources / PBR uniforms](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/lightingcustomization?view=minecraft-bedrock-stable).

### Lo observado
- BSLB y Solar usan pbr/global.json con valores de fallback y una emisión de base baja (valor de canal 10 en los arrays inspeccionados).
- Dark Fantasy usa emisión de base 0 en los valores inspeccionados y deja su brillo representado en recursos concretos.

No se puede concluir que un enfoque sea superior: ambos reflejan decisiones diferentes de dirección artística.

### Propuesta para Hogar Dorado
- Usar un fallback prudente, sin emisión global innecesaria.
- Mantener los brillos intencionales en Texture Sets específicos que se hayan decidido conservar.
- Revisar bloques, entidades, partículas e ítems por separado: un mismo fallback para todas las categorías puede provocar resultados distintos a los deseados.
- Evitar crear docenas de Texture Sets solo para fijar valores que pueden expresarse como fallback uniforme.
- No usar el fallback como sustituto de texturas de color ausentes en un Texture Set que las necesita.

La decisión de valores debe tomarla Claude tras revisar los archivos de la beta y compararla con la referencia v13, no copiar valores exactos de otra marca.

## 5. Subsurface scattering: una posible herramienta para la luz atravesando follaje

Vibrant Visuals admite **subsurface scattering** (dispersión subsuperficial), que simula luz atravesando superficies translúcidas como hojas, cera o piel. Requiere el formato de cuatro canales MERS; la documentación señala que metalness y subsurface no pueden dominar simultáneamente el mismo píxel/valor.

Fuente: [Microsoft Learn — Subsurface Scattering](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/subsurfacescatteringcustomization?view=minecraft-bedrock-stable).

Este efecto podría dar una sensación de luz más suave atravesando hojas en amaneceres y atardeceres, y quizá pétalos o vegetación seleccionada. Encaja con la idea de una luz dorada que baña el mundo, pero el exceso podría cambiar la lectura de las texturas y hacer que el follaje parezca plástico o demasiado brillante.

**Prueba pequeña recomendada:** un tipo de hoja, una escena con el sol a contraluz, otra en sombra, un valor bajo y otro moderado. Comparar contra la versión sin SSS y comprobar que la textura sigue pareciendo Minecraft.

Estado: exploración futura. No aplicar a todo el follaje de golpe ni mezclar con emisiones fuertes en el mismo paso.

## 6. Keyframes para una transición diaria deliberada

La configuración de Vibrant Visuals permite animar ciertos valores con keyframes. Para el tiempo del juego, la documentación usa valores de 0 a 1 para el ciclo completo: mediodía = 0.0, atardecer ≈ 0.25, medianoche = 0.5, amanecer ≈ 0.75 y el siguiente mediodía = 1.0. En los parámetros admitidos, la interpolación es lineal.

Fuente: [Microsoft Learn — Key Frame JSON Syntax](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/keyframejsonsyntax?view=minecraft-bedrock-stable).

No es necesario hacer que un efecto cambie bruscamente al llegar cierta hora. Podemos colocar puntos que permitan un descenso gradual de luz, cambiar el color del sol cerca del atardecer y hacer que la noche se vuelva índigo. La posición exacta de los puntos debe derivarse de pruebas visuales, no copiarse de un pack ajeno.

**Cuidado:** ciertos valores no admiten keyframes o tienen límites propios. Verificar en el esquema oficial qué propiedades de la versión objetivo admiten la sintaxis.

## 7. Cubemap: idea interesante, no la primera prioridad

Un cubemap es un cielo envolvente compuesto por seis texturas 2D. BSLB incluye cubemaps/default_cubemap.json y seis imágenes de cubemap dentro de subpacks/bl/textures/environment/overworld_cubemap/; los otros dos packs inspeccionados no incluyen esa misma configuración.

La documentación oficial explica que la personalización de cubemaps descrita aquí solo está disponible en el Overworld; el End conserva su cubemap incorporado. Por ello, un cubemap no es la forma correcta de sustituir el cielo del End. Para la identidad del End deben investigarse primero la atmósfera, la niebla, la iluminación y la gradación de color.

Fuente: [Microsoft Learn — Cubemaps](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/cubemapcustomization?view=minecraft-bedrock-stable).

Dejarlo como prueba opcional de fase posterior: comparar cielo vanilla + atmósfera contra una propuesta de cielo envolvente muy sutil para el Overworld. No añadir un cubemap solo porque otro pack lo usa; las seis caras, su continuidad visual, el coste de recursos y la coherencia con el cielo existente deben justificarlo.

## 8. Subpacks: aprender de variantes completas y de pequeños overrides

### Evidencia observada

**BSLB**
- El manifiesto describe variantes de resolución y de nubes.
- Dos carpetas (subpacks/vh y subpacks/bh) contienen la mayor parte de los recursos de alta resolución.
- subpacks/bl contiene solo once entradas contando directorios, principalmente clouds.png y seis caras del cubemap. Es un ejemplo de una variante que modifica un subconjunto muy pequeño de recursos en vez de replicar todo el contenido.
- El manifiesto usa memory_tier. No copiar esta sintaxis sin verificar el esquema vigente.

**Solar**
- Tiene una carpeta de subpack subpacks/1 con 2.576 entradas, además de un conjunto base más pequeño.
- Su estructura es distinta de BSLB; no asumir que ambas variantes implementan la misma estrategia.

Documentación oficial: [Microsoft Learn — Building Sub-Packs](https://learn.microsoft.com/en-us/minecraft/creator/documents/buildingsubpacks?view=minecraft-bedrock-stable).

No empezar con muchos perfiles. Primero construir y probar una versión base; crear un subpack únicamente si resuelve una necesidad demostrada. Si luego se desea una variante de resolución o nube, diseñarla como un conjunto explícito de diferencias y comprobar el resultado en el juego.

La documentación vigente distingue memory_tier del campo nuevo memory_performance_tier y señala condiciones relacionadas con el formato de manifiesto. Revisar el esquema actual antes de adoptar la estructura de una versión ajena.

## 9. Auditoría estática de los recursos de referencia

Se hizo una inspección automatizada de los archivos ZIP/MCPack locales. Esta comprobación es deliberadamente limitada y no ejecutó Minecraft.

| Pack | Entradas de archivo | Tamaño descomprimido aproximado | Texture sets encontrados | Referencias a recursos comprobadas |
|---|---:|---:|---:|---|
| BSLB Dreamy Visuals v1.0.4 | 10.794 | 30,44 MiB | 2.643 | Sin referencias ausentes en los campos examinados |
| Solar Shaders Visuals v2.0.3 | 2.854 | 4,85 MiB | 682 | Sin referencias ausentes en los campos examinados |
| Dark Fantasy Visuals | 1.739 | 32,57 MiB | 19 | Sin referencias ausentes en los campos examinados |

La comprobación busca que los Texture Sets examinados declaren color y que sus rutas de color, MER/MERS, normal o heightmap apunten a archivos reconocibles en la ubicación esperada. Los resultados no garantizan esquema válido, carga correcta, ausencia de warnings, compatibilidad con el juego ni calidad visual.

### Una lección importante de tamaño

Dark Fantasy tiene 1.379 archivos de audio .ogg, que contribuyen de forma importante al tamaño del pack. En contraste, BSLB y Solar concentran gran parte de sus entradas en texturas, mapas PBR y configuraciones visuales. Por ello, no se debe comparar optimización solo con el tamaño descomprimido: antes hay que clasificar recursos, estimar memoria/handles y medir el juego.

La documentación oficial advierte que incluso un archivo de audio comprimido puede producir presión de memoria al cargarse y que la cantidad de sonidos afecta el coste de búsqueda/caché. Para Hogar Dorado, la posibilidad de un paisaje sonoro se deja como investigación futura, no como requisito de la beta visual.

Fuente de rendimiento: [Microsoft Learn — Improving performance and resource usage](https://learn.microsoft.com/en-us/minecraft/creator/documents/practices/improvingperformanceandresourceusage?view=minecraft-bedrock-stable).

## 10. Herramientas para validar antes de abrir Minecraft

### Minecraft Creator Tools (opción oficial)

Mojang ofrece el sitio [mctools.dev](https://mctools.dev) y herramientas de línea de comandos. La documentación indica que su Inspector acepta MCPack, MCAddon y ZIP compatibles, ejecuta validaciones y puede exportar reportes CSV/HTML. También afirma que el procesamiento del sitio ocurre localmente en el navegador, sin enviar los archivos a un servidor.

Fuente: [Minecraft Creator Tools Overview](https://learn.microsoft.com/es-mx/minecraft/creator/documents/mctoolsoverview?view=minecraft-bedrock-experimental).

Como Ángel trabaja desde iPhone, probar primero si el flujo de Inspector acepta cómodamente el .mcpack en Safari. La documentación menciona expresamente Chrome/Edge para abrir carpetas locales directamente; por tanto, no se da por garantizada toda la experiencia de edición móvil. Si la validación en Safari no resulta viable, conservar la herramienta como opción de referencia y no como dependencia obligatoria.

### Validador propio para GitHub (más adelante)

Cuando la estructura fuente del pack esté organizada dentro del repositorio, estudiar con Claude un validador que compruebe automáticamente:
1. Sintaxis JSON según el formato admitido por Bedrock.
2. Presencia de los recursos referenciados en cada Texture Set.
3. Identificadores duplicados o identificadores no resueltos en archivos de bioma.
4. Que la capacidad pbr se declare cuando corresponde.
5. Que cada subpack declarado exista y sus overrides no estén incompletos.
6. Restricciones conocidas de mezcla entre perfiles (operadores de tone mapping, orbital offset y parámetros de agua).
7. Entradas de local_lighting y reutilización intencionada de colores.
8. Conteo de archivos, texturas, tamaños por dimensión y distribución por tipo de recurso.
9. Cobertura de los biomas activos de la versión objetivo.

El validador debe ser compatible con las convenciones reales de los JSON de Bedrock; evitar un parser demasiado estricto que reporte falsos positivos por sintaxis aceptada por el motor. No subir al repositorio texturas vanilla extraídas ni contenido de terceros sin permiso adecuado. Las guías y reportes técnicos sí pueden documentar lo aprendido.

## 11. Nuevos criterios de diseño derivados

- **Contraste por capas:** PBR/emisión para detalles de superficie; iluminación local coloreada para el entorno; color grading/atmósfera para la sensación global.
- **Colores con sistema:** pocas familias de iluminación, reutilizadas donde compartan función.
- **Excepciones con intención:** no todos los biomas necesitan perfiles exclusivos; crear un nuevo perfil solo si la diferencia aporta algo.
- **Luz a través de la vegetación:** SSS como experimento de baja prioridad visual, con comparación antes/después.
- **Ciclo diario gradual:** keyframes con valores elegidos alrededor de los hitos del día.
- **Compatibilidad viva:** comprobar los biomas nuevos y los identificadores internos de la versión de Bedrock objetivo.
- **Herramientas antes de gameplay:** inspección estructural primero; carga y apariencia real después.
- **No inflar la beta:** no añadir cubemap, audio o más perfiles por el mero hecho de que existan en otros packs.

## 12. Matriz de experimentos sugerida

| ID | Pregunta | Comparación mínima | Riesgo que se vigila | Estado |
|---|---|---|---|---|
| LOCAL-01 | ¿Un farol dorado mejora la atmósfera? | Luz vanilla vs point_light cálida | Dominancia visual, coste, artefactos | Pendiente |
| LOCAL-02 | ¿Redstone activada puede dar un matiz rojo controlado? | Vanilla vs static_light rojo + textura emisiva apropiada | Propagación de color, legibilidad, chunks | Pendiente |
| SSS-01 | ¿El sol se siente mejor atravesando hojas? | Sin SSS vs valor bajo vs moderado | Follaje plástico, exceso de brillo | Futuro |
| BIOME-01 | ¿Los cambios de bioma se perciben gradualmente? | Dos perfiles compatibles en biomas adyacentes | Cortes de color, incoherencia | Pendiente |
| DAY-01 | ¿El atardecer cuenta una historia visual? | Keyframes base vs propuesta ajustada | Cambio demasiado naranja o noche lavada | Pendiente |
| CUBE-01 | ¿Un cielo envolvente aporta algo al Overworld? | Cielo vanilla con atmósfera vs cubemap sutil | Coste, costuras, pérdida de identidad | Futuro |
| PACK-01 | ¿El Inspector detecta problemas antes del juego? | Un .mcpack de prueba con Creator Tools | Compatibilidad móvil del sitio | Pendiente |
| BIOME-02 | ¿Todos los biomas recientes están cubiertos? | Lista de cobertura vs versión instalada | Bioma con apariencia vanilla accidental | Pendiente |

Para cada prueba: anotar versión del pack, versión del juego, escena, ajuste visual, resultado observado y decisión. No marcar “validado” hasta comprobarlo en Minecraft.

## 13. Orden recomendado de trabajo

1. Claude compara esta investigación con el contenido real de la referencia v13 y con CAMBIOS.md.
2. Priorizar un solo experimento de iluminación local; no alterar todo PBR simultáneamente.
3. Hacer inventario de entradas local_lighting y confirmar qué tipos pueden usar point lights en la versión objetivo.
4. Revisar perfiles compartidos de biomas y comprobar dappled_forest y sulfur_caves.
5. Ejecutar validación estructural con Creator Tools cuando sea viable o con herramienta equivalente.
6. Probar los cambios en el iPhone y registrar capturas reales.
7. Explorar SSS/cubemap solo si la beta principal ya está estable.
8. Revisar con Ángel qué entra en la siguiente versión; la implementación sigue bajo control del responsable del proyecto.

## 14. Fuentes

- [Microsoft Learn — Light Sources](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/lightingcustomization?view=minecraft-bedrock-stable)
- [Microsoft Learn — Bedrock 1.26.10 Creator Update Notes](https://learn.microsoft.com/en-us/minecraft/creator/documents/update1.26.10?view=minecraft-bedrock-stable)
- [Microsoft Learn — Biome Customization](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/biomecustomization?view=minecraft-bedrock-stable)
- [Microsoft Learn — Subsurface Scattering](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/subsurfacescatteringcustomization?view=minecraft-bedrock-stable)
- [Microsoft Learn — Key Frame JSON Syntax](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/keyframejsonsyntax?view=minecraft-bedrock-stable)
- [Microsoft Learn — Cubemaps](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/cubemapcustomization?view=minecraft-bedrock-stable)
- [Microsoft Learn — Improving performance and resource usage](https://learn.microsoft.com/en-us/minecraft/creator/documents/practices/improvingperformanceandresourceusage?view=minecraft-bedrock-stable)
- [Microsoft Learn — Minecraft Creator Tools](https://learn.microsoft.com/es-mx/minecraft/creator/documents/mctoolsoverview?view=minecraft-bedrock-experimental)

## 15. Límites de esta investigación

- La inspección de paquetes fue estática: no ejecutó el juego ni midió FPS, memoria, temperatura o compatibilidad real.
- Los contadores de archivos y texturas dependen del método de inventario y son comparaciones técnicas preliminares, no una clasificación general de calidad de packs.
- No se han modificado recursos de Hogar Dorado, ni se afirma que los experimentos estén implementados.
- El soporte de iluminación colorizada debe verificarse en el build exacto que Ángel utiliza; las notas oficiales consultadas identifican su disponibilidad en Bedrock 1.26.10 estable y además describen una limitación conocida.
- Los packs de la comunidad son referencias de aprendizaje, no recursos que debamos copiar o volver a distribuir.
