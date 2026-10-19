# ExamForge

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.21423287.svg)](https://doi.org/10.5281/zenodo.21423287)

Exámenes tipo test únicos para cada estudiante, con corrección automática de las hojas escaneadas. Aplicación web de un solo fichero (`index.html`), sin servidor.

**Usar la app:** https://fborrasumh.github.io/examforge/

**Idiomas de la interfaz:** español (por defecto), inglés y portugués; selector en la barra superior (o `?lang=en` / `?lang=pt` en la URL). Los exámenes se pueden redactar en 15 lenguas de alfabeto latino de Europa occidental.

## Qué hace

1. **Material.** Pegas el texto de la asignatura o subes PDF, Word, PowerPoint o texto; se leen en el navegador.
2. **Examen.** Asignatura, título impreso, número de preguntas (5–50), 4 o 5 opciones, dificultad, idioma del examen, instrucciones para la IA y lista de estudiantes. Puedes pedir un modelo distinto por estudiante o un número menor de modelos (cada estudiante recibe un modelo con las preguntas barajadas). Antes de generar ves cuántas llamadas a la IA se harán.
3. **Generación** con cuatro agentes: analista (conceptos y hechos del material), generador (preguntas con una cita literal del material que respalda la respuesta correcta), validador (revisa si la respuesta declarada es la única correcta y marca las ambiguas) y formateador (asigna exámenes, códigos y versiones).
4. **Revisión e impresión.** Banco de preguntas por modelo con los avisos del código; puedes cambiar a mano la respuesta correcta. Descargas: PDF para imprimir (A4 vertical, dos columnas, casillas, marcas de registro y código de versión en bits), plantilla de corrección JSON, clave de respuestas CSV y banco de preguntas en Word con las citas. Reimpresión de una versión suelta con la misma plantilla.
5. **Corrección** de los escaneos (PDF o imágenes): localiza las marcas, corrige giro y escala con una homografía, lee la versión y mide la tinta de cada casilla. Las marcas dudosas o dobles salen como «?» y las decides tú antes de exportar las notas (CSV, con penalización opcional). Incluye una prueba con hojas simuladas para comprobar el circuito antes del examen real.

## Qué comprueba el código (y qué hace la IA)

La IA analiza, redacta y revisa. El código comprueba, sin IA:

- que la **cita** de cada pregunta aparece literalmente en tu material (si no, ⚠);
- opciones **repetidas**, opciones que **remiten a otras** («todas las anteriores», «A y B»), **cifras** que no están en el material, opciones o letra correcta **ausentes**, preguntas **repetidas** en un modelo;
- **caracteres que el PDF no puede imprimir** (la fuente estándar solo cubre Windows-1252);
- el **reparto de la letra correcta** entre A–E, que hace el código y no la IA;
- la **lectura óptica** de las hojas.

Una cita verificada significa que el texto citado existe en el material, no que la pregunta sea perfecta.

## Cómo se usa la IA

Con la propia clave de OpenAI, Google Gemini o Anthropic Claude, elegida en el botón «IA». La clave se guarda solo en el navegador y cada persona paga su propio uso. El ejemplo de la portada funciona sin clave.

## Privacidad

- Se quedan en el navegador: los nombres de los estudiantes (no se envían nunca a la IA), los exámenes, los PDF, la plantilla, los escaneos y las notas.
- Sale hacia el proveedor de IA elegido: el texto del material, con correos, DNI/NIE y teléfonos enmascarados, y las preguntas para que el validador las revise. Antes del primer envío de cada sesión se muestra una muestra de lo que sale.

## Límites

- La IA puede redactar preguntas ambiguas o con más de una respuesta defendible: revisa siempre el banco antes de imprimir. Por coste, si hay más de tres modelos el validador revisa solo el primero, salvo que marques «Revisión de la IA de todos los modelos».
- Con material muy largo, cada lote del generador ve un fragmento de unos 40 000 caracteres además del análisis completo.
- El PDF imprime bien el alfabeto latino de Europa occidental; otros caracteres (griego, ≥, Ł…) se sustituyen o salen como «?» y se avisa.
- La lectura óptica requiere imprimir en A4 a escala 100 %. Falla con hojas muy recortadas, impresas a otra escala o con marcas muy tenues; esas hojas se marcan. Máximo 1023 versiones por tanda (10 bits).
- El PDF y la plantilla van juntos: si cambias una respuesta después de descargar la plantilla, descarga ambos de nuevo (la app avisa).

## Novedades de la versión 2.0

Diseño Forja; interfaz en español, inglés y portugués; OpenAI, Gemini o Claude; citas verificadas y avisos por código; revisión y cambio manual de la respuesta correcta; decisión manual de las marcas dudosas; banco en Word; prueba con hojas simuladas. Corrige un fallo de la 1.x: con exámenes de 4 opciones la lectura fallaba. Mejora la detección de preguntas en blanco en fotos y escaneos con sombras. Las plantillas JSON de la 1.x siguen siendo válidas.

## Cómo se ha probado

Pruebas automáticas en Chromium con IA simulada que miente a propósito (cita inventada, «todas las anteriores», cifras inventadas, caracteres no imprimibles, opción ausente, JSON roto, identificadores inexistentes, error 429), con los tres proveedores; idiomas, exportaciones, persistencia, móvil y CSP. Lectura óptica: hojas simuladas (giro, escala, ruido y sombra) y el escaneo real con móvil incluido en el repositorio (`Examen2.pdf` con `plantilla_omr_eval1.json`). No se ha probado con claves reales de IA ni con un lote grande de escaneos reales.

## Autoría

Fernando Borrás Rocher (Universidad Miguel Hernández de Elche).

ORCID: Fernando Borrás Rocher [0000-0002-5519-4573](https://orcid.org/0000-0002-5519-4573)

## Cómo citar

Borrás Rocher, F. (2026). *ExamForge* (v2.0.0) [Software]. Zenodo. https://doi.org/10.5281/zenodo.21423287

## Licencia

MIT. Véase [LICENSE](LICENSE).
