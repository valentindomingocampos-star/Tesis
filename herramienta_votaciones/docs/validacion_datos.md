# Validación de datos

La herramienta corre un conjunto de chequeos cada vez que carga un escenario y muestra el resultado en el panel superior del módulo. Este documento describe qué se valida y cómo se interpreta el banner.

## Estados del panel

El panel de validación tiene tres estados visuales:

| Estado | Cuándo aparece | Qué significa |
|---|---|---|
| 🟢 Verde "Datos validados" | conteos de voto OK + distribución territorial OK | El dataset cierra contra la metadata oficial y contra la composición institucional esperada |
| 🟡 Amarillo "Dataset bajo revisión territorial" | conteos de voto OK pero distribución territorial NO | Los votos cuadran con el acta pero al menos una jurisdicción no tiene la cantidad esperada de bancas. Las visualizaciones por provincia deben leerse con cautela |
| 🔴 Rojo "Inconsistencias críticas en conteos" | conteos de voto NO | Hay diferencia entre los registros del dataset y los conteos oficiales (afirmativos, negativos, etc.). El escenario no se puede usar sin corregir |

Adicionalmente, en el banner verde, si el escenario declara `voteSource` y `districtSource` (caso típico de Senado), el texto se refuerza con "**Datos validados con doble fuente**" y cita la procedencia de cada campo.

## Reglas territoriales

**Senado de la Nación.** 24 jurisdicciones × 3 senadores = **72 bancas**. Si alguna jurisdicción no tiene exactamente 3 senadores, dispara el estado amarillo y aparece la tabla `Provincia / Bancas esperadas / Registros cargados / Diferencia / Estado` en el propio panel.

**Cámara de Diputados.** Distribución por distrito según Ley 22.847 (vigente para 2008 y 2012):

| Distrito | Bancas |
|---|---:|
| Buenos Aires | 70 |
| CABA | 25 |
| Santa Fe | 19 |
| Córdoba | 18 |
| Mendoza | 10 |
| Entre Ríos · Tucumán | 9 |
| Chaco · Corrientes · Misiones · Salta · Santiago del Estero | 7 |
| Jujuy · San Juan | 6 |
| Catamarca · Chubut · Formosa · La Pampa · La Rioja · Neuquén · Río Negro · San Luis · Santa Cruz · Tierra del Fuego | 5 |
| **Total** | **257** |

## Validaciones internas (resumen)

El JS de la herramienta corre estas comprobaciones en cada escenario:

1. **Total de registros vs bancas declaradas.** El array de legisladores tiene que tener el largo del campo `totalSeats` del escenario.
2. **Afirmativos vs metadata.** Tolera la convención de presidencia: acepta `AFIRMATIVO == meta.yes` o `AFIRMATIVO + PRESIDENCIA == meta.yes`.
3. **Negativos, abstenciones, ausentes** vs metadata.
4. **Presentes calculados vs metadata.** Tolera presidencia: acepta `present == meta.present` o `present − PRESIDENCIA == meta.present`.
5. **Campos obligatorios completos.** Cada registro tiene que tener `name`, `block`, `family`, `province`, `vote`.
6. **Voto dentro del set válido.** {AFIRMATIVO, NEGATIVO, AUSENTE, ABSTENCION, PRESIDENCIA}.
7. **Familia reconocida.** Las 10 familias de `FAMILY_COLORS`.
8. **Provincia normalizada.** Las 24 jurisdicciones canónicas; los alias se resuelven antes de contar.
9. **Sin nombres duplicados.**

## Validación territorial agregada

Para cada escenario, además de los chequeos por registro, se construye una tabla `provincia → cantidad cargada` y se compara contra la distribución esperada. Las filas con diferencia se marcan con uno de tres estados:

- **FALTAN N** — registrada menos cantidad que las bancas esperadas.
- **SOBRAN N** — registrada más cantidad que las bancas esperadas.
- **NO_RECONOCIDA** — la provincia que aparece en el dataset no está en el set canónico.

Si todas las filas son OK, el estado del escenario es **verde**.

## Buenas prácticas para no romper la validación

- No agregar registros sin verificar que la provincia esté normalizada.
- No mezclar `block` (etiqueta del acta) con `family` (clasificación analítica).
- No reescribir `PRESIDENCIA` como `AFIRMATIVO` aunque el conteo cuadre de otra forma: la categoría institucional se preserva porque importa para visualizar correctamente.
- Si una provincia aparece con un nombre nuevo, agregar el alias en `PROVINCE_ALIASES` antes de tocar el dataset.
- Si una familia política aparece nueva, agregarla a `FAMILY_COLORS` antes de tocar el dataset; si no, todos los registros con esa familia van a saltar como "familia no reconocida".

## Convención para reconstrucciones futuras

Cuando una sesión legislativa nueva exija cargar un escenario más:

1. Conseguir la fuente primaria (acta nominal de la cámara correspondiente).
2. Si el acta no incluye la columna provincia, conseguir además una fuente canónica de composición institucional con distrito por legislador (la Secretaría Parlamentaria publica esos archivos en formato XLSX).
3. Construir el dataset y correr la validación interna antes de declararlo terminado.
4. Generar una tabla de trazabilidad si hubo cruce con doble fuente.
5. Documentar fechas, expediente, OD y presidente en `SCENARIOS` con los mismos campos que los actuales (`voteSource`, `districtSource`, `territorialValidation` si corresponde).
