# Metodología y fuentes

Este documento explica de dónde sale cada campo del dataset y cómo se trataron los casos especiales. Pensado para acompañar al anexo metodológico de la tesis.

## Estructura general del registro

Cada legislador queda representado por un objeto con cinco campos obligatorios:

```
name      → apellido y nombre tal como figura en el acta oficial
block     → bloque político tal como figura en el acta
family    → familia política (clasificación analítica, 10 categorías)
province  → distrito al que representa
vote      → uno de: AFIRMATIVO · NEGATIVO · ABSTENCION · AUSENTE · PRESIDENCIA
```

## Fuentes por escenario

### Aerolíneas Argentinas · Cámara de Diputados (Ley 26.466, 03/12/2008)

Acta oficial de la Honorable Cámara de Diputados de la Nación. Expediente **6538-D-08**, Orden del Día **1342**. Votación en general, 34ª Reunión, 1ª Sesión Especial de Prórroga del 126° Período Legislativo. Presidente: **FELLNER, Eduardo Alfredo**.

El acta de Diputados incluye explícitamente la columna **Provincia** junto a Apellido y Nombre, Bloque Político y Voto. Por lo tanto, los cuatro campos (`name`, `block`, `province`, `vote`) **provienen directamente del acta nominal**.

Archivo: `fuentes/aerolineas_diputados/VOTO DIPUTADOS AAAA.pdf`.

### YPF · Cámara de Diputados (Ley 26.741, 03/05/2012)

Acta oficial HCDN. Expediente **29-S-12**, Orden del Día **288**. Votación en general, 7ª Reunión, 5ª Sesión Especial del 130° Período Ordinario. Presidente: **DOMÍNGUEZ, Julián**.

Mismo formato que el caso anterior: la columna Provincia aparece en el acta. Los cuatro campos vienen del acta nominal.

Archivo: `fuentes/ypf_diputados/VOTO DIPUTADOS YPF.pdf`.

### Aerolíneas Argentinas · Senado (Ley 26.466, 17/12/2008)

Acta nominal Nº 4 del Honorable Senado de la Nación, 21ª Sesión Ordinaria de Prórroga, 126° Período Legislativo. Votación en general y en particular. Presidente: **PAMPURO, José Juan Bautista** (vicepresidente provisional, senador por Buenos Aires).

El acta del HSN **sólo lista Apellido y Nombre + Voto**: no incluye la columna Provincia. Por eso `name`, `block` y `vote` salen del acta, pero `province` se reconstruyó desde una fuente externa.

- `name` y `vote`: `fuentes/aerolineas_senado/VOTO SENADORES AAAA.pdf`.
- `block` y `province`: `fuentes/aerolineas_senado/SEN AMPLIADOS AAAA.xlsx` (publicación de la Secretaría Parlamentaria del HSN con datos ampliados por senador).

Cruce 72/72 con confianza alta (67) o media por diferencias ortográficas menores (5).

### YPF · Senado (Ley 26.741, 26/04/2012)

Acta nominal Nº 1 del HSN, 1ª Sesión Especial del 130° Período Legislativo. Votación en general. Presidente: **BOUDOU, Amado** (vicepresidente de la Nación; no es senador, por eso no figura como banca).

Misma estructura que el anterior: `name` y `vote` desde el acta nominal; `block` y `province` desde el XLSX ampliado de la Secretaría Parlamentaria del HSN.

- `name` y `vote`: `fuentes/ypf_senado/VOTO SENADORES YPF.pdf`.
- `block` y `province`: `fuentes/ypf_senado/SEN AMPLIADOS YPF.xlsx`.

Cruce 72/72 con confianza alta (63) o media por diferencias ortográficas menores (9).

## Casos especiales documentados

### Presidencia del cuerpo

La categoría `PRESIDENCIA` no es un voto: es una **marca institucional** para identificar a quien presidió la sesión. Se usa de manera consistente en los cuatro escenarios:

- Si quien preside es legislador del cuerpo y aparece como autoridad en el acta, queda con `vote: 'PRESIDENCIA'`.
- Esto **no contradice** la metadata oficial: cuando el acta cuenta al presidente como afirmativo, la validación interna acepta que `AFIRMATIVO + PRESIDENCIA == metadata.yes`.
- Si quien preside no es legislador del cuerpo (por ejemplo, el vicepresidente de la Nación presidiendo el Senado), no figura como banca.

Casos concretos:

| Escenario | Presidente | Tratamiento |
|---|---|---|
| Aerolíneas · Diputados | Fellner | `vote: PRESIDENCIA`, bloque etiquetado como `Presidencia` |
| Aerolíneas · Senado | Pampuro (senador BA) | `vote: PRESIDENCIA`. El acta lo cuenta como afirmativo |
| YPF · Diputados | Domínguez | `vote: PRESIDENCIA`. No aparece en el listado del acta; se incorpora explícitamente para cerrar las 257 bancas |
| YPF · Senado | Boudou (Vicepresidente, no senador) | No aparece como banca |

### Modificaciones del acta

Algunas actas registran modificaciones formales posteriores a la sesión. La versión que figura en el dataset es siempre la **versión final del sistema parlamentario**:

- **KATZ, Daniel** (Aerolíneas Diputados): ABSTENCIÓN → AFIRMATIVO (modificación del 03/12/2008 19:50). Queda como AFIRMATIVO.
- **DE MARCHI, Omar Bruno** (Aerolíneas Diputados): institucionalmente AUSENTE. El acta deja constancia de su "voto por la NEGATIVA a viva voz" como observación, pero no afecta el conteo del sistema.
- **GARCÍA, Andrea** (YPF Diputados): ABSTENCIÓN → AFIRMATIVO (modificación del 03/05/2012 21:47). Queda como AFIRMATIVO.
- **PINCHETTI DE SIERRA MORALES, Delia** (Aerolíneas Senado): AUSENTE → NEGATIVO según informe de auditoría del acta.
- **JENEFES, Guillermo Raúl** (Aerolíneas Senado): deja constancia de voto negativo en Artículo 1° (informe de auditoría).

### Familias políticas

La asignación de bloque → familia es una **clasificación analítica** del trabajo, no aparece en el acta. Se basa en afinidad programática y cabecera de bloque, no en alineamiento legislativo coyuntural. La paleta de familias se mantiene constante en los 10 valores: FPV-PJ, UCR, PRO, Coalición Cívica, Socialista, Peronismo disidente, Centroizquierda, Concertación / CF, Provinciales / otros, Otros.

Por decisión metodológica explícita:

- **Nuevo Encuentro** (Heller, Sabbatella, Raimundi, Junio, Harispe) queda como Centroizquierda, no como FPV-PJ, aunque haya acompañado al oficialismo en estos votos.
- **Frente Cívico por Santiago** (Zamora) queda como Provinciales / otros, no como FPV-PJ.

Esta separación entre **identidad partidaria** y **alineamiento legislativo** se mantiene para no contaminar la lectura del dataset. Si en el futuro se agrega una capa `alignment` (Oficialismo / Aliado / Oposición / Provincial / Mixto), va a ser un campo separado.

### Normalización territorial

Los nombres de provincia se normalizan al canónico de la herramienta antes de cualquier conteo o visualización. Aliases reconocidos:

| Aparece como | Se normaliza a |
|---|---|
| Cdad. Aut. Bs. As. / Ciudad Autónoma de Buenos Aires / Capital Federal | CABA |
| Rio Negro | Río Negro |
| Tierra del Fuego, Antártida e Islas del Atlántico Sur | Tierra del Fuego |
| Cordoba / Neuquen / Tucuman / Entre Rios | Córdoba / Neuquén / Tucumán / Entre Ríos |

## Trazabilidad

Para los dos escenarios de Senado, donde se aplicó reconstrucción territorial con fuente externa, hay un CSV por escenario con una fila por senador y las siguientes columnas:

- `nombre_html`, `nombre_xlsx`: el nombre en cada fuente.
- `voto_html`, `voto_xlsx`: voto en cada fuente (cuando el XLSX lo trae).
- `provincia_html`, `provincia_xlsx`: distrito en cada fuente.
- `bloque_html`, `bloque_xlsx`: bloque en cada fuente.
- `familia_html`: clasificación analítica del trabajo.
- `fuente_voto`, `fuente_distrito`: origen de cada campo.
- `confianza`: ALTA si el nombre coincide exactamente; MEDIA si difiere en grado de detalle (segundo nombre, acentos en mayúsculas).
- `cambia_provincia`, `cambia_voto`: SI/NO respecto al dataset previo.
- `observaciones`: campo libre.

Archivos: `trazabilidad/sen_traza_aerolineas_senado.csv` y `trazabilidad/sen_traza_ypf_senado.csv`.
