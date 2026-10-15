# Investigación III — Compatibilidad, Minecraft Creator Tools y auditoría de v12

**Fecha:** 2026-10-15  
**Estado:** auditoría estática e investigación documental; sin cambios al resource pack.  
**Archivo local inspeccionado:** hogar-dorado-v12.mcpack (el archivo compartido en esta conversación).  
**Versión de Minecraft indicada por Ángel:** Bedrock 26.52.  
**Prioridad:** convertir los hallazgos en pruebas que Claude pueda ejecutar o contrastar cuando retome la referencia v13.

> **Límite de alcance:** esta auditoría describe exclusivamente el archivo v12 que se adjuntó aquí. No afirma nada sobre v13 ni sobre el contenido de referencia/cambios que Claude guardó posteriormente. Antes de cambiar archivos, Claude debe comparar estos hallazgos con v13 y con su registro de cambios.

## 1. Resumen ejecutivo

Esta ronda encontró tres oportunidades de gran valor:

1. **Compatibilidad con otros packs:** las definiciones PBR de una misma textura no se fusionan al combinar packs. La definición con mayor prioridad reemplaza a la de menor prioridad, y un Texture Set solo puede referenciar imágenes incluidas en su propio pack. Hogar Dorado necesita una estrategia de compatibilidad explícita si busca complementar otros resource packs.
2. **Agua por bioma:** v12 personaliza el color de agua en los archivos de bioma, pero su water settings usa el formato 1.21.80 y no define el parámetro introducido en 1.26.0, biome_water_color_contribution. La documentación indica que su valor predeterminado es 0, por lo que el color de agua de cada bioma no se mezcla en Vibrant Visuals salvo que se habilite ese aporte. Es una prueba prioritaria.
3. **Atmósfera e iluminación por bioma:** los 62 archivos client biome de v12 no incluyen identificadores explícitos para configuraciones de atmósfera o iluminación, aunque el pack tiene un archivo atmospherics global y otro lighting global. La documentación actual advierte que las configuraciones vanilla por bioma pueden tener prioridad sobre las configuraciones globales del pack. Hay que verificar si la intención de la v12 realmente se ve en cada bioma.

Además, la inspección de v12 encontró cuatro archivos MER, pero no encontró ningún archivo con extensión .texture_set.json. Eso hace necesario comprobar con Inspector y en juego si esos mapas se están usando como se pretende. No se debe asumir que las texturas se aplican por su nombre solamente.

## 2. Inventario estático de la v12 compartida

La inspección del MCPack, sin ejecutar Minecraft, dio estos datos:

| Elemento | Resultado |
|---|---|
| Tamaño comprimido del archivo | Aproximadamente 64,7 KiB |
| Número de entradas de archivo | 97 |
| Tamaño descomprimido total | Aproximadamente 114,3 KiB |
| Archivos client biome | 62 |
| Configuraciones de niebla | 19 |
| Perfiles de color grading propios | 2: hogar:golden y hogar:lush |
| Configuración de atmósfera | 1 global |
| Configuración de iluminación | 1 global |
| Configuración de agua | 1 global |
| Configuración de sombras | 1 global |
| Configuración local_lighting/local_lighting.json | No encontrada |
| Configuraciones *.texture_set.json | No encontradas |
| Imágenes MER/MERS | 4 archivos terminados en _mer.png |

El archivo manifest.json declara la capacidad pbr y min_engine_version [1, 26, 10]. Esto es coherente con el requisito documentado de usar la capacidad pbr para Vibrant Visuals y un engine version suficientemente reciente, pero no certifica por sí solo que el resto de los recursos esté bien configurado.

### Las cuatro imágenes MER en la v12

- lava_still_mer.png
- lava_flow_mer.png
- glowstone_mer.png
- sea_lantern_mer.png

La v12 también contiene lava_still.png y lava_flow.png. En este archivo adjunto no se encontraron texturas de color para glowstone o sea lantern ni archivos texture_set.json que relacionen colores con mapas MER.

La documentación oficial define los Texture Sets como archivos JSON y exige una capa color válida, además de que las imágenes referenciadas existan dentro del mismo pack. Por tanto, para esta copia v12, los mapas MER parecen carecer del vínculo explícito que la documentación muestra. Es un **hallazgo estático que debe confirmar Creator Tools o Minecraft**; Claude podría haber corregido esta situación en v13.

Fuente: [Microsoft Learn — Texture Set JSON and Introduction to Texture Sets](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/texturesetsreference/texturesetsconcepts/texturesetsintroduction?view=minecraft-bedrock-stable).

## 3. Descubrimiento prioritario: el parámetro nuevo para el agua

### Qué hay en v12

El archivo water/water.json tiene:
- format_version: 1.21.80.
- chlorophyll: 0.02.
- suspended_sediment: 0.04.
- cdom: 0.2.
- waves.enabled: false.
- caustics.enabled: true.
- No contiene biome_water_color_contribution.

Los 62 archivos client biome contienen un surface_color de agua cada uno. Sin embargo, la documentación de Vibrant Visuals indica que el color de agua en ese modo depende principalmente de las concentraciones de partículas y que, por defecto, los valores surface_color de los archivos client biome no se incorporan al agua de Vibrant Visuals.

### Qué cambió en Bedrock 1.26.0

La documentación del agua y las notas de actualización de 1.26.0 describen el campo biome_water_color_contribution, un valor entre 0.0 y 1.0 que controla cuánto se mezcla el surface_color de cada bioma con la base de agua de Vibrant Visuals. Su valor predeterminado es 0.0.

Fuentes:
- [Microsoft Learn — Water Effects](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/watercustomization?view=minecraft-bedrock-stable)
- [Microsoft Learn — 1.26.0 Update Notes](https://learn.microsoft.com/en-us/minecraft/creator/documents/update1.26.0?view=minecraft-bedrock-stable)

### Por qué importa para Hogar Dorado

La identidad acuática de Hogar Dorado cambia el surface_color por bioma. Si ese color no se incorpora en Vibrant Visuals, parte del trabajo de color definido en los client biome puede resultar menos perceptible que en el modo Classic.

### Experimento sugerido

No cambiar todos los parámetros del agua a la vez. Crear tres copias temporales de configuración o tres builds controlados:

| Variante | biome_water_color_contribution | Pregunta |
|---|---:|---|
| A — referencia | 0.0 (valor por defecto) | ¿Cómo se ve el agua actual? |
| B — aporte sutil | 0.25 | ¿El color del bioma se percibe sin parecer teñido artificialmente? |
| C — aporte fuerte | 0.60 | ¿Se conserva la claridad y la identidad de cada agua? |

El valor 1.0 puede añadirse si B y C muestran que tiene sentido, pero no hace falta empezar probando todos los extremos. Revisar al menos océano, río, pantano, bosque moteado y cuevas de azufre. Mantener las olas desactivadas y las caústicas como están durante esta prueba para aislar el efecto del nuevo parámetro.

Antes de implementar, Claude debe revisar el esquema oficial actual y comprobar que el formato de la configuración se actualiza de forma compatible para admitir el campo. Este informe no modifica water.json.

## 4. Configuración ambiental por bioma: ¿está llegando el estilo esperado?

### Lo que contiene la copia v12

Cada uno de los 62 archivos client biome tiene los siguientes cinco componentes:
- minecraft:fog_appearance
- minecraft:water_appearance
- minecraft:foliage_appearance
- minecraft:grass_appearance
- minecraft:color_grading_identifier

Ninguno de los 62 archivos declara:
- minecraft:atmosphere_identifier
- minecraft:lighting_identifier
- minecraft:water_identifier

Aun así, el pack contiene:
- atmospherics/atmospherics.json con identifier hogar:global_atmosphere.
- lighting/global.json con identifier hogar:global_lighting.
- water/water.json con identifier hogar:water.

Los perfiles hogar:golden y hogar:lush usan ambos el operador ACES en sus configuraciones de color grading, lo cual mantiene coherencia con la restricción documentada de que los operadores de tone mapping no se mezclan entre biomas.

### Riesgo que se debe verificar

Desde Bedrock 1.21.90, la documentación explica que las configuraciones Vibrant Visuals por bioma del pack vanilla tienen precedencia sobre los valores globales incorporados por un pack. Para que una configuración personalizada se aplique a un bioma determinado, el client biome debe referenciar los identificadores de los perfiles deseados.

Por eso, no basta con ver lighting/global.json y atmospherics/atmospherics.json dentro del pack: hay que confirmar en el juego que sus efectos se estén aplicando a los biomas previstos.

Fuente: [Microsoft Learn — Biome Customization](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/biomecustomization?view=minecraft-bedrock-stable).

### Propuesta

1. Comparar el Overworld de la v12 con Vibrant Visuals y sin él.
2. Comparar un bioma habitual (plains o forest), el dappled_forest, el Nether y el End.
3. Registrar si el ciclo de luz y el color de cielo cambian como se espera.
4. Si se confirma que el estilo global no alcanza ciertos biomas, estudiar cómo asignar identificadores de atmósfera y lighting a través de perfiles compartidos, sin copiar grandes configuraciones por separado.
5. Mantener los nombres de perfiles con namespace propio (hogar:...) para evitar colisiones con otros packs.

Esto no implica que haya que definir un perfil único para cada bioma. Se pueden compartir pocos perfiles de atmósfera e iluminación y asociarlos a muchos biomas con un catálogo de asignaciones.

## 5. Compatibilidad de Hogar Dorado con otros resource packs

Ángel quiere que Hogar Dorado complemente otros add-ons y packs, no compita con ellos. La pila de recursos puede hacer que ese objetivo sea técnicamente complejo, sobre todo en el área PBR.

### Regla explícita de Texture Sets

Microsoft documenta que:
- Un Texture Set puede referenciar solamente imágenes existentes en el mismo resource pack.
- Una textura de un pack de mayor prioridad no sustituye la imagen referenciada por el Texture Set de otro pack.
- Los Texture Sets que apuntan al mismo recurso no se fusionan: gana la definición del pack con mayor prioridad.

Fuente: [Microsoft Learn — Texture Set Resource Stack Behavior](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/texturesetsreference/texturesetsconcepts/texturesetsintroduction?view=minecraft-bedrock-stable).

### Consecuencia práctica

Si Hogar Dorado lleva un Texture Set para una textura vanilla y utiliza una imagen de color incluida en Hogar Dorado, la definición PBR superior puede hacer que se use esa imagen y no se mezclen automáticamente los mapas PBR del pack inferior. Por eso, un pack puede convivir perfectamente con otro en algunos ámbitos y competir por un recurso concreto en otros.

La solución no es prometer compatibilidad universal. Es diseñar una estrategia de intervención mínima y verificarla.

### Modelo de compatibilidad recomendado

**Capa ambiental:** priorizar perfiles de iluminación, atmósfera, agua, sombras y color, con identificadores namespaced. Aun así, los archivos y perfiles de ambiente también necesitan pruebas de prioridad y asignación por bioma.

**Capa de materiales:** agregar Texture Sets solo donde la función visual sea importante y se haya probado. Mantener una lista de recursos deliberadamente afectados y comprobar cómo se comportan cuando hay otro pack PBR en la pila.

**Capa de identidad de criaturas:** tocar materiales/entidades solo en casos elegidos. Cambiar archivos de entidad o render controllers puede tener efectos más invasivos que un ajuste ambiental, por lo que se debe inspeccionar por entidad.

### Matriz mínima de pruebas de compatibilidad

| Prueba | Orden/configuración | Qué observar |
|---|---|---|
| COMP-01 | Hogar Dorado solo | Referencia visual y comportamiento base |
| COMP-02 | Hogar Dorado por encima de un pack de texturas/PBR | Qué colores, materiales y perfiles dominan |
| COMP-03 | Hogar Dorado por debajo del pack de texturas/PBR | Qué ajustes sobreviven y cuáles son reemplazados |
| COMP-04 | Hogar Dorado con un add-on de criaturas | Brillos y apariencia de entidades |
| COMP-05 | Desactivar Hogar Dorado sin cambiar de mundo | Recuperación de la apariencia del pack base |

Registrar nombres y versiones exactos de cada pack, orden de prioridad, dispositivo y resultado. No afirmar compatibilidad universal a partir de una sola combinación.

## 6. Minecraft Creator Tools: cómo usarlo en nuestro flujo

Mojang describe Minecraft Creator Tools (mctools.dev) como un conjunto de código abierto con interfaz web y CLI. El sitio dispone de un Inspector que valida contenido y puede exportar reportes CSV o HTML. La documentación dice que se admiten .mcpack, .mcaddon y ZIP adecuados, y que el procesamiento de archivos realizado por el Inspector se ejecuta localmente en el navegador.

Fuentes:
- [Microsoft Learn — Minecraft Creator Tools Overview](https://learn.microsoft.com/es-mx/minecraft/creator/documents/mctoolsoverview?view=minecraft-bedrock-experimental)
- [Microsoft Learn — Validation Rules Index](https://learn.microsoft.com/es-mx/minecraft/creator/reference/content/mctoolsvalreference/validationrulesindex?view=minecraft-bedrock-experimental)
- [Minecraft Creator Tools](https://mctools.dev/)

### Flujo desde el iPhone, sin sustituir GitHub

1. Guardar una copia del MCPack de referencia en la app Archivos. No editar ni sobrescribir el original.
2. Abrir mctools.dev y probar el Inspector con el MCPack. La documentación garantiza el formato de entrada en el sitio, pero no confirma que todas las funciones del editor sean cómodas en Safari móvil.
3. Si los resultados están filtrados, cambiar la vista a Full o Raw en la configuración del proyecto, según la interfaz/documentación.
4. Empezar por reglas relacionadas con manifest, formato de archivo/JSON, referencias de textura, imágenes, presupuesto de textura y tamaño del pack.
5. Revisar los errores y advertencias más relevantes. No convertir cada advertencia contextual en una tarea obligatoria.
6. Exportar el informe CSV o HTML y guardarlo como un artefacto de auditoría.
7. Repetir exactamente el mismo flujo con v13 y comparar los resultados.

La documentación advierte que el sitio conserva el trabajo dentro del almacenamiento del navegador y que este puede limpiarse. Por tanto, Creator Tools debe ser un validador o editor auxiliar; GitHub debe seguir siendo el registro de continuidad del proyecto. No mantener una única copia del pack dentro del sitio.

### Reglas especialmente pertinentes

- **Manifest validation:** incluye comprobaciones de capacidad PBR y min_engine_version.
- **File validation:** puede detectar archivos JSON que no se pueden analizar.
- **Texture validation:** informa sobre las referencias de textura y uso aproximado de texture handles.
- **Texture images:** informa sobre dimensiones, memoria y presupuestos por tier.
- **Pack size:** cuenta archivos y tamaños.
- **Format/schema validation:** permite detectar incompatibilidades estructurales dentro de las reglas disponibles.

El informe de Creator Tools no garantiza que el resultado visual sea correcto. La validación de archivos y la prueba dentro de Minecraft son pasos complementarios.

### Advertencia sobre falsos positivos de pack

El índice incluye una regla llamada Incomplete Override para packs que parecen ser packs completos de texturas, pero cubren una proporción insuficiente de texturas vanilla. Hogar Dorado es principalmente un pack de ambiente/Vibrant Visuals, no un reemplazo integral de todas las texturas vanilla. Si esa regla aparece, revisar su contexto en vez de copiar indiscriminadamente las texturas vanilla o convertir Hogar Dorado en un pack completo.

## 7. Oportunidad de programación: catálogo único de biomas y generación reproducible

En v12 hay 62 archivos client biome. La repetición de cinco componentes entre todos ellos aumenta el riesgo de que una futura mejora se aplique solo a una parte de los biomas.

La idea es que Claude revise el script de construcción actual y estudie un catálogo de datos como fuente única, por ejemplo:
- ID del bioma.
- Perfil de niebla.
- Color de agua.
- Color de follaje.
- Color de hierba.
- Perfil de color grading.
- Perfiles de atmósfera/iluminación, si se añaden.
- Estado de verificación para la versión de Bedrock.

A partir de ese catálogo, el script podría generar los archivos .client_biome.json. Las salidas generadas no deben editarse manualmente; los cambios se realizan en el catálogo y se vuelven a generar.

Beneficios:
- Menos divergencia accidental entre biomas.
- Cobertura comprobable de los biomas que Ángel quiere admitir.
- Una sola actualización puede asignar un perfil común a muchos biomas.
- Se puede comparar el resultado generado antes y después mediante GitHub diff.
- Facilita revisar nuevos biomas que lleguen en actualizaciones futuras.

El catálogo y el script son una propuesta futura; no se ha creado ni ejecutado código en esta investigación.

## 8. Otra oportunidad: revisión automática al actualizar GitHub

Cuando la fuente del pack y el script de construcción estén disponibles en el repositorio, puede ser útil ejecutar comprobaciones automáticamente al guardar cambios. No hace falta empezar con una pipeline grande.

Un flujo futuro podría:
1. Analizar la sintaxis de JSON.
2. Comprobar que cada referencia a una imagen o perfil existe.
3. Verificar que cada Texture Set tiene color y una combinación permitida de mapas.
4. Comprobar que no hay IDs duplicados ni perfiles huérfanos.
5. Confirmar que los biomas objetivo aparecen en el catálogo.
6. Revisar el parámetro de water settings y las restricciones que no se mezclan entre biomas.
7. Producir un resumen de archivos, texturas, tamaño y cambios respecto a la referencia.
8. Empaquetar una beta reproducible y adjuntarla como artefacto de prueba.

Esto podría usar GitHub Actions o una validación alojada equivalente. Primero hay que asegurarse de que los archivos fuente necesarios se encuentren realmente en el repositorio y que las reglas no generen falsos positivos. No se ha implementado ningún workflow.

## 9. Notas de versión y cobertura del v12

La v12 tiene 62 archivos client biome y el documento biomas-bedrock.md del proyecto registra 60 biomas en su lista principal más dappled_forest y sulfur_caves. La coincidencia numérica es alentadora, pero no basta para certificar cobertura completa ni que todos los identificadores sean activos en Bedrock 26.52.

El documento también identifica algunas variantes históricas como no usadas en la generación de mundos normales. No borrar esas entradas automáticamente: primero confirmar si se quieren conservar por compatibilidad con mundos antiguos. Clasificarlas como:
- Actual/activo en 26.52.
- Histórico o no generado normalmente, posiblemente conservado por compatibilidad.
- Reciente añadido en 26.50/26.2.
- Preview/no confirmado.

Para cada nuevo release, revisar la fuente oficial de biomas o la muestra de referencia actual y mantener en el catálogo el estado de verificación. No utilizar IDs pendientes de confirmar en la versión estable como si fueran hechos establecidos.

## 10. Secuencia de pruebas sugerida para cuando vuelva Claude

| Prioridad | ID | Prueba | Criterio de decisión |
|---|---|---|---|
| 1 | TOOL-01 | Ejecutar Inspector sobre una copia de v12 | Obtener un informe base y detectar qué reglas reconoce |
| 2 | WATER-01 | Comparar water color contribution 0.0 vs 0.25 vs 0.60 | Elegir el aporte que mantiene agua clara y diferenciación por bioma |
| 3 | ATM-01 | Comparar bioma con configuración actual vs perfil explícito de atmósfera/iluminación | Confirmar si los perfiles globales actuales se aplican donde se esperaba |
| 4 | PBR-01 | Revisar los cuatro mapas MER de v12 y confirmar cómo están vinculados en v13 | Ningún mapa esencial queda huérfano o sin Texture Set válido |
| 5 | COMP-01 | Probar una combinación de resource packs con orden distinto | Registrar las partes de Hogar Dorado que se reemplazan y las que coexisten |
| 6 | GEN-01 | Prototipo local del catálogo de biomas/script de generación | Archivos generados repetibles y sin diferencias inesperadas |
| 7 | CI-01 | Definir un chequeo automático en GitHub | Solo después de confirmar que el código fuente del pack está en el repo |

Para cada prueba, registrar versión de juego, versión de pack, cambios, escena, captura real y resultado. No marcar una prueba como validada solo por pasar el Inspector.

## 11. Fuentes oficiales consultadas

- [Minecraft Creator Tools Overview](https://learn.microsoft.com/es-mx/minecraft/creator/documents/mctoolsoverview?view=minecraft-bedrock-experimental)
- [MCTools Validation Rules Index](https://learn.microsoft.com/es-mx/minecraft/creator/reference/content/mctoolsvalreference/validationrulesindex?view=minecraft-bedrock-experimental)
- [Texture Set JSON and Resource Stack Behavior](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/texturesetsreference/texturesetsconcepts/texturesetsintroduction?view=minecraft-bedrock-stable)
- [Water Effects and biome_water_color_contribution](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/watercustomization?view=minecraft-bedrock-stable)
- [1.26.0 Update Notes](https://learn.microsoft.com/en-us/minecraft/creator/documents/update1.26.0?view=minecraft-bedrock-stable)
- [Biome Customization](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/biomecustomization?view=minecraft-bedrock-stable)
- [Improving performance and resource usage](https://learn.microsoft.com/en-us/minecraft/creator/documents/practices/improvingperformanceandresourceusage?view=minecraft-bedrock-stable)
- [Texture Images Validation Rules](https://learn.microsoft.com/es-mx/minecraft/creator/reference/content/mctoolsvalreference/textureimage?view=minecraft-bedrock-experimental)
- [Texture Validation Rules](https://learn.microsoft.com/es-mx/minecraft/creator/reference/content/mctoolsvalreference/texture?view=minecraft-bedrock-experimental)
- [Minecraft Bedrock 26.52 Hotfix Changelog](https://feedback.minecraft.net/hc/en-us/articles/49175370527501-Minecraft-Bedrock-Edition-26-52-Hotfix-Changelog)

## 12. Conclusión y handoff

El siguiente avance no debería ser añadir más efectos porque sí. Primero hay que asegurarse de que los efectos que ya existen están vinculados correctamente, que el color del agua utiliza las características disponibles en la versión objetivo, que las configuraciones de atmósfera/iluminación se asignan a los biomas deseados y que el pack coexiste razonablemente con otros recursos.

Cuando vuelva, Claude debe:
1. Revisar este informe y el estado actual de GitHub.
2. Compararlo con el contenido real de referencia/v13 y con CAMBIOS.md.
3. Ejecutar primero Minecraft Creator Tools con una copia, guardar el reporte y revisar reglas relevantes.
4. Priorizar con Ángel una prueba de agua, atmósfera o PBR que aisle un solo cambio.
5. Documentar el resultado real antes de continuar.

**No se modificó el resource pack, no se ejecutó el juego y no se midió el rendimiento.** Esta ronda agrega investigación, un inventario estático de v12 y una lista de pruebas para hacer el siguiente ciclo más preciso.
