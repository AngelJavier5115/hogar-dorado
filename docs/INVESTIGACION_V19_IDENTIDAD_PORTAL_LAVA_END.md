# Hogar Dorado v19 — identidad visual, portal, lava y cielo del End

**Fecha:** 2026-10-15  
**Base:** v18 restaurada.  
**Evidencia:** capturas reales de Ángel en Bedrock 26.52.  
**Estado:** paquete generado y validado estáticamente; prueba visual pendiente.  
**Artefacto local:** hogar-dorado-v19-identidad-restaurada-PRUEBAS-PERSONALES.mcpack  
**Tamaño:** 190,641 bytes.  
**SHA-256:** 1530bddfe295169a1b6c032437f90ddd97fc218ce9d1240f0352dbcbf9543a7a

## Dirección artística que debe conservarse

Raíz del proyecto: **«Una nostalgia que parece un sueño»**. El pack debe evocar familiaridad cálida, atardeceres dorados, noches índigo y misterio sutil; preservar la identidad reconocible de Minecraft y complementar otros packs/addons en lugar de competir con ellos. Los últimos cambios técnicos estaban haciendo que la identidad se perdiera, así que esta iteración vuelve a priorizar el aspecto visual y limita los cambios al portal, la lava y el End.

## Cambios en v19

### Portal: más translúcido

- Se conserva la textura animada restaurada de v18, 16 × 512.
- Se redujo el canal alfa de cada píxel al 80 % de su valor anterior, sin alterar RGB ni las dimensiones de la tira.
- El alfa resultante va de 124 a 186.

Esto es una prueba de transparencia de textura; hay que confirmar dentro de Bedrock si el renderizador del portal respeta el cambio como se espera. Si no cambia la transparencia visual, la siguiente investigación debe centrarse en el material/render method del bloque, no seguir bajando alfa a ciegas.

### Lava: +20 % de brillo de color

- Se multiplicaron por 1.20 los canales RGB de lava_still y lava_flow, limitando valores a 255.
- Se preservaron las dimensiones de las tiras animadas: lava_still 16 × 512; lava_flow 32 × 512.
- No se incrementó la emisión PBR: v17 había mostrado que la emisión excesiva lava el detalle. Este ajuste afecta el color de la textura y busca un resultado más luminoso sin borrar la estructura de la lava.

El 20 % es un ajuste numérico de los canales de color, no una promesa de que la luminancia percibida en el juego suba exactamente un 20 %.

### End: menos neblina y cielo índigo

Las capturas de v18 seguían mostrando un cielo gris/granulado y una neblina que ocultaba el terreno. v19 prueba una hipótesis más marcada, manteniendo el End distinto del mundo normal:

- Color de cielo del bioma: #7560B8.
- Color de niebla aérea: #302344.
- Inicio de niebla aérea: 0.90; final: 1.0.
- Densidad volumétrica aérea máxima: 0.0008, reducida desde 0.0028.
- Dispersión/absorción aérea más baja y con componente índigo.

No se cambiaron los perfiles de ambiente global ni los colores dorados/índigo del mundo normal. La causa del cielo gris puede incluir el skybox/renderizado propio del End y la interacción entre atmósfera y niebla; esta modificación no se considera resuelta hasta ver nuevas capturas.

## Validación estática

- Integridad ZIP: correcta.
- 176 archivos; 119 JSON parseados correctamente.
- Portal: 16 × 512, alfa entre 124 y 186.
- Lava quieta: 16 × 512.
- Lava en flujo: 32 × 512.
- Densidad aérea máxima del End: 0.0008.
- Manifiesto con identidad nueva para evitar confundir la importación con v18.
- SHA-256: 1530bddfe295169a1b6c032437f90ddd97fc218ce9d1240f0352dbcbf9543a7a.

No se ha probado v19 dentro del juego ni se ha medido rendimiento en el iPhone 14 Plus. Las validaciones estáticas no garantizan la apariencia visual.

## Protocolo de prueba

1. Desactivar las otras versiones de Hogar Dorado y activar solo v19.
2. En la misma escena, comparar el portal con v18: ¿se ve más translúcido y conserva los tonos violetas/partículas?
3. Comparar lava quieta y en flujo: ¿se nota más viva sin perder detalle ni parecer una superficie amarilla plana?
4. En el End, comparar la misma vista de torres e isla: ¿se ve un cielo más índigo y se distingue más el terreno?
5. Si el cielo sigue gris, no seguir tocando al azar el color del bioma: revisar la interacción entre skybox del End, atmósfera global, fog y color grading con documentación específica de Bedrock 26.52.

## Procedencia

El .mcpack incluye recursos vanilla de referencia suministrados para pruebas personales; se mantiene fuera de GitHub y no debe publicarse ni venderse. El repositorio contiene únicamente documentación del experimento.
