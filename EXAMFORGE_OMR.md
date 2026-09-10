# ExamForge + OMR — qué cambia y cómo probarlo

`index.html` sigue siendo un único fichero sin build. Se le han añadido dos
cosas: las hojas de examen ahora llevan casillas legibles por máquina, y hay
un módulo nuevo que corrige los escaneos dentro del propio navegador.

## Lo que cambia en la generación

El formato antiguo (A4 apaisado, rejilla 5×2 y círculos) **se ha eliminado**:
no queda en el código ni en la interfaz, para no dar a elegir entre dos hojas
que no son intercambiables. Solo hay un formato, **A4 vertical a dos
columnas**, con el diseño validado en papel:

- Un **cuadrado** delante de cada opción, que el estudiante rellena.
- Cuatro **marcas de registro** negras en las esquinas, a 10 mm del borde.
  Son la referencia geométrica: con ellas el corrector calcula la homografía
  y compensa el giro, la escala y la deformación del escaneo.
- Dos **tiras de 12 bits** en el pie, idénticas entre sí. Codifican el número
  de versión (10 bits) y el número de página dentro del examen (2 bits). Se
  leen las dos y se vota: si una queda emborronada, vale la otra. Así el
  corrector sabe qué examen tiene delante sin depender del OCR del texto.
- Cabecera en negro sobre blanco, sin fondos de color, para ahorrar tinta.

Un examen puede ocupar varias páginas: las preguntas fluyen por las dos
columnas y, cuando no caben, se abre otra hoja con su propia cabecera, sus
marcas y su código de bits.

### Botones nuevos en la pantalla de resultados

- **PDF para imprimir (todos)** — un único PDF con todos los exámenes, uno
  detrás de otro. Es lo que se manda a imprimir.
- **plantilla_omr.json** — el fichero que necesita el corrector: coordenadas
  exactas de las 5 casillas de cada pregunta de cada versión, posiciones de
  las marcas y de los bits, y la clave de respuestas.
- **Corregir escaneos** — abre el módulo de corrección.

En la tarjeta de cada examen queda **Reimprimir hoja**, que vuelve a generar
esa versión concreta (por si un estudiante pierde la suya) con el mismo
número de versión, de modo que la plantilla ya descargada sigue sirviendo.

**El PDF y el JSON van juntos.** Si regeneras los exámenes, el JSON anterior
deja de servir: las coordenadas ya no corresponden.

## El módulo de corrección

Se abre desde la pantalla de resultados o desde el enlace de la pantalla
inicial (para corregir días después, sin volver a generar nada). Pide:

1. La **plantilla JSON** de esos exámenes.
2. El **PDF escaneado** con todas las hojas, o las imágenes sueltas.
3. La **penalización** por fallo, si se aplica.

Qué hace con cada hoja:

1. La rasteriza con pdf.js a unos 1700 px de ancho.
2. Localiza las cuatro marcas: binariza por Otsu, busca componentes conexas
   cuadradas y sólidas del tamaño esperado, y de las candidatas de cada
   esquina se queda con la combinación que forma el rectángulo previsto. La
   búsqueda se hace sobre una copia reducida y el centro se refina luego a
   resolución completa.
3. Calcula la homografía (sistema 8×8) que lleva las coordenadas del PDF a
   las de la imagen.
4. Lee las dos tiras de bits y decodifica versión y página.
5. Mide la proporción de tinta dentro de cada casilla y decide: marca clara →
   respuesta; marca floja pero única → respuesta; nada → en blanco; empate o
   dos marcas → `?` y la hoja queda en estado `revisar`.
6. Junta las páginas de un mismo examen y calcula la nota.

La tabla de notas muestra, para cada pregunta, lo leído y lo correcto
(`B/E`), en verde si acierta y en rojo si falla, y marca en la columna
"Revisar" las preguntas dudosas. El CSV que se descarga lleva por pregunta
tres columnas: leída, letra correcta y texto de la respuesta correcta.

Todo ocurre en el navegador: los escaneos no salen del ordenador.

## Impresión y escaneo

- A4, **escala 100%** (no "ajustar a la página"), una cara. La geometría
  depende de que la hoja salga a tamaño real.
- Escaneo a 200–300 ppp, gris o color. No hace falta que esté recta.

## Estados que puede devolver una hoja

| Estado | Qué significa |
|---|---|
| `ok` | Leída sin incidencias. |
| `revisar` | Alguna pregunta con dos marcas, un borrón o una marca a medias. |
| `faltan hojas` | Del examen se ha leído una página pero no las demás. |
| `sin marcas de registro` | Hoja muy recortada, muy oscura o girada de más. |
| `version ilegible` / `version ambigua` | Las dos tiras de bits no coinciden; el código impreso arriba permite arreglarlo a mano. |

## Cómo se ha probado

El circuito completo se ha ejecutado fuera del navegador, con el mismo código
que va dentro del HTML: generación del PDF con jsPDF, rasterizado a 200 ppp,
simulación de casillas rellenadas a boli (trazos irregulares), degradación
tipo escáner (giro de ±3°, cambio de escala, desplazamiento, sombra lateral y
ruido) y corrección. Resultado: todas las versiones recuperadas con la
versión, la página y las respuestas exactas, incluidos exámenes de dos hojas.

## Ficheros de apoyo

Para poder repetir esas pruebas, en `pruebas/` van los scripts que se han
usado (Node + Python con OpenCV). No hacen falta para usar la aplicación.
