# Reporte — Senado (Aerolíneas y YPF)

## Estado final

**Reconstruido con doble fuente y validado.** Ambos escenarios pasan validación con críticos 0 e importantes 0.

| Escenario | Estado | Voto | Distrito |
|---|:-:|---|---|
| Aerolíneas · Senado | ✓ | Acta nominal del Senado | SEN AMPLIADOS XLSX (Secretaría Parlamentaria HSN) |
| YPF · Senado | ✓ | Acta nominal del Senado | SEN AMPLIADOS XLSX (Secretaría Parlamentaria HSN) |

## El problema metodológico

Las actas de votación nominal del Honorable Senado de la Nación, a diferencia de las de Diputados, **no incluyen la columna Provincia / Distrito**. El acta sólo lista Apellido y Nombre, junto al voto. Para construir un dataset apto para visualización territorial fue necesario apoyarse en una segunda fuente canónica: los archivos **SEN AMPLIADOS** publicados por la Secretaría Parlamentaria del HSN, que sí incluyen, para cada senador, el distrito que representa, el bloque y el voto.

Esto explica por qué los dos escenarios de Senado tienen, en el panel de validación, un banner verde diferenciado: **"Datos validados con doble fuente. Voto reconstruido desde acta nominal del Senado; distrito/provincia reconstruido desde fuente canónica SEN AMPLIADOS XLSX"**.

## Aerolíneas · Senado (17/12/2008)

- Acta nominal Nº 4, 21ª Sesión Ordinaria de Prórroga, 126° Período Legislativo, votación en general y en particular. Presidente: **PAMPURO, José Juan Bautista** (vicepresidente provisional del Senado, senador por Buenos Aires).
- Archivos: `fuentes/aerolineas_senado/VOTO SENADORES AAAA.pdf` (acta nominal), `fuentes/aerolineas_senado/SEN AMPLIADOS AAAA.xlsx` (distrito/bloque).

| Métrica | Resultado |
|---|:-:|
| Total registros HTML | 72 |
| Total registros XLSX | 72 |
| Match por (apellido + primer nombre) | **72 / 72** ✓ |
| Confianza alta (nombre exacto) | 67 |
| Confianza media (diferencia ortográfica menor) | 5 |
| Confianza baja | 0 |
| Provincias corregidas | **23** |
| Cambios de voto | 0 (se preservó PAMPURO como PRESIDENCIA) |
| 24 jurisdicciones × 3 senadores | ✓ |
| Conteos vs metadata oficial | cuadran ✓ |

### Modificaciones del acta documentadas
- **PINCHETTI DE SIERRA MORALES, Delia**: AUSENTE → NEGATIVO según informe de auditoría del acta.
- **JENEFES, Guillermo Raúl**: deja constancia de voto negativo en Artículo 1°.

### Conteos finales

| Categoría | Cantidad |
|---|---:|
| AFIRMATIVO | 41 |
| NEGATIVO | 21 |
| ABSTENCION | 0 |
| AUSENTE | 9 |
| PRESIDENCIA | 1 (Pampuro) |
| **Total bancas** | **72** |

La metadata oficial del acta cuenta a Pampuro como afirmativo (yes = 42). La validación interna tolera la convención: `AFIRMATIVO + PRESIDENCIA == 41 + 1 == 42 == meta.yes`. El dataset conserva PAMPURO con `vote: PRESIDENCIA` por la decisión metodológica de tratar la presidencia del cuerpo como **categoría institucional**, separada del voto político concreto.

## YPF · Senado (26/04/2012)

- Acta nominal Nº 1, 1ª Sesión Especial, 130° Período Legislativo, votación en general. Presidente: **BOUDOU, Amado** (vicepresidente de la Nación; no es senador y por lo tanto no figura como banca).
- Archivos: `fuentes/ypf_senado/VOTO SENADORES YPF.pdf` (acta nominal), `fuentes/ypf_senado/SEN AMPLIADOS YPF.xlsx` (distrito/bloque).

| Métrica | Resultado |
|---|:-:|
| Total registros HTML | 72 |
| Total registros XLSX | 72 |
| Match por (apellido + primer nombre) | **72 / 72** ✓ |
| Confianza alta (nombre exacto) | 63 |
| Confianza media (diferencia ortográfica menor) | 9 |
| Confianza baja | 0 |
| Provincias corregidas | **19** |
| Cambios de voto | 0 |
| 24 jurisdicciones × 3 senadores | ✓ |
| Conteos vs metadata oficial | cuadran ✓ |

### Conteos finales

| Categoría | Cantidad |
|---|---:|
| AFIRMATIVO | 63 |
| NEGATIVO | 3 |
| ABSTENCION | 4 |
| AUSENTE | 2 |
| PRESIDENCIA | 0 |
| **Total bancas** | **72** |

## Tratamiento de nombres con confianza media

Los XLSX SEN AMPLIADOS usan en algunos casos formas más completas del nombre que las actas nominales (por ejemplo, "COLOMBO de ACEVEDO, María Teresita del Valle" vs "COLOMBO DE ACEVEDO, María Teresa", o "PINCHETTI DE SIERRA MORALES, Delia Norma" vs "Delia"). En todos los casos identificados son la **misma persona**. Decisión metodológica: **se preservan los nombres del dataset HTML** (que provienen del acta nominal). El nombre del XLSX queda en la columna `nombre_xlsx` de la trazabilidad.

Casos con apellido compuesto resueltos con alias manual:

| XLSX | HTML | Comentario |
|---|---|---|
| GIRI, Haide Delia | GIRI, Haydé Delia | sólo acento en mayúsculas |
| AGUIRRE, Hilda Clelia | AGUIRRE DE SORIA, Hilda Clelia | apellido compuesto |
| CABRAL, Salvador | CABRAL ARRECHEA, Salvador | apellido compuesto |

## Metadata agregada al SCENARIOS

Cada uno de los dos escenarios de Senado lleva ahora tres campos extras en el objeto del escenario:

```javascript
voteSource:         'Acta nominal del Senado de la Nación',
districtSource:     'SEN AMPLIADOS XLSX (Secretaría Parlamentaria, HSN) — <archivo>.xlsx',
territorialValidation: true,
```

El panel de validación detecta automáticamente la presencia de `voteSource + districtSource` y muestra el banner verde reforzado.

## Trazabilidad

Una fila por senador, con la procedencia de cada campo:

- `trazabilidad/sen_traza_aerolineas_senado.csv` (72 filas, 16 columnas).
- `trazabilidad/sen_traza_ypf_senado.csv` (72 filas, 16 columnas).

Columnas: `escenario`, `nombre_html`, `nombre_xlsx`, `voto_html`, `voto_xlsx`, `provincia_html`, `provincia_xlsx`, `bloque_html`, `bloque_xlsx`, `familia_html`, `fuente_voto`, `fuente_distrito`, `confianza`, `cambia_provincia`, `cambia_voto`, `observaciones`.

## Backup previo
`backups/pre_senado/votaciones_consolidado.bak_pre_senado.html` (preserva el HTML antes de la reconstrucción territorial del Senado).
