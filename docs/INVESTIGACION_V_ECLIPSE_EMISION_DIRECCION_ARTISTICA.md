# Investigación V — Eclipse Shader, emisión selectiva de entidades y dirección artística

**Fecha:** 15 de octubre de 2026  
**Proyecto:** Hogar Dorado · Minecraft Bedrock 26.52 · Vibrant Visuals · dispositivo objetivo: iPhone 14 Plus  
**Tipo de trabajo:** auditoría de código y recursos, investigación documental y propuesta de diseño.  
**Estado de validación:** análisis estático; las propuestas todavía no se han probado dentro de Minecraft.

## Resumen ejecutivo

La investigación deja cinco conclusiones firmes:

1. Eclipse Shader es un shader de Java para Iris, no un resource pack de Bedrock. Sus sombreadores GLSL no se pueden trasladar tal cual a un .mcpack. Sí podemos estudiar sus ideas —emisión con máscaras, balance de color, agua, sombras, atmósfera y luz direccional para el End— y recrear objetivos parecidos con las interfaces documentadas de Vibrant Visuals.
2. En los archivos revisados, Eclipse no mantiene una tabla de luces dinámicas para mobs. Su shaders/ph_lights.json define 36 fuentes de bloques; no incluye entradas para Blaze, piglin, gólem, Enderman u otras entidades. Los archivos gbuffers_entities_glowing.fsh y gbuffers_spidereyes.fsh dirigen capas concretas a un pase emisivo, pero eso no equivale a que cada mob ilumine dinámicamente el entorno.
3. Bedrock permite emisión superficial de píxeles concretos de mobs mediante mapas PBR MERS. Esto debe distinguirse de proyectar luz real sobre los bloques cercanos. La documentación de Vibrant Visuals presenta local_lighting/local_lighting.json para fuentes locales de bloques; no documenta allí un sistema equivalente de fuentes puntuales arbitrarias adheridas a cualquier mob en movimiento.
4. En el paquete de referencia Bedrock 26.50 examinado, el canal verde de los mapas MERS de lava quieta y lava en flujo está completamente negro. Aclarar el RGB de la lava no activa emisión PBR. Para evitar el aspecto plano necesitamos trabajar con contraste dentro de la textura, una máscara emisiva diseñada y el balance entre zonas calientes, oscuras y ambiente.
5. La regla artística no es que todo brille: cada material y criatura necesita su propia jerarquía. Las fuentes brillantes deben ser escasas y legibles; el resto de la escena conserva sombras, medios tonos, reflejos y color atmosférico.

## 1. Auditoría de Eclipse Shader

### 1.1 Qué es realmente

El README describe Eclipse como una edición de Bliss para Minecraft Java, construida para Iris y basada en trabajo previo de Chocapic13 y Bliss. Enumera, entre otras cosas, simulación de olas de agua, hierba animada, auroras y arcoíris, capas adicionales de nubes, nieve y ondulaciones de lluvia, emisivos mejorados, luz con sombras en la isla principal del End y cambios de iluminación durante rayos. El propio README señala que algunas adiciones de nubes siguen en progreso.

Fuentes primarias revisadas:
- Repositorio: https://github.com/Merlin1809/Eclipse-Shader
- README: https://github.com/Merlin1809/Eclipse-Shader/blob/Unstable/README.md
- Configuración de perfiles: https://github.com/Merlin1809/Eclipse-Shader/blob/Unstable/shaders/shaders.properties
- Fuentes Photonics: https://github.com/Merlin1809/Eclipse-Shader/blob/Unstable/shaders/ph_lights.json
- Pase de emisiones vanilla: https://github.com/Merlin1809/Eclipse-Shader/blob/Unstable/shaders/dimensions/all_vanilla_emissives.fsh
- Clasificación de materiales de entidades: https://github.com/Merlin1809/Eclipse-Shader/blob/Unstable/shaders/entity.properties
- Créditos: https://github.com/Merlin1809/Eclipse-Shader/blob/Unstable/CREDITS.txt
- Licencia visible: https://github.com/Merlin1809/Eclipse-Shader/blob/Unstable/LICENSE.md

### 1.2 Dónde se controla la iluminación

**Hallazgos confirmados por inspección estática de archivos:**

- shaders/ph_lights.json contiene 36 entradas de luz. Son fuentes de bloques, incluidas lava, bloque de magma, fuego normal y de almas, portal del Nether, antorchas, linternas, glowstone, crying obsidian, froglights y otras. No hay entradas de entidades o mobs.
- La entrada lava de Photonics usa color naranja-rojo cálido, intensidad 40, radio 10 y caída 0.25. magma_block usa intensidad 15 y radio 2. Esto es una buena lección artística: incluso dentro de una familia de color, dos materiales no tienen que irradiar igual.
- shaders/world0/gbuffers_entities_glowing.fsh activa GLOWING y shaders/world0/gbuffers_spidereyes.fsh activa SPIDER_EYES; ambas incluyen shaders/dimensions/all_vanilla_emissives.fsh. El pase usa el color de textura y un control de intensidad emisiva para generar contribución de emisión en esa capa.
- shaders/entity.properties clasifica entidades en grupos de subsurface scattering (dispersión bajo la superficie): fuerte, débil o ninguno. Esta lista **no es un catálogo de mobs luminosos**.
- shaders/lib/settings.glsl contiene controles globales para emisión, ondas y absorción del agua, tonemapping, saturación, balance de blancos y una luz direccional especial para la isla principal del End. Son parámetros de un pipeline GLSL Java; no deben copiarse mecánicamente a Bedrock.
- shaders/dimensions/all_solid.fsh contiene lógica emisiva codificada para **bloques**, mediante IDs y material. Por ejemplo, distingue sculk, glow lichen, crying obsidian, brewing stand, respawn anchor, llamas de almas y amethyst. Eso demuestra que separa materiales; no demuestra que esta lógica sea un sistema portable de luz dinámica para mobs.

### 1.3 Técnicas que sí podemos aprender

| Técnica observada | Idea reutilizable para Hogar Dorado | Límite en Bedrock |
|---|---|---|
| Emisión en un pase separado para determinadas capas | Máscaras de emisión solo en los píxeles que lo justifican | Bedrock usa sus texturas PBR y pipeline VV, no el pase GLSL de Eclipse |
| Intensidades distintas por material | Diseñar fuerza de emisión según el significado y material | Hay que usar los campos y JSON documentados para Bedrock |
| Controles de absorción del agua y ondas | Dar profundidad mediante color, normales, rugosidad y reflejos | No hay equivalencia exacta garantizada para la simulación de ondas |
| Tonemapping, balance de blancos, saturación y exposición | Tratar luces, sombras y medios tonos como un conjunto | Controles y comportamiento son distintos; se valida en juego |
| Luz direccional y sombras del End | Dar dirección, sombras reconocibles y color ambiental propio al End | Recrear con iluminación/atmósfera de Bedrock, no trasladar código |
| Nubes estratificadas y relámpagos que alteran la escena | Crear contraste ambiental sin teñir todo uniformemente | Reutilizar el objetivo visual, no asumir la misma capacidad técnica |

**Advertencia de licencia:** el archivo LICENSE.md visible dice “© 2020 Chocapic13. All rights reserved.” y CREDITS.txt identifica trabajos base y varias contribuciones. En los archivos revisados no encontramos una licencia permisiva que autorice reutilizar código o recursos. Por eso se estudian las ideas; **no se copiará código GLSL, texturas ni otros activos originales de Eclipse a Hogar Dorado**. La versión pública debe usar recursos propios o materiales con permiso/licencia compatible.

## 2. Comparación con Bedrock + Vibrant Visuals

La documentación oficial de Microsoft dice que las texturas PBR de Vibrant Visuals pueden aplicarse a bloques, entidades, mobs, partículas y objetos. Los mapas MERS representan metalness, emisión, roughness y subsurface scattering. El pack debe cumplir los requisitos actuales del manifest, incluida la capacidad pbr y una versión mínima de motor compatible.

Fuentes oficiales:
- Vibrant Visuals Resource Packs: https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/vvresourcepacks?view=minecraft-bedrock-stable
- Light Sources: https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/lightingcustomization
- Overview of PBR: https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/pbroverview?view=minecraft-bedrock-stable

### 2.1 Tres conceptos diferentes

1. **Color base de textura:** el RGB de lava, metal o piel. Aclararlo cambia el color percibido, pero no añade una máscara emisiva.
2. **Emisión superficial PBR:** el canal verde de MERS. Negro significa sin emisión; valores mayores codifican más emisión del material. Sirve para ojos, grietas, manchas o partes pequeñas.
3. **Luz local que afecta al entorno:** Bedrock documenta fuentes locales puntuales para tipos de bloque y advierte de su coste de rendimiento. No hay que prometer que unos ojos emisivos iluminarán paredes como una linterna. La emisión visible del propio mob y la luz dinámica sobre el entorno son efectos distintos.

pbr/global.json puede definir valores MERS de respaldo para categorías de material, pero no recomendamos usar una emisión global alta como sustituto de máscaras por entidad: puede iluminar superficies que deberían permanecer oscuras.

### 2.2 Implicación para lava

El mapa MERS es el lugar para definir la máscara emisiva; la textura de color conserva el relieve visual. La lava necesita una escala de valores diseñada:

- **Costras y base:** rojos quemados y naranjas oscuros; no convertir toda la imagen en un naranja uniforme.
- **Flujo medio:** naranja cálido con formas irregulares y variación entre fotogramas.
- **Grietas calientes:** pequeñas regiones amarillo-naranja que concentran el máximo de emisión.
- **Contornos y vecindad:** zonas más oscuras alrededor para que el centro caliente destaque. La luz cercana puede añadir un rebote cálido moderado, pero no borrar el detalle de la roca.

En los archivos examinados de la referencia Bedrock 26.50.0, lava_still_mers.tga (16×512) y lava_flow_mers.tga (32×512) tienen **0 píxeles con valor verde mayor que cero**. No incluyen emisión en ese canal en la referencia examinada. Esto es una observación de archivos; no es una medición del motor ni una afirmación sobre otras versiones.

## 3. Auditoría cuantitativa de mapas MERS de mobs

### Método y límites

Se inspeccionó el canal verde de archivos *_mers.tga del paquete de recursos Bedrock 26.50.0 compartido durante el proyecto. El conteo señala píxeles con emisión codificada mayor que cero; no mide el brillo percibido final ni la luz que llega al mundo. Una criatura puede usar varias texturas/capas y variantes; hay que verificar cada archivo con su texture_set.json correspondiente.

| Entidad/capa | Mapa y tamaño | Píxeles G > 0 | Máximo G (0–255) | Lectura |
|---|---:|---:|---:|---|
| Blaze | blaze_mers.tga, 64×32 | 464/2048 (22.7 %) | 15 | Emisión amplia, muy baja en el mapa base |
| Magma Cube | magmacube_mers.tga, 64×32 | 260/2048 (12.7 %) | 64 | Máscara localizada y más marcada |
| Magma Cube v2 | magmacube_v2_mers.tga, 64×64 | 104/4096 (2.5 %) | 26 | Variante más discreta en ese mapa |
| Glow Squid adulto | glow_squid_mers.tga, 64×32 | 1204/2048 (58.8 %) | 26 | Muchos píxeles con emisión suave |
| Allay | allay_mers.tga, 32×32 | 296/1024 (28.9 %) | 66 | Emisión débil extendida por parte de la textura |
| Vex | vex_mers.tga, 32×32 | 343/1024 (33.5 %) | 76 | Máscara extendida, pocos píxeles superan G=32 |
| Araña | spider_mers.tga, 64×32 | 20/2048 (1.0 %) | 41 | Máscara muy localizada, candidata a acento ocular |
| Enderman | enderman_mers.tga, 64×32 | 6/2048 (0.3 %) | 32 | Emisión mínima en el mapa base |
| Gólem de hierro | iron_golem_mers.tga, 128×128 | 0/16384 | 0 | Sin emisión en este mapa |
| Piglin | piglin_mers.tga, 64×64 | 0/4096 | 0 | Sin emisión codificada en el mapa principal |
| Piglin zombificado | zombie_piglin_mers.tga, 64×64 | 0/4096 | 0 | Sin emisión codificada en el mapa principal |
| Warden: cuerpo | warden_mers.tga, 128×128 | 80/16384 (0.5 %) | 7 | Emisión casi nula |
| Warden: capa bioluminiscente | warden_bioluminescent_layer_mers.tga, 128×128 | 0/16384 | 0 | El mapa de esta capa está a cero |
| Warden: corazón | warden_heart_mers.tga, 128×128 | 35/16384 (0.2 %) | 26 | Pequeño acento de intensidad baja |
| Warden: manchas pulsantes 1 | warden_pulsating_spots_1_mers.tga, 128×128 | 27/16384 (0.2 %) | 3 | Máscara prácticamente apagada |
| Warden: manchas pulsantes 2 | warden_pulsating_spots_2_mers.tga, 128×128 | 20/16384 (0.1 %) | 191 | Muy pocos píxeles, algunos bastante intensos |
| Guardián y guardián anciano | guardian_mers.tga y guardian_elder_mers.tga, 64×64 | 0 en ambos | 0 | Sin emisión en los mapas examinados |
| Creeper | creeper_mers.tga, 64×32 | 0/2048 | 0 | Sin emisión en el mapa base |
| Ghast normal | ghast_mers.tga, 128×64 | 0/8192 | 0 | Sin emisión en el mapa base |
| Ghast disparando | ghast_shooting_mers.tga, 128×64 | 38/8192 (0.5 %) | 51 | Acento pequeño en variante de disparo |
| Gólem de cobre: ojos | copper_golem_eyes_mers.tga, 64×64 | 8/4096 (0.2 %) | 13 | Emisión casi nula |
| Gólem de cobre: cuerpo | copper_golem_mers.tga, 64×64 | 0/4096 | 0 | Sin emisión en el mapa principal |
| Strider | strider_mers.tga, 64×128 | 0/8192 | 0 | Sin emisión en el mapa base |

### Conclusiones de la comparación

Estos valores muestran las máscaras del recurso examinado, no una lista completa de cómo se renderizan todas las criaturas. Algunas usan varias texturas, capas o variantes, y el motor puede combinar efectos.

- Blaze tiene valores MERS débiles y relativamente dispersos. El brillo excesivo visto en el juego no debe atribuirse automáticamente a ese mapa; hay que separar capas de fuego, otras texturas, ajuste emisivo global y tonemapping.
- Magma Cube ya tiene una máscara parcial útil para ensayar emisión cálida por capas; no hace falta convertir el cubo completo en lámpara.
- Allay, Vex y Glow Squid tienen valores positivos en muchas zonas, pero máximos moderados. Subirlos por igual podría convertirlos en masas luminosas sin forma.
- Los mapas principales de gólem de hierro y piglins no contienen emisión. Para obtener el efecto buscado será necesario pintar máscaras en los archivos propios del paquete de prueba o crear una capa válida para Bedrock. No basta con modificar un valor JSON global.
- En el Warden conviene validar la relación entre modelo, capa y coordenadas UV. La capa llamada bioluminescent está a cero en este MERS; las manchas pulsantes son mejores candidatas que iluminar todo el torso.

## 4. Catálogo artístico propuesto: qué mobs deberían brillar

Esto es una **propuesta de dirección artística**, no un comportamiento confirmado ni un cambio implementado. Cada criatura debe brillar por el significado de su diseño: biología, magia, energía o temperatura.

### Nivel A — emisión puntual, solo ojos o marcas

| Mob | Qué brilla | Color/intensidad propuesta | Evitar |
|---|---|---|---|
| Gólem de hierro | Solo ojos, rojo carmín oscuro, área mínima y fuerza baja-media | Contraste legible en oscuridad | No iluminar placas, cuerpo ni contorno |
| Piglin zombificado | Ojos; otras fisuras solo si existen en una capa propia | Rojo-anaranjado oscuro, por debajo de la lava | No teñir toda la cara |
| Piglin | Ojos muy contenidos; el oro refleja luz, no la emite | Ámbar rojizo o rojo profundo y bajo | No hacer que toda la armadura brille |
| Enderman | Ojos magenta; mantener el cuerpo negro | Magenta/violeta localizado | No convertir el cuerpo entero en neón |
| Araña / araña de cueva | Solo ojos donde el mapeo UV lo confirme | Rojo rubí discreto | No crear un halo que tiña toda la cueva |
| Gólem de cobre | Ojos pequeños; mantener el metal como material reflectante/desgastado | Ámbar tenue o rojo cálido, según variante | No iluminar el cuerpo cobrizo |
| Guardián / anciano | Núcleo u ojos si la capa se puede aislar | Cian/turquesa contenido | No hacer toda la criatura turquesa luminosa |
| Warden | Corazón y manchas pulsantes identificables | Teal/cian profundo, pocos puntos más intensos | No subir todo el torso ni convertir manchas en blanco |
| Phantom | Ojos pequeños o contorno muy sutil si hay una capa apropiada | Azul frío tenue | No iluminar todas las alas |
| Esqueletos, zombis, husk, drowned, pillager y otros mobs ordinarios | Sin emisión general por defecto | Nulo, salvo que haya una razón visual confirmada | No hacer que todos los hostiles nocturnos brillen |

Gólem y piglins: el jugador debe percibir un punto brillante al mirar sin sentir que la criatura porta una antorcha. Si el MERS original carece de máscara, primero hay que identificar su textura/color y coordenadas UV; no aplicar una emisión global a todos los mobs.

### Nivel B — superficie mágica o bioluminiscente

| Mob | Tratamiento propuesto | Riesgo que vigilar |
|---|---|---|
| Allay | Alas/contorno como luz muy suave; centro algo más visible | No iluminar de manera uniforme ni perder silueta |
| Vex | Emisión más fría y localizada en zonas de energía; menor superficie luminosa que Allay | Evitar que ambos parezcan el mismo efecto |
| Glow Squid | Tentáculos/manchas cian o turquesa suave, con zonas oscuras entre ellos | Evitar una masa plana que parezca una lámpara |
| Warden | Manchas pulsantes y corazón; reconstruir máscara de la capa bioluminiscente tras verificar UV | La capa correspondiente no tiene G emisivo en el mapa inspeccionado |
| Magma Cube | Algunas zonas internas/capa central naranjas; puntos calientes más intensos | Conservar aristas y zonas oscuras entre capas |
| Blaze | Varillas y núcleo con emisión cálida controlada; llama separada más brillante, sin sobreexposición | Diagnosticar antes qué capa produce el exceso actual |
| Ghast al disparar | Acento en la capa de disparo, no brillo permanente de todo el mob | Confirmar la variante y el mapeo en juego |
| Ender Dragon | Ojos o elementos energéticos si una capa separada y válida lo permite | No emitir en todo el cuerpo |

### Nivel C — materiales y efectos encendidos

- **Lava y bloque de magma:** lava recibe zonas pequeñas de máxima intensidad y más superficie emisiva que magma block. El magma debe sentirse caliente, pero no competir con una corriente abierta.
- **Fuego, soul fire y proyectiles mágicos:** diferenciar temperaturas cálidas y frías, limitando las regiones de máxima intensidad.
- **Portal del Nether:** violeta vivo concentrado en el interior; bordes y roca circundante más oscuros. No lavar la escena ni volver opaco el portal.
- **Minerales y bloques mágicos:** emitir desde las vetas/cristales y reducir el resto.
- **Ojos y equipo reflectante:** separar emisión de reflexión. Oro, cobre e hierro pueden reflejar focos calientes, pero no deben emitir solo por ser brillantes.

### Orden recomendado

1. Primera prueba: ojos del gólem, piglin zombificado y Enderman.
2. Segunda: Magma Cube, aprovechando y ajustando su máscara parcial.
3. Tercera: Blaze, después de identificar qué capa produce el exceso.
4. Cuarta: Glow Squid, Allay, Vex y Warden por separado.
5. No tocar los demás mobs por defecto. Añadir emisión solo si una meta visual concreta lo justifica.

## 5. Dirección artística extraída de las capturas

Las imágenes de referencia son muestras de estilo, no pruebas de que Eclipse implemente una técnica concreta. La cualidad común no es “más saturación”: es **contraste controlado entre temperatura, valores tonales y distancia atmosférica**.

### 5.1 Color y contraste

- **Luz cálida / sombra fría:** sol bajo, lava y fuego pueden ser ámbar/naranja, mientras sombras, nieve, niebla y agua conservan azules/índigos. Es más rico que teñir todo de naranja o morado.
- **Sombras con detalle:** conservan diferencias entre roca, vegetación, agua y criaturas. Negro absoluto borra volumen; aclarar todas las sombras aplana la escena.
- **Medios tonos tranquilos:** no todas las superficies deben competir con cielo, lava o foco mágico. Tonos medios menos saturados permiten que la región focal se perciba brillante sin crear un halo enorme.
- **Color contextual por dimensión:** End violeta/frío; Nether con rojos quemados y fuentes naranjas; bosques al atardecer con dorados y verdes aún reconocibles.
- **Agua como puente de color:** puede recoger reflejos cálidos del cielo, pero el cuerpo del agua mantiene un rango más frío y oscuro. Si toda el agua queda uniforme y celeste, pierde profundidad.

### 5.2 Profundidad atmosférica

- **Primer plano:** texturas legibles, contraste local y sombras.
- **Plano medio:** un poco menos de saturación y contraste gradual.
- **Fondo:** niebla del color del ambiente que integra horizonte y cielo, sin tapar todo a corta distancia.
- **Niebla por escena:** la niebla fría de nieve/tormenta no es la neblina cálida de atardecer ni el violeta del End. Cambiar solo el color de niebla no corrige un desequilibrio entre cielo, luz y superficies.
- **Reflejos controlados:** reflejos especulares puntuales ayudan a leer agua y metal; reflejar cada píxel con la misma intensidad parece plástico.

### 5.3 Luz y sombra

- **Dirección legible:** iluminación lateral o trasera define copas, bordes de roca y siluetas. Iluminación ambiental idéntica en todas las direcciones elimina volumen.
- **Separar luz directa e indirecta:** el sol y las fuentes directas marcan caras; la luz ambiental permite leer los lados oscuros sin borrar la sombra. El rebote de lava debe ser local, no naranja global.
- **Escala de brillo:** un foco amarillo intenso se percibe brillante por contraste con tonos más oscuros. Si la pantalla está brillante/saturada en todas partes, el foco deja de destacar.
- **Bloom no equivale a volumen:** un halo grande alrededor de Blaze o portal no sustituye sombras ni detalle. Primero hay que diseñar máscara y jerarquía de brillo; el halo es secundario.
- **Tormentas y relámpagos:** el contraste nace de nubes estratificadas y un destello puntual que altera temporalmente la iluminación, no de que todas las nubes tengan el mismo brillo.

### 5.4 Objetivo por dimensión

**Overworld:** naturaleza legible, sol dorado en atardecer, sombras azuladas con detalle, reflejos puntuales en hojas y agua fría salvo donde recoge fuentes cálidas. Mantener la identidad reconocible de Minecraft.

**Nether:** conservar roca oscura, bandas de lava con contraste interno y resplandor cálido limitado a la vecindad. Portal violeta de emisión localizada; no borrar las grietas ni la animación.

**End:** siluetas fuertes sin volverlas negras sin detalle, atmósfera índigo-violeta, focos legibles y sombras con dirección. Debe sentirse contraste entre islas, pilares, cielo y vacío; no resolverlo solo cambiando la niebla.

## 6. Plan experimental sin variables mezcladas

Se mantiene la regla: **una variable por experimento**, escena fija y captura comparable. v13 congelada sigue siendo referencia histórica y no se reemplaza.

| ID | Variable única | Escena | Criterio de aceptación |
|---|---|---|---|
| E1 | Máscara de ojos del gólem en mapa propio | Gólem sobre suelo gris neutro, día/noche | Ojos distinguibles; cuerpo y suelo no parecen iluminados |
| E2 | Máscara de ojos del piglin zombificado | Interior oscuro y Nether | Rojo visible, no inunda la cara |
| E3 | Máscara de ojos del Enderman | Noche con fondos claros y oscuros | Ojos identificables; cuerpo se mantiene oscuro |
| E4 | Emisión/contraste del Magma Cube | Cueva con piedra próxima | Capas y núcleo legibles; bordes no quemados |
| E5 | Diagnóstico de capas del Blaze | Fondo oscuro y medio tono | Menos aspecto de “todo brilla” sin perder identidad de la llama |
| E6 | Máscara MERS de lava, sin aumentar RGB global | Lava quieta y corriente | Grietas calientes y costras oscuras; tiras/animación intactas |
| E7 | Balance del End | Misma posición, hora y orientación | Identidad violeta, sombras con forma, sin velo gris uniforme |
| E8 | Atardecer y agua | Punto fijo con cielo, costa y reflejos | Cielo cálido, sombra fría y reflejo moderado |
| E9 | Rendimiento | Solo tras aprobar el aspecto | Registrar FPS orientativos, temperatura y estabilidad en iPhone; nunca inferirlo por el ZIP |

Para cada resultado registrar: versión y orden de packs activos; Vibrant Visuals; hora/bioma; distancia de renderizado; capturas antes/después; cambios exactos; defectos; FPS si se midieron; y decisión. No registrar “aprobado” si solo pasó la validación JSON/ZIP.

### Qué corregir de la hipótesis de v19

El RGB ×1.20 de lava probado en v19 fue un ajuste de color, no de emisión PBR. Como Ángel sigue percibiendo la lava plana y apagada, no conviene repetir el mismo enfoque elevando más el RGB. El siguiente experimento de lava debe crear contraste espacial entre costra y grieta y verificar una máscara MERS por fotograma. Un ajuste de luz global se prueba aparte porque puede alterar agua, mobs y biomas.

## 7. Fuentes, evidencias y límites

### Fuentes primarias consultadas

1. Microsoft Learn — Vibrant Visuals Resource Packs: https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/vvresourcepacks?view=minecraft-bedrock-stable
2. Microsoft Learn — Light Sources / iluminación VV: https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/lightingcustomization
3. Microsoft Learn — PBR overview: https://learn.microsoft.com/en-us/minecraft/creator/documents/vibrantvisuals/pbroverview?view=minecraft-bedrock-stable
4. Eclipse Shader — README: https://github.com/Merlin1809/Eclipse-Shader/blob/Unstable/README.md
5. Eclipse Shader — Photonics: https://github.com/Merlin1809/Eclipse-Shader/blob/Unstable/shaders/ph_lights.json
6. Eclipse Shader — pase emisivo: https://github.com/Merlin1809/Eclipse-Shader/blob/Unstable/shaders/dimensions/all_vanilla_emissives.fsh
7. Eclipse Shader — grupos de entidad: https://github.com/Merlin1809/Eclipse-Shader/blob/Unstable/shaders/entity.properties
8. Eclipse Shader — licencia: https://github.com/Merlin1809/Eclipse-Shader/blob/Unstable/LICENSE.md
9. Eclipse Shader — créditos: https://github.com/Merlin1809/Eclipse-Shader/blob/Unstable/CREDITS.txt

### Archivos revisados estáticamente

- Paquete de referencia ResourcePack_v26.50.0.zip aportado durante el proyecto: mapas MERS de las entidades y lava enumeradas. Es una referencia de recursos Bedrock 26.50.0; no es una lectura del motor ni un volcado certificado de la instalación 26.52.
- Repositorio público Eclipse Shader, rama Unstable: README, archivos GLSL de emisión/sombreado, clasificación de entidades, ajustes globales y fuentes Photonics.
- Capturas aportadas por Ángel: referencias visuales de iluminación/atmósfera y evidencia de defectos percibidos en pruebas previas.

### Qué no está confirmado

- No se ha compilado ni ejecutado Eclipse como shader de Java en esta sesión.
- No se ha ejecutado Minecraft Bedrock ni Creator Tools para probar estas propuestas.
- No se han medido FPS, memoria, temperatura o batería tras cambios.
- Las proporciones de píxeles MERS son conteos de archivo, no mediciones ópticas.
- No se afirma que todas las capas/variantes de cada criatura estén cubiertas.
- No se reutilizarán código o arte de Eclipse sin permiso claro.

## Decisión propuesta

No crear aún una versión nueva que mezcle lava, End y mobs. Primero implementar en una copia de prueba solo tres máscaras diminutas —gólem, piglin zombificado y Enderman— y ejecutar E1–E3. En paralelo, preparar una prueba independiente de lava con MERS y textura de color contrastada. Solo combinar cambios que hayan sido aceptados visualmente en el iPhone.

La raíz artística no cambia: **“Una nostalgia que parece un sueño”**. La prioridad es una atmósfera armónica y legible, no que todo brille más.
