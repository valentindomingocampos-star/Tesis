# Reporte — Aerolíneas · Diputados

## Estado final
**Validado contra PDF oficial.** Validación: críticos 0, importantes 0, **OK**.

El dataset coincide persona por persona con el acta oficial. No hizo falta reconstruir nada sustantivo; sólo se aplicó una normalización cosmética del nombre de un bloque.

## Acta oficial
- Honorable Cámara de Diputados de la Nación.
- Expediente **6538-D-08**, Orden del Día **1342**.
- 126° Período Legislativo, 1ª Sesión Especial de Prórroga, 34ª Reunión.
- **Fecha:** 03/12/2008.
- **Hora:** 19:41.
- **Presidente:** FELLNER, Eduardo Alfredo.
- **Resultado:** AFIRMATIVO.
- **Fuente del archivo:** `fuentes/aerolineas_diputados/VOTO DIPUTADOS AAAA.pdf` (9 páginas, incluye `Apellido y Nombre · Bloque Político · Provincia · Voto` por cada legislador).

## Conteos finales

| Categoría | Cantidad |
|---|---:|
| AFIRMATIVO | 153 |
| NEGATIVO | 84 |
| ABSTENCION | 0 |
| AUSENTE | 19 |
| PRESIDENCIA | 1 (Fellner) |
| **Total bancas** | **257** |

Coincide exactamente con la metadata oficial del escenario.

## Cruce contra el PDF

| Métrica | Resultado |
|---|:-:|
| Registros HTML | 257 |
| Registros PDF | 257 |
| Match exacto por (apellido + primer nombre) | **257 / 257** ✓ |
| Cambios de voto | 0 |
| Cambios de provincia | 0 |
| Sólo en HTML, no en PDF | 0 |
| Sólo en PDF, no en HTML | 0 |
| 24 jurisdicciones cuadran al cupo legal | ✓ |

## Casos especiales documentados

### KATZ, Daniel — AFIRMATIVO (versión final del acta)
La versión original del acta marcaba ABSTENCIÓN. El sistema parlamentario registró una modificación formal el 03/12/2008 a las 19:50 que la cambió a AFIRMATIVO. El dataset conserva la versión final (AFIRMATIVO), que es la versión institucional vigente.

### DE MARCHI, Omar Bruno — AUSENTE (versión institucional)
El acta deja constancia, como observación, de que De Marchi expresó "su voto por la NEGATIVA a viva voz". Sin embargo, **el sistema no le computa voto**: queda registrado como AUSENTE. El dataset preserva la categoría institucional (AUSENTE). La observación se documenta acá pero no en el campo `vote`.

### FELLNER, Eduardo Alfredo — Presidencia
Fellner presidió la sesión. No aparece en el listado de votantes del acta. Se lo incorpora al dataset como una banca con `vote: 'PRESIDENCIA'` y `block: 'Presidencia'` (categoría institucional, distinta de su bloque legislativo ordinario FPV-PJ Jujuy) para que el hemiciclo y los conteos cierren a 257.

## Única normalización aplicada

El dataset previo guardaba el bloque `Solidaridad e Igualdad (SI) - ARI (T.D.F` (truncado, sin el `.)` final). El PDF lo lista completo como `Solidaridad e Igualdad (SI) - ARI (T.D.F.)`. Se normalizó en **9 legisladores**:

- MACALUSE, Eduardo Gabriel
- NAIM, Lidia Lucía
- RAIMUNDI, Carlos Alberto
- BISUTTI, Delia Beatriz
- GARCÍA MÉNDEZ, Emilio Arturo
- GONZÁLEZ, María América
- BENAS, Verónica Claudia
- BELOUS, Nélida
- GORBACZ, Leonardo Ariel

Corrección cosmética; no afecta votos, provincia ni familia política.

## Backup previo
`backups/pre_aerolineas_diputados/votaciones_consolidado.bak_pre_aaaadip.html` (preserva el HTML antes de la normalización del bloque SI-ARI).
