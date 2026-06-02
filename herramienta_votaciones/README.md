# Herramienta interactiva de visualización legislativa

> **Versión:** v1.0 — versión estable para tesis (2026-05-19).

Plataforma autocontenida (HTML + CSS + JS) para visualizar las **votaciones nominales** asociadas a la reestatización de empresas estratégicas argentinas: **Ley 26.466** (Aerolíneas Argentinas, 2008) y **Ley 26.741** (YPF, 2012), en sus tratamientos en Diputados y en el Senado de la Nación.

Forma parte del trabajo de tesis de grado *"El péndulo del poder: disputas de propiedad, soberanía y desarrollo en la República Argentina"* (Ciencia Política y Relaciones Internacionales).

## Estado actual

- **Los 4 escenarios están validados** y pasan los chequeos internos con `críticos: 0` e `importantes: 0`. Datasets intactos desde 2026-05-14: 257 / 72 / 257 / 72.
- **Diputados** (Aerolíneas y YPF) validado/reconstruido desde las **actas nominales oficiales** de la Honorable Cámara de Diputados de la Nación. Los cuatro campos del registro (`name`, `block`, `province`, `vote`) provienen del acta.
- **Senado** (Aerolíneas y YPF) validado con **doble fuente**: voto desde el acta nominal del HSN; distrito/provincia reconstruido desde el archivo canónico `SEN AMPLIADOS XLSX` de la Secretaría Parlamentaria del HSN, porque las actas nominales del Senado no incluyen la columna provincia.
- **Estética editorial premium**: paleta sobre fondo cream con tinta profunda y acento dorado, vote colors institucionales (verde hunter para afirmativo, burgundy para negativo, ocre mostaza para abstención, piedra cálida para ausente), familias políticas con 10 tonos profundos desaturados.
- **Modo familia limpio**: las bancas codifican un único plano (regla metodológica: voto y partido nunca compiten en la misma marca). El cruce se consulta en tooltip, panel individual, filtros y tablas.
- **Tablas premium editoriales**: 4 tarjetas distintas (tabla nominal, resumen territorial, resumen por familia, ficha institucional) con tipografía afinada, mini-barras de composición, badges/chips sobrios y notas metodológicas al pie.
- **Exportaciones académicas**: 4 PNG con contexto académico (hemiciclo + mapa, modo voto + modo familia), 4 PNG de tablas editoriales, 5 CSV con columnas en español formal y trazabilidad por fila.
- **Backups preservados** en `backups/`, uno por cada reconstrucción importante y uno por cada etapa de pulido editorial, sin tocar.

## Archivo maestro

```
herramienta_votaciones/index.html
```

Es el único archivo que se ejecuta. Está pensado como una **sola página HTML autocontenida**: no carga fuentes ni librerías externas, no consume datos en red, y abre en cualquier navegador moderno sin instalar nada.

## Cómo abrirla

**Opción A — doble clic.** Abrir directamente `index.html` con el navegador. Las exportaciones a PNG funcionan, pero algunos navegadores aplican restricciones de `file://` que pueden afectar cómo se descargan las imágenes.

**Opción B — servidor local (recomendado).** Desde la raíz del proyecto:

```bash
cd /Users/vdc78/Desktop/Tesis
python3 -m http.server 8000
```

Y luego visitar `http://localhost:8000/herramienta_votaciones/`.

Trabajar bajo `http://` evita restricciones del esquema `file://` para exportar PNG y para algunos comportamientos de `canvas`.

## Qué contiene

Cuatro escenarios, accesibles por las tabs de la cabecera:

1. **Aerolíneas Argentinas · Cámara de Diputados** (03/12/2008, 257 bancas).
2. **Aerolíneas Argentinas · Senado de la Nación** (17/12/2008, 72 bancas).
3. **YPF · Cámara de Diputados** (03/05/2012, 257 bancas).
4. **YPF · Senado de la Nación** (26/04/2012, 72 bancas).

**Módulo estadístico (sub-tab Estadísticas)** — pieza fuerte de análisis legislativo comparado, con **síntesis interpretativa automática** al inicio de cada división (oraciones descriptivas derivadas de los indicadores, sin formular causalidad) y los siguientes indicadores politológicos:

- **Resultado agregado** — composición del cuerpo, votos emitidos, margen, apoyo sobre presentes y sobre cuerpo.
- **Participación y ausentismo** — tasa de presencia y de ausencia; provincias y familias con mayor ausentismo.
- **Cohesión por familia política** — peso del voto dominante sobre presentes; umbrales editoriales unificada ≥ 90% / dividida < 70%.
- **Cohesión por bloque político** — disciplina legislativa a nivel de bloque parlamentario.
- **Territorialización del voto** — apoyo provincial + polarización (provincias / familias / bloques divididos).
- **Cohesión dicotómica (Rice) y fragmentación (Rae)** — índices clásicos de ciencia política legislativa:
  - **Rice** = `|Afi − Neg| / (Afi + Neg)` por familia y bloque. Excluye ausentes, presidencia y abstenciones. Si `Afi + Neg = 0` → "No calculable".
  - **Rae** = `1 − Σ pᵢ²` sobre afirmativo, negativo y abstención. Excluye ausentes y presidencia. Cercano a 0 = voto concentrado; valores más altos = voto fragmentado.
- **Alineamiento oficialismo/oposición** — no se calcula con el dataset actual (requiere una variable analítica adicional).
- **Comparador entre escenarios** — tabla comparativa agrupada por bloques temáticos, diferencias entre casos/cámaras (Δ en puntos porcentuales **incluyendo Δ cohesión promedio por familia**), rankings, heatmaps familia × escenario (con **selector de métrica**: % Afirmativo, Rice, % Ausentismo) y provincia × escenario, Rae comparado entre los 4 escenarios.

Cada escenario incluye:

- **Ficha institucional compacta** (colapsable, con la línea resumen Ley · Cámara · Fecha · Resultado · tally siempre visible y las 3 bandas completas — header, trámite, fuentes — bajo el toggle "Ver ficha institucional ▾").
- Panel de **validación de datos** (verde "Datos validados", verde "Datos validados con doble fuente" para Senado, amarillo "Bajo revisión territorial" cuando falta).
- **Resumen ejecutivo** con cifras de A favor / En contra / Abstención / Ausentes / Margen sobre fondo cálido.
- **Hemiciclo** SVG con cada banca posicionada según orden político; panel central simplificado (cámara · fecha + RESULTADO grande).
- **Mapa provincial** SVG con las 24 jurisdicciones; Malvinas como referencia cartográfica.
- **Toggle de modo**: colorear por voto o por familia política (regla central: una banca, un plano — voto y partido nunca compiten en la misma marca).
- **Filtros**: buscador libre, filtro por provincia, filtro por voto, botón limpiar.
- **Sub-tab Tablas**: 4 láminas editoriales — tabla nominal, resumen territorial por provincia (con mini-barras de composición), resumen por familia política (chip + sub-línea con bloques), y ficha institucional (grid K/V en 5 secciones).
- **Sub-tab Exportar** organizada en 3 bloques:
  - **Figuras PNG**: hemiciclo y mapa con contexto académico (recomendado para tesis) + solo gráfico.
  - **Tablas como figura PNG**: 4 figuras editoriales (nominal, provincial, familia, ficha institucional).
  - **Tablas CSV**: 5 CSV (legisladores, provincias, familias, resumen institucional, comparativo general 4 escenarios).
- Panel lateral con detalle de legislador seleccionado y resumen provincial / nacional.

## Estado de validación

Al momento del cierre de esta versión, los cuatro escenarios pasan la validación interna con `críticos: 0` e `importantes: 0`:

| Escenario | Estado | Fuente del voto | Fuente del distrito |
|---|:-:|---|---|
| Aerolíneas · Diputados | ✓ validado contra PDF oficial | Acta nominal HCDN | Acta nominal HCDN |
| Aerolíneas · Senado | ✓ validado con doble fuente | Acta nominal HSN | SEN AMPLIADOS XLSX (Secretaría Parlamentaria HSN) |
| YPF · Diputados | ✓ reconstruido desde PDF oficial | Acta nominal HCDN | Acta nominal HCDN |
| YPF · Senado | ✓ validado con doble fuente | Acta nominal HSN | SEN AMPLIADOS XLSX (Secretaría Parlamentaria HSN) |

**Advertencia metodológica importante.** Para los dos escenarios de Senado, la columna *provincia* no aparece en el acta de votación nominal (que sólo lista nombre y voto). La provincia/distrito de cada senador se reconstruyó a partir de los archivos `SEN AMPLIADOS *.xlsx` provistos por la Secretaría Parlamentaria del Honorable Senado de la Nación. El banner verde "Datos validados con doble fuente" deja constancia explícita de esa doble procedencia. Ver `docs/metodologia_fuentes.md` para el detalle.

## Estructura del proyecto

```
herramienta_votaciones/
├── index.html                  ← archivo maestro
├── README.md                   ← este archivo
├── CHANGELOG.md                ← historial de cambios
├── docs/                       ← documentación metodológica y de uso
│   ├── metodologia_fuentes.md
│   ├── validacion_datos.md
│   ├── uso_herramienta.md
│   └── checklist_v1.md         ← checklist de entrega v1.0
├── fuentes/                    ← archivos originales (PDFs y XLSX por escenario)
│   ├── aerolineas_diputados/
│   ├── aerolineas_senado/
│   ├── ypf_diputados/
│   └── ypf_senado/
├── trazabilidad/               ← cruces nombre × distrito × fuente (Senado)
├── exports/                    ← destino sugerido para imágenes/CSV generados
│   ├── png/
│   └── csv/
├── backups/                    ← versiones del HTML previas a cada reconstrucción
│   ├── pre_senado/
│   ├── pre_ypf_diputados/
│   ├── pre_aerolineas_diputados/
│   └── versiones_anteriores/
└── auditorias/                 ← reportes por escenario y auditoría final
    ├── auditoria_final.md
    ├── reporte_aerolineas_diputados.md
    ├── reporte_ypf_diputados.md
    └── reporte_senado.md
```

## Cómo exportar

Dentro de la herramienta, en cualquier escenario, sub-tab **Exportar**:

- **Figuras PNG con contexto académico (recomendado).** Hemiciclo y mapa rasterizados a 2,5× sobre fondo cream `#fffdf8`, con eyebrow institucional, título serif, bajada, franja de metadatos (Fecha · Ley · Sesión · Tipo de votación · Bancas/Quórum · Resultado), leyenda cromática embebida, nota de lectura, fuentes documentales, nota cartográfica de Malvinas (mapa) y crédito al pie. Filename ej.: `aerolineas_diputados_hemiciclo_contexto_voto.png`.
- **Figuras PNG solo gráfico** (opción secundaria). El SVG crudo sin wrapper, para usos editoriales donde el contexto se compone aparte. Filename ej.: `aerolineas_diputados_hemiciclo_voto.png`.
- **Tablas como figura PNG.** Cada tabla se renderiza como figura editorial autosuficiente (eyebrow + título + bajada + meta-chips + tabla con badges/chips/mini-barras + fuentes + credit). Filenames: `<escenario>_tabla_legisladores.png`, `tabla_provincias.png`, `tabla_familias.png`, `resumen_institucional.png`. La tabla nominal exporta las filas visibles tras filtros y búsqueda; si no hay filtros y son > 100 filas, pide confirmación.
- **Tablas CSV.** Codificación UTF-8 con BOM (acentos respetados en Excel y Numbers). Cinco salidas: `<escenario>_tabla_legisladores.csv`, `tabla_provincias.csv`, `tabla_familias.csv`, `resumen_institucional.csv`, y `comparativo_votaciones_resumen.csv` (4 escenarios). Cada CSV incluye contexto institucional (Caso · Cámara · Fecha · Ley), valores de voto en español formal (Afirmativo / Negativo / Abstención / Ausente / Presidencia), porcentajes 1-decimal sobre el denominador apropiado, fuentes documentales y estado de validación.

Una vez generados, conviene mover los archivos descargados a `exports/png/` o `exports/csv/` para mantenerlos versionados con el proyecto.

## Fuentes

| Tipo | Archivo | Ubicación |
|---|---|---|
| Acta nominal Diputados (AAAA) | `VOTO DIPUTADOS AAAA.pdf` | `fuentes/aerolineas_diputados/` |
| Acta nominal Diputados (YPF) | `VOTO DIPUTADOS YPF.pdf` | `fuentes/ypf_diputados/` |
| Acta nominal Senado (AAAA) | `VOTO SENADORES AAAA.pdf` | `fuentes/aerolineas_senado/` |
| Acta nominal Senado (YPF) | `VOTO SENADORES YPF.pdf` | `fuentes/ypf_senado/` |
| Datos ampliados Senado (AAAA) | `SEN AMPLIADOS AAAA.xlsx` | `fuentes/aerolineas_senado/` |
| Datos ampliados Senado (YPF) | `SEN AMPLIADOS YPF.xlsx` | `fuentes/ypf_senado/` |
| Diarios de sesiones | `DIP/SEN *.pdf` | `fuentes/*/` |

## Respaldos

`backups/` contiene snapshots del HTML maestro inmediatamente anteriores a cada reconstrucción importante (Aerolíneas Diputados, YPF Diputados, Senado). Existen para poder rebobinar cualquier cambio si aparece un error que recién se descubre más tarde.

**Regla operativa: no editar ni borrar los backups salvo recuperación explícita.** Son archivos de sólo lectura desde la perspectiva del trabajo cotidiano. Si en algún momento hay que rebobinar uno, la operación correcta es **copiarlo** sobre `index.html` (no moverlo), para que el backup quede preservado en su lugar.

## Cómo seguir trabajando

- Cualquier modificación de fondo del HTML debería pasar por `node --check` de su script extraído y por `python3` con el auditor de datasets antes de declararla terminada.
- Cualquier cambio de fuentes debe quedar registrado en `CHANGELOG.md` y, si toca datasets, generar un archivo nuevo en `trazabilidad/`.
- Si se actualiza alguna composición legislativa, la convención es: extraer la fuente primaria, generar el dataset reconstruido, cruzar contra el dataset actual, presentar un reporte con confianza alta/media/baja, y aplicar sólo tras autorización explícita.
