# Investigación técnica: PBR, emisión y optimización móvil

**Estado:** investigación documental; propuestas pendientes de implementación y prueba.  
**Fecha:** 2026-10-15  
**Proyecto:** Hogar Dorado — Minecraft Bedrock  
**Entorno objetivo conocido:** iPhone 14 Plus; Ángel indica que tiene instalada la versión 26.52. La compatibilidad concreta de cada archivo debe verificarse en el juego.

> **Propósito:** dejar un punto de continuidad para que Claude pueda retomar los hallazgos y decidir, junto con Ángel, qué incorporar en la siguiente versión. Este documento no afirma que las propuestas estén implementadas ni probadas.

## 1. Resumen ejecutivo

La recomendación central es avanzar por experimentos pequeños y reversibles. Primero hay que validar la cadena de recursos PBR/emisión; después probar uno o dos casos representativos; luego optimizar con mediciones del dispositivo y consolidar la identidad ambiental. No conviene cambiar varios sistemas a la vez.

Principios rectores:

1. Preservar una referencia reproducible antes de editar.
2. Separar la capa ambiental (luz, cielo, niebla, agua y color) de la capa de texturas emisivas siempre que la arquitectura del pack lo permita.
3. Mantener abiertos los brillos de minerales y criaturas hasta probarlos; no descartarlos solo por una preocupación teórica de diseño o rendimiento.
4. Optimizar a partir de mediciones y presupuestos, no de suposiciones basadas únicamente en la cantidad de archivos o el tamaño de descarga.
5. Mantener Minecraft reconocible: atmósfera y comodidad visual antes que espectáculo constante.
6. Registrar como **propuesta**, **implementado**, **probado** o **validado** según la evidencia real.

## 2. Base documental y alcance

### Material del proyecto revisado

- **Guía de texturas de Minecraft Bedrock (para Hogar Dorado):** documenta estructura de resource pack, atlas de terreno, flipbooks, texture sets, PBR, texturas de entidad, biomas y reglas propuestas para la próxima versión.
- **Lo que aprendí de la comunidad de creadores (para Hogar Dorado):** compara ideas de BSLB Dreamy Visuals, Solar, Dark Fantasy y otros packs; propone separar ambiente y texturas, considerar niveles de rendimiento, opciones y una noche donde las luces destaquen.
- **Packs de referencia facilitados por Ángel:** BSLB Dreamy Visuals, Solar Shaders Visuals y Dark Fantasy Visuals, además de otros ejemplos citados en la guía. Sirven para estudiar organización y técnicas, no como permiso para copiar sus archivos o contenido.

### Fuentes externas prioritarias

- [Microsoft Learn — Vibrant Visuals Resource Packs](https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/vvresourcepacks?view=minecraft-bedrock-stable)
- [Microsoft Learn — Introduction to Texture Sets](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/texturesetsreference/texturesetsconcepts/texturesetsintroduction?view=minecraft-bedrock-stable)
- [Microsoft Learn — Improving performance and resource usage](https://learn.microsoft.com/en-us/minecraft/creator/documents/practices/improvingperformanceandresourceusage?view=minecraft-bedrock-stable)
- [Microsoft Learn — Texture Budgets](https://learn.microsoft.com/en-us/minecraft/creator/documents/texturebudgets?view=minecraft-bedrock-stable)
- [Microsoft Learn — Subpack manifest reference](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/manifestreference/subpack?view=minecraft-bedrock-stable)
- [Microsoft Learn — Material files](https://learn.microsoft.com/en-us/minecraft/creator/documents/material-files?view=minecraft-bedrock-stable)
- [Mojang — Bedrock Samples](https://github.com/Mojang/bedrock-samples)
- [Mojang — Texture Sets reference in Bedrock Samples](https://github.com/Mojang/bedrock-samples/blob/main/documentation/Texture%20Sets.html)

Las páginas oficiales pueden actualizarse y la versión de muestra en la rama `main` puede cambiar. Al implementar, confirmar la sintaxis y el comportamiento relevantes para Minecraft Bedrock 26.52, no asumir que un ejemplo aislado garantiza compatibilidad con todos los contextos.

## 3. PBR y emisión: lista de comprobación técnica

### 3.1 Cada texture set debe describir una textura real

La documentación de Microsoft define la capa `color` como obligatoria en un `*.texture_set.json`. Las demás capas son opcionales. El texture set debe referenciar archivos disponibles en el pack o valores uniformes válidos según el esquema; no se debe declarar una configuración que apunte a una imagen inexistente.

**Regla propuesta para la revisión de la beta:**

- Confirmar que existe la textura de color que corresponde al bloque/ítem/entidad.
- Confirmar que cada ruta dentro del JSON corresponde a un archivo incluido y con nombre/ruta exactos.
- Confirmar que `format_version` y la estructura coinciden con un ejemplo oficial adecuado para la función usada.
- Revisar los registros de contenido del juego si está disponible la información del error; un archivo presente no demuestra que el motor lo haya aceptado.
- No asumir que Minecraft detecta automáticamente un mapa por terminar en `_mer` o `_mers`: debe estar referenciado correctamente por el texture set cuando el esquema lo requiere.
- No sustituir la textura de color vanilla por una textura inventada sin revisar el efecto artístico y la compatibilidad.

### 3.2 Lectura del mapa MER

En el mapa `metalness_emissive_roughness` (MER), los canales RGB tienen estas funciones:

- **Rojo:** metalness.
- **Verde:** emisión.
- **Azul:** roughness.

El blanco en el canal relevante no debe interpretarse como una garantía de que el bloque iluminará dinámicamente todo lo que lo rodea. La emisión PBR describe propiedades visuales del material; el resultado exacto depende de la canalización gráfica y debe verificarse en juego.

### 3.3 Primer experimento: redstone

Empezar con un único caso reduce el área de búsqueda de errores.

1. Guardar una referencia de la versión sin el cambio.
2. Verificar textura de color, mapa MER y el texture set correspondiente.
3. Crear una variante con emisión moderada y otra con emisión más intensa, si técnicamente es fácil mantenerlas como pruebas separadas.
4. Probar en una cueva oscura y en una zona iluminada.
5. Evaluar reconocimiento, legibilidad de píxeles, coherencia con la paleta, distracción y rendimiento.
6. Registrar capturas reales, versión, configuración y resultado. No marcar como validado hasta que la prueba ocurra.

El criterio no es “que brille lo máximo posible”, sino que ayude a reconocer el mineral y aporte magia sin romper el equilibrio visual.

### 3.4 Criaturas: comprobar el mecanismo correcto por entidad

La documentación de materiales de Bedrock describe materiales como `entity_emissive_alpha`, pero no debe aplicarse indiscriminadamente sin inspeccionar el JSON, la textura y el renderizado original de cada criatura.

Para cada mob candidato:

- Partir del archivo de entidad apropiado y compatible con la versión objetivo.
- Confirmar si el material existente puede mantenerse o debe cambiarse.
- Determinar qué zonas de la textura deben ser emisivas y cómo interactúa su alfa con la transparencia.
- Probar una criatura inicialmente, conservando una copia de referencia.
- Comprobar apariencia de día y de noche, así como visibilidad en distintas distancias.
- Evitar sobrescribir recursos de otros packs salvo que esa interacción sea intencional y probada.

Los mapas `_mer` observados en packs comunitarios no prueban por sí solos que todas las entidades los usen de la misma manera. Verificar el método por entidad antes de generalizarlo.

### 3.5 Otras texturas luminosas

Después de la redstone, evaluar un bloque como glowstone, shroomlight o sea lantern. Confirmar cada caso individualmente: textura de color, mapa de emisión, textura set y representación final. No dar por hecho que una solución válida para un bloque también funcionará para todos los demás.

## 4. Optimización: qué medir antes de decidir

Microsoft Learn recomienda vigilar el número de archivos, el tamaño total sin comprimir y la cantidad de texture handles; advierte que demasiados recursos pueden afectar la carga o provocar problemas en algunos dispositivos. Sus presupuestos varían por tipo de contenido y nivel de rendimiento.

**No confundir estas medidas:**

- Tamaño del archivo `.mcpack` comprimido.
- Tamaño total descomprimido del pack.
- Número total de archivos.
- Número de texturas únicas/texture handles.
- Total de texels de texturas cargadas y uso de memoria durante el juego.
- Fluidez percibida, duración de carga y calentamiento del teléfono.

Una cifra aislada no mide el rendimiento completo. Las recomendaciones oficiales son presupuestos y guías, no sustitutos de la prueba en el dispositivo objetivo.

### Acciones de bajo riesgo

1. Evitar incluir archivos que no use la versión final.
2. No duplicar texturas a mayor resolución si el detalle no es perceptible en la pantalla objetivo.
3. Conservar los formatos y dimensiones apropiados para cada recurso; revisar los flipbooks antes de reemplazar animaciones.
4. Mantener las configuraciones ambientales y la emisión organizadas para facilitar identificar qué cambio provoca un problema.
5. Ejecutar una validación estructural/PBR antes de cada prueba de juego y conservar la salida del validador.
6. Registrar los resultados en el mismo escenario y con los mismos ajustes, para hacer comparaciones razonables.

### Opciones futuras sujetas a evidencia

- **Subpacks de calidad:** son una vía oficial para ofrecer alternativas a los jugadores. No crearlos antes de saber qué limitación resuelven. Definir qué archivos sustituye cada variante y verificar cómo el juego selecciona o presenta las opciones.
- **Perfil ligero:** valorar solo si el perfil base presenta problemas medibles o si la investigación de compatibilidad demuestra que conviene ofrecerlo.
- **Calidad de niebla/sombras/rayos de luz:** probar cada coste visual por separado. No atribuir una caída de rendimiento a un efecto sin comparar una escena controlada.
- **Resolución por grupo de recursos:** priorizar el presupuesto visual en cielo, agua o detalles importantes solo si el beneficio se observa en pantalla.

### Inventario preliminar de los packs de referencia

Un inventario inicial realizado durante esta investigación reportó aproximadamente:

| Pack facilitado | Archivos contados | Tamaño descomprimido reportado |
|---|---:|---:|
| BSLB Dreamy Visuals v1.0.4 | 10.794 | 31,2 MiB |
| Solar Shaders Visuals v2.0.3 | 2.854 | 4,8 MiB |
| Dark Fantasy Visuals | 1.739 | 32,6 MiB |

Estas cifras son **inventario preliminar**, no benchmark ni certificación técnica: no informan FPS, memoria pico ni compatibilidad; antes de usarlas para decisiones finales, repetir el inventario con un método consistente y comparar el tamaño comprimido y descomprimido por separado. El mayor número de archivos de un pack tampoco demuestra, por sí solo, peor rendimiento en todos los dispositivos.

## 5. Dirección artística traducida a criterios comprobables

### Capas y dimensiones

- **Overworld:** familiaridad, vegetación reconocible, luz cálida y profundidad moderada.
- **Atardecer:** sensación dorada y nostálgica; evitar saturación excesiva o pérdida de detalle.
- **Noche:** valores más oscuros con fuentes de luz reconocibles; no oscurecer hasta dificultar jugar.
- **Lluvia:** profundidad atmosférica sin borrar detalles útiles.
- **Nether:** calor y peligro conservados; tintes alternativos solo si no confunden lava, rutas y siluetas.
- **End:** sensación de inmensidad y vacío; no limitarlo a un filtro morado genérico.

Estas son metas creativas, no resultados ya conseguidos.

### Secuencia temporal

Investigar si las transiciones mañana → tarde → atardecer → noche pueden lograrse con keyframes/configuración ambiental antes de multiplicar texturas o archivos. Validar la sintaxis en documentación y muestras actuales. Comparar transiciones completas, no solo capturas estáticas de una hora.

### Prueba de comodidad visual

En sesiones cortas de 10–15 minutos y, posteriormente, una sesión más larga:

- ¿La imagen sigue siendo agradable sin exigir atención constante?
- ¿Se distinguen bloques, objetos y criaturas?
- ¿Los brillos son decorativos/funcionales en la medida prevista?
- ¿El cambio entre día y noche se siente natural?
- ¿El pack conserva la identidad reconocible de Minecraft?

Las respuestas deben registrarse como observaciones subjetivas, no como mediciones científicas de confort.

## 6. Matriz de experimentos propuesta

| ID | Experimento | Cambiar solo | Criterio de aceptación | Estado |
|---|---|---|---|---|
| PBR-01 | Redstone emisiva | Texture set/MER del caso elegido | Sin error estructural; emisión consistente y legible en dos escenas | Pendiente |
| MOB-01 | Una criatura candidata | Material/zonas emisivas de una entidad | Solo brillan los píxeles previstos; apariencia correcta día/noche | Pendiente |
| ATM-01 | Transición diaria | Keyframes o configuración ambiental | Transición coherente en cuatro momentos del día | Pendiente |
| ATM-02 | Niebla de Overworld | Configuración de niebla elegida | Profundidad perceptible sin perder legibilidad | Pendiente |
| DIM-01 | Nether/End | Configuración de una dimensión por prueba | Se mantiene la identidad de cada dimensión | Pendiente |
| PERF-01 | Versión base | Ningún cambio visual adicional | Registrar carga, fluidez percibida y calentamiento en el mismo escenario | Pendiente |
| PERF-02 | Variante ligera (solo si procede) | Una categoría de recursos | Mejora observable sin degradación visual inaceptable | No iniciada |

No declarar aprobado un experimento solo porque el JSON sea válido. Separar **validación de archivos**, **carga en el juego**, **resultado visual** y **rendimiento**.

## 7. Orden de trabajo recomendado para la siguiente versión

1. **Confirmar el punto de partida:** v13 y el registro de cambios guardados por Claude; identificar el commit/archivo exacto de referencia antes de editar.
2. **Auditar estructura PBR:** revisar los texture sets objetivo, rutas y archivos de color/emisión.
3. **Validar un caso pequeño:** empezar por redstone; no ampliar a todos los minerales hasta registrar el resultado.
4. **Validar una criatura:** comprobar la solución adecuada según su archivo de entidad y textura.
5. **Validar ambiente:** revisar las cinco escenas reproducibles: amanecer, atardecer, noche, lluvia y una escena por dimensión relevante.
6. **Medir rendimiento en el iPhone 14 Plus:** usar la misma escena y ajustes para comparar la referencia con la versión modificada.
7. **Decidir si hacen falta variantes de calidad:** únicamente con resultados que las justifiquen.
8. **Actualizar documentación y changelog:** anotar qué cambió, por qué, cómo se probó y qué resultado se observó.

## 8. Registro de fuentes y límites

### Confirmado por documentación de Microsoft/Mojang

- Vibrant Visuals usa texture sets PBR y requiere declarar la capacidad `pbr` en el manifiesto conforme a la documentación vigente.
- La capa `color` es necesaria en el texture set; MER codifica metalness, emisión y roughness en sus canales.
- Vibrant Visuals admite texture sets en bloques y también en carpetas de ítems, entidades y partículas.
- Los subpacks permiten opciones alternativas de contenido/calidad.
- Existen guías oficiales sobre conteo de archivos, tamaño, texture handles y presupuestos de texturas.

### Observaciones del material entregado por Ángel

- Las guías del proyecto ya identifican posibles texture sets incompletos por ausencia de la textura de color y recomiendan no declararlos sin color.
- Las referencias comunitarias incluyen ejemplos de mapas emisivos para ciertos minerales y configuraciones ambientales separadas por dimensión.
- Las decisiones de identidad visual de Hogar Dorado priorizan nostalgia, legibilidad, comodidad y preservación de la identidad de Minecraft.

### Hipótesis o pendientes por verificar

- Qué técnica emisiva concreta funciona mejor para cada bloque y criatura en la versión 26.52.
- Si un subpack ligero resulta necesario para el pack final.
- El coste real de los efectos de niebla, sombras y rayos de luz en el iPhone.
- Si cada transición temporal y configuración de dimensión ofrece el resultado artístico esperado.
- El inventario definitivo de archivos y los recursos que se pueden retirar sin alterar la experiencia.

**No se han realizado ni se afirman aquí pruebas de gameplay o benchmarks de rendimiento.** Esta investigación es un mapa de trabajo para guiar futuras pruebas.

## 9. Instrucciones de continuidad para Claude

Al retomar Hogar Dorado:

1. Leer este documento, `docs/ESTADO_ACTUAL.md`, `docs/IDENTIDAD_VISUAL.md`, `docs/PROTOCOLO_COLABORACION.md`, y `CHANGELOG.md`.
2. Revisar la referencia v13 y el registro `CAMBIOS.md` que Claude indicó guardar en `referencia/`.
3. Verificar qué recomendaciones ya se implementaron después de la última actualización; no repetir trabajo ni asumir que una propuesta pendiente sigue pendiente sin inspeccionar el estado actual.
4. Elegir junto con Ángel el siguiente experimento pequeño y reversible; la decisión final corresponde a Ángel.
5. Registrar el resultado real en GitHub y diferenciar implementación, validación y prueba en Minecraft.

> **Objetivo común:** que Hogar Dorado produzca una nostalgia que parece un sueño, con una base técnica mantenible y un rendimiento comprobado, sin sacrificar los detalles mágicos de Minecraft por precaución no demostrada.
