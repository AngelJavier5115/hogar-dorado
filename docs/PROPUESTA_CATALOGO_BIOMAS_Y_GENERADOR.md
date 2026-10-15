# Propuesta de arquitectura: catálogo central de biomas y generación reproducible

**Fecha:** 2026-10-15  
**Estado:** diseño documentado; implementación pendiente de revisión con Claude y Ángel.  
**Juego objetivo indicado por Ángel:** Minecraft Bedrock 26.52.  
**Propósito:** reducir duplicaciones y errores al mantener decenas de archivos client biome, sin alterar la identidad visual ni generar archivos a partir de información antigua sin verificar.

> Este documento es una especificación de trabajo, no una implementación. La v13 y el script de construcción de Claude deben seguir siendo la referencia técnica hasta que se comparen y se apruebe una migración.

## 1. Resumen ejecutivo

La opción recomendada es una **fuente única de datos en JSON**, leída por un generador pequeño en Node.js que produzca los archivos .client_biome.json de la carpeta final del resource pack.

El flujo propuesto es:

1. Mantener un catálogo humano-legible con la asignación de paleta y perfiles por bioma.
2. Validar el catálogo antes de generar nada.
3. Generar un archivo client biome por cada bioma incluido en la versión.
4. Validar las salidas generadas: estructura, rutas/perfiles referenciados, colores y cobertura.
5. Comparar la salida con la versión anterior mediante Git diff.
6. Empaquetar y probar en Creator Tools y Minecraft.
7. Documentar la evidencia antes de considerar la versión validada.

**No se recomienda comenzar escribiendo el catálogo completo hoy:** primero hay que extraer los valores reales de la referencia v13 y del script de construcción que Claude dijo haber guardado. La lista de biomas compartida por Ángel contiene valores vanilla y una asignación de paleta de Hogar Dorado v8; no debe confundirse con los valores que actualmente generan la v13 o la versión de trabajo de Claude.

## 2. Qué pude confirmar en el repositorio actual

Se revisó la raíz y la carpeta docs del repositorio AngelJavier5115/hogar-dorado. En el estado visible actual hay documentación Markdown (README, bitácora, changelog, estado, identidad, protocolo e informes de investigación), pero no se encontraron carpetas fuente del resource pack, catálogo de biomas ni script de construcción.

Eso significa:
- Aún no hay una fuente de datos única que podamos modificar con seguridad en el repositorio.
- No se debe crear un generador desconectado del script que Claude ya utiliza.
- Antes de implementar, se necesita importar/compartir la estructura fuente adecuada o pedir a Claude que incorpore el script existente y los recursos fuente a la estructura acordada, conservando la referencia v13 por separado.
- La propuesta debe integrarse con el script actual, no reemplazarlo por un generador improvisado.

## 3. Por qué sirve un catálogo

La v12 que Ángel compartió contiene 62 archivos de client biome con una estructura parecida. Cada uno guarda valores visuales como el color de agua, hierba y follaje; referencias a niebla y gradación de color; y, en futuras iteraciones, posiblemente referencias de atmósfera, iluminación y agua para Vibrant Visuals.

Cuando cada archivo se edita manualmente, pueden ocurrir errores de mantenimiento:
- Un cambio de paleta se aplica a unos biomas pero se olvida en otros.
- Un identificador de niebla/perfil cambia de nombre y quedan referencias huérfanas.
- Se introduce un color con formato inválido.
- Una entrada de bioma se duplica o queda fuera de la versión actual.
- Los archivos resultantes no son fáciles de comparar entre versiones.
- Un bioma reciente como dappled_forest queda con reglas antiguas o sin estado de verificación.

El catálogo concentra las decisiones intencionales en una única capa de datos. El generador traduce esos datos al JSON con la forma exacta que espera Minecraft.

La documentación de Microsoft define client biome como configuración visual/sonora del lado del cliente. Cada archivo contiene format_version y una definición minecraft:client_biome, con description y components. La descripción del identificador debe referirse a un bioma real existente; los componentes no usados pueden omitirse. El generador no debe añadir todos los componentes posibles: solo debe emitir los que el catálogo define de manera explícita.

Fuentes:
- [Client Biomes JSON and Overview](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/clientbiomesreference/examples/clientbiomesoverview?view=minecraft-bedrock-stable)
- [Client Biome JSON File Reference](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/clientbiomesreference/examples/components/client_biome_json_file?view=minecraft-bedrock-stable)
- [Mojang Bedrock Samples — Client Biomes](https://github.com/Mojang/bedrock-samples/blob/main/documentation/Client%20Biomes.html)

## 4. Recomendación sobre el formato: JSON en vez de YAML

Para este proyecto, empezaría con JSON porque:
- El juego espera JSON como salida.
- Node.js puede leerlo sin instalar dependencias adicionales.
- No hace falta agregar un parser YAML a un proyecto que quizá se editará desde el iPhone.
- Los campos, arreglos, valores nulos y tipos pueden validarse de forma explícita.
- Git muestra diferencias precisas de un valor a otro.

La legibilidad debe venir de la estructura del catálogo y de comentarios en la documentación, no de introducir una nueva dependencia. JSON estándar no admite comentarios; la explicación de cada perfil y la procedencia de los datos pueden ir en campos como note/source o en un documento acompañante. Si se decide usar YAML más adelante por facilidad editorial, debe ser una decisión consciente y validada por el generador; no mezclar formatos sin una necesidad.

## 5. Modelo de datos recomendado

Separar tres conceptos que a menudo se mezclan:

### A. Inventario de biomas

Cada registro representa el identificador exacto de un bioma de Bedrock que el pack pretende atender. Campos sugeridos:

- id: nombre interno exacto usado en description.identifier y nombre base del archivo generado.
- status: active, legacy, preview o unverified.
- palette: referencia a una paleta del proyecto.
- overrides: campos específicos que sobrescriben el perfil base, si existe un motivo visual aprobado.
- target: clasificación de dimensión (overworld, nether, end) y notas de cobertura.
- provenance: de dónde se obtuvo el registro y qué tan confirmado está.
- verification: versión del juego, pack inspeccionado y tipo de evidencia (documental, inspección estática, validación de archivos, prueba en juego).

### B. Paletas y perfiles compartidos

Un catálogo central de paletas evita copiar valores idénticos en decenas de registros. Un perfil puede contener:
- waterColor
- grassColor
- foliageColor
- dryFoliageColor, si se usa
- fogProfile
- colorGradingProfile
- atmosphereProfile, cuando se adopte
- lightingProfile, cuando se adopte
- waterProfile, cuando se adopte
- cubemapProfile, solo si se decide usar esa característica y el formato objetivo la permite

Los perfiles deben referenciar identificadores exactos de archivos que existan en el pack. Las propiedades no elegidas deben omitirse o quedar explícitamente sin configurar; no asignar perfiles por defecto que no hayan sido decididos.

### C. Configuración de salida

Mantener en un solo lugar explícito:
- clientBiomeFormatVersion
- waterFormatVersion
- targetGameVersion
- minEngineVersion del manifest
- directorio fuente y destino de salida
- política sobre biomas legacy, preview y unverified

Estas versiones no son intercambiables. Por ejemplo, que el juego objetivo sea 26.52 no significa que format_version de todos los JSON deba ser la cadena “26.52”. La documentación oficial trata format_version como el esquema de cada archivo, mientras min_engine_version del manifest expresa el requisito del pack. El validador de Creator Tools tiene reglas separadas para estos conceptos.

## 6. Ejemplo conceptual del catálogo

El siguiente ejemplo muestra la idea del modelo, no valores aprobados para la v13. Los valores de color son ilustrativos y deben sustituirse por los valores extraídos de la referencia actual.

    {
      "catalogVersion": 1,
      "targetGameVersion": "26.52",
      "clientBiomeFormatVersion": "1.21.90",
      "palettes": {
        "plains": {
          "waterColor": "#5FB8D0",
          "grassColor": "#9CC25A",
          "foliageColor": "#8DB24E",
          "fogProfile": "hogar:plains_fog",
          "colorGradingProfile": "hogar:lush"
        }
      },
      "biomes": [
        {
          "id": "plains",
          "status": "active",
          "dimension": "overworld",
          "palette": "plains",
          "overrides": {},
          "provenance": {
            "source": "v13",
            "verification": "pending"
          }
        }
      ]
    }

La forma definitiva del catálogo debe validarse con la versión de Bedrock y el esquema actual. En particular:
- Conservar el identificador vanilla exacto que requiere el client biome de la versión objetivo; no agregar un namespace automáticamente sin comprobarlo.
- Las propiedades opcionales no presentes deben omitirse de components. No emitir cadenas “null”, objetos vacíos irrelevantes ni componentes incompletos.
- Las referencias perfil deben existir en los archivos correspondientes, por ejemplo en fogs, color_grading, atmospherics, lighting, water o cubemaps según el componente elegido.
- La fuente y el estado de verificación son metadatos del catálogo y no deben copiarse al client biome generado.

## 7. Una sola fuente de verdad y archivos generados

Estructura sugerida para discutir con Claude, no creada todavía:

    data/
      biome-catalog.json
    tools/
      build-biomes.mjs
      validate-biomes.mjs
    pack-src/
      manifest.json
      fogs/
      color_grading/
      lighting/
      water/
      textures/
    build/
      resource_pack/
        biomes/
          *.client_biome.json
        ...recursos copiados del pack fuente...

Responsabilidades:
- data/biome-catalog.json: única fuente de datos para el inventario, los grupos y la configuración por bioma.
- tools/build-biomes.mjs: convierte catálogo a client biome JSON determinista.
- tools/validate-biomes.mjs: valida reglas propias del proyecto antes de construir.
- pack-src/: recursos originales editables de la beta que no son generados desde el catálogo.
- build/: salida reproducible; se puede reconstruir y debe tratarse como artefacto generado.

Esta estructura no se debe imponer si el script actual de Claude ya tiene una organización correcta. Lo importante es una sola fuente de verdad y una frontera clara entre archivos editables y generados.

## 8. Generación determinista: cómo evitar que el script sea otra fuente de problemas

Un generador robusto debería:
1. Leer el catálogo como UTF-8 y fallar con un mensaje claro si el archivo está mal formado.
2. Validar todos los campos antes de escribir cualquier archivo, para no dejar una generación parcial.
3. Resolver palette y overrides de forma explícita.
4. Crear el client biome conforme a la estructura vigente: format_version, minecraft:client_biome, description.identifier y components.
5. Emitir solo los componentes y subcampos definidos en los datos efectivos.
6. Ordenar las entradas de forma estable, respetando la estructura acordada para facilitar los diffs.
7. Escribir una salida reproducible: la misma entrada debe producir exactamente los mismos bytes, sin timestamps o rutas locales variables.
8. Escribir solo dentro del directorio de salida generado.
9. Reportar qué biomas emitió, omitió y por qué.
10. No borrar manualmente archivos del pack original. La limpieza de salidas obsoletas debe limitarse a la carpeta de generated output y validarse antes de ejecutarse.

El script debe fallar de forma segura ante un ID duplicado, perfil inexistente, color mal formado o campo desconocido que pudiera ocultar un error de escritura.

## 9. Validación propuesta

### Validación del catálogo

- IDs únicos, no vacíos y con la nomenclatura esperada para los biomas vanilla.
- Valores de status restringidos a la lista aprobada.
- Todo valor palette apunta a una paleta existente.
- Todo perfil utilizado en una paleta/override se resuelve en el catálogo de perfiles y posteriormente en un archivo real.
- Colores hexadecimales normalizados y validados.
- Formas/tipos de los campos correctos.
- No se emiten biomas con status preview o unverified a menos que se active una opción de build explícita.
- Registros legacy se mantienen rastreables pero su salida debe seguir una política decidida por Ángel.
- Los datos de procedencia diferencian claramente el inventario vanilla documentado de la configuración del pack v8 y la configuración real de v13.

### Validación de los perfiles y archivos

- El identificador declarado en cada archivo resuelve con el perfil que referencia el catálogo.
- No hay perfiles repetidos de manera accidental o referencias rotas.
- Los campos obligatorios para una característica concreta se verifican contra el esquema actual, no con una lista artesanal que quede obsoleta.
- Se conservan restricciones globales conocidas, como tener un operador de tone mapping compatible entre perfiles cuando el motor no mezcla esos valores.
- Se valida la configuración del agua por separado, incluida biome_water_color_contribution si el pack decide utilizarla.
- Se comprueba el número de archivos generado contra el número de biomas que la política permite emitir.

### Validación del artefacto

Después de la generación:
1. Comparar el contenido de la carpeta generada contra el catálogo.
2. Revisar el diff en GitHub.
3. Ejecutar Creator Tools Inspector sobre una copia del pack construido.
4. Resolver errores; evaluar advertencias en contexto.
5. Importar la copia a Minecraft y probar escenas.
6. Anotar el reporte de la herramienta y evidencia real.

Creator Tools documenta reglas para JSON/formato, integridad, tamaño del pack, referencias y límites de texture handles. Es una capa de validación complementaria, no una garantía de calidad visual ni de rendimiento real.

Fuentes:
- [MCTools Validation Rules Index](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/mctoolsvalreference/validationrulesindex?view=minecraft-bedrock-stable)
- [MCTools Texture Validation](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/mctoolsvalreference/texture?view=minecraft-bedrock-stable)
- [MCTools Project Integrity](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/mctoolsvalreference/prjint?view=minecraft-bedrock-stable)
- [MCTools File Validation](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/mctoolsvalreference/valfile?view=minecraft-bedrock-stable)

## 10. Pruebas automatizadas y GitHub Actions

Al estar la fuente del pack y el generador en el repositorio, se puede usar GitHub Actions para ejecutar las validaciones al hacer push o abrir un pull request. GitHub documenta que los workflows pueden ejecutar scripts almacenados en el repositorio en runners alojados.

Primera fase recomendada:
- Sin publicar ni distribuir el pack automáticamente.
- Ejecutar el generador en modo validación o sobre una carpeta temporal.
- Ejecutar pruebas unitarias del catálogo/generador.
- Comprobar que los archivos generados estén actualizados y que una segunda generación no produzca diff.
- Empaquetar un artefacto de prueba descargable desde el workflow, sin publicarlo en CurseForge/MCPEDL.
- Adjuntar el informe de validación como artefacto.

Para empezar, un script Node.js que use las APIs estándar puede bastar. No añadir dependencias ni una pipeline sofisticada antes de que exista un catálogo verdadero y un build reproducible. Integrar Creator Tools automatizado solo después de confirmar el mecanismo CLI o ejecución disponible en la versión elegida; la primera etapa puede usar inspección del navegador.

Fuente: [GitHub Docs — Adding scripts to a workflow](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/add-scripts).

## 11. Pruebas del generador antes de confiar en él

Pruebas mínimas:
- Un catálogo válido genera el número esperado de archivos.
- Una segunda generación produce el mismo resultado.
- Un ID duplicado produce error y no deja una salida parcial.
- Una paleta inexistente produce error claro.
- Un perfil no resuelto produce error claro.
- Un color inválido produce error.
- Un bioma unverified no se emite en un build normal.
- Un componente opcional vacío queda omitido en vez de emitir JSON inválido.
- El nombre de archivo corresponde exactamente al id de salida.
- Si se elimina un bioma del catálogo, el generador identifica la salida obsoleta en su directorio generado; no borra recursos fuera de ese directorio.
- Los archivos generados tienen un diff legible y no cambian el orden entre ejecuciones.

Si es factible, hacer pruebas de regresión que comparen el resultado del generador con los client biome JSON de una referencia aprobada. No buscar igualdad con la v12 si la v13 ya contiene cambios intencionales.

## 12. Migración segura en etapas

**Etapa 0 — Preparar fuente real**
- Claude localiza su script de construcción y los archivos fuente de la referencia v13.
- Ángel decide con Claude qué parte de la estructura fuente se mantendrá en el repositorio privado.
- Mantener la v13 congelada sin regenerarla encima.

**Etapa 1 — Auditoría de datos**
- Extraer los 62 registros actuales desde la fuente v13, no desde la paleta de v8.
- Comprobar cada bioma frente a su archivo actual y al registro CAMBIOS.md.
- Añadir el estado de verificación y la procedencia.
- Confirmar qué biomas son activos, históricos o pendientes para Bedrock 26.52.

**Etapa 2 — Catálogo paralelo**
- Crear el catálogo sin quitar todavía los client biome JSON actuales.
- Generar una salida de prueba en un directorio separado.
- Comparar JSON y diffs con la versión de referencia; identificar todas las diferencias.

**Etapa 3 — Validación y aprobación**
- Ejecutar Creator Tools.
- Revisar con Ángel cualquier cambio visual que se derive de diferentes asignaciones.
- Probar una build temporal en Minecraft.
- Solo cuando los resultados coincidan con la intención, decidir si el catálogo reemplaza la edición manual.

**Etapa 4 — Automatización**
- Añadir validación de catálogo/generador y un workflow de CI.
- Crear artefactos de prueba versionados y un resumen automático.
- Mantener la publicación pública como acción manual que requiere aprobación de Ángel.

## 13. Qué hacer con los biomas nuevos y el historial

El archivo biomas-bedrock.md registra la lista principal de biomas y, por separado, dappled_forest y sulfur_caves como novedades recientes. También mantiene ice caves como no confirmadas. Es una buena base para el inventario, pero no debe controlar la apariencia de la versión actual porque algunas columnas reflejan Hogar Dorado v8.

Propuesta:
- Conservar la lista original como referencia con sus propias fechas y fuentes.
- Crear el catálogo operativo a partir de los client biome JSON actuales de v13.
- Cruzar los IDs con la documentación/versiones actuales y con pruebas cuando estén disponibles.
- Marcar cada bioma como active, legacy, preview o unverified.
- Mantener ice caves fuera del build normal mientras no se confirme su presencia y el ID exacto en el Bedrock estable objetivo.
- Conservar biomas históricos solo si se documenta por qué; no agregarlos al build actual automáticamente.

## 14. Decisiones que necesita revisar Claude antes de implementar

- Dónde vive hoy el script de construcción y qué archivos produce.
- Qué archivo es la autoridad actual de los colores y perfiles de v13.
- Si usar un catálogo único con perfiles dentro del mismo JSON o dos archivos separados (biomes + profiles). La preferencia inicial es un solo JSON mientras el tamaño sea manejable.
- Qué versiones de formato admite la versión objetivo por tipo de archivo.
- Qué identificadores se esperan exactamente para vanilla client biomes en la build objetivo.
- Qué directorio será salida generada y cómo se empaqueta la build final.
- Si las salidas generadas se mantienen en Git o se reconstruyen siempre; no mezclar los dos enfoques sin una política.
- Qué cambios de integración necesitan aprobación de Ángel.

## 15. Fuentes oficiales

- [Client Biomes JSON and Overview](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/clientbiomesreference/examples/clientbiomesoverview?view=minecraft-bedrock-stable)
- [Client Biome JSON File Reference](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/clientbiomesreference/examples/components/client_biome_json_file?view=minecraft-bedrock-stable)
- [Mojang Bedrock Samples — Client Biomes](https://github.com/Mojang/bedrock-samples/blob/main/documentation/Client%20Biomes.html)
- [MCTools Validation Rules Index](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/mctoolsvalreference/validationrulesindex?view=minecraft-bedrock-stable)
- [MCTools Texture Validation](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/mctoolsvalreference/texture?view=minecraft-bedrock-stable)
- [MCTools Project Integrity](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/mctoolsvalreference/prjint?view=minecraft-bedrock-stable)
- [MCTools File Validation](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/mctoolsvalreference/valfile?view=minecraft-bedrock-stable)
- [GitHub Docs — Adding scripts to a workflow](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/add-scripts)

## 16. Conclusión

El catálogo central es una mejora potencial no porque reduzca necesariamente los FPS o el tamaño del pack, sino porque reduce duplicación de decisiones, hace las salidas reproducibles y facilita validar cobertura y errores. La optimización técnica tiene que medirse por separado.

El paso correcto no es escribir un generador genérico sin la fuente actual. Es recuperar la fuente y el script que Claude ya tiene, extraer de allí el estado real de v13, crear el catálogo en paralelo, comparar las salidas y probar antes de sustituir nada.

**No se implementó código ni se modificó el resource pack en esta investigación.** La siguiente acción de desarrollo debe coordinase con Claude, que dispone del script de construcción y de la referencia v13, y Ángel conserva la decisión final.
