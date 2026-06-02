# Reporte — YPF · Diputados

## Estado final
**Reconstruido desde PDF oficial.** Validación: críticos 0, importantes 0, **OK**.

## Acta oficial
- Honorable Cámara de Diputados de la Nación.
- Expediente **29-S-12**, Orden del Día **288**.
- 130° Período Ordinario, 5ª Sesión Especial, 7ª Reunión.
- **Fecha:** 03/05/2012.
- **Hora:** 21:29.
- **Presidente:** DOMÍNGUEZ, Julián Andrés.
- **Resultado:** AFIRMATIVO.
- **Fuente del archivo:** `fuentes/ypf_diputados/VOTO DIPUTADOS YPF.pdf` (11 páginas, incluye `Apellido y Nombre · Bloque Político · Provincia · Voto` por cada legislador).

## Conteos finales

| Categoría | Cantidad |
|---|---:|
| AFIRMATIVO | 208 |
| NEGATIVO | 32 |
| ABSTENCION | 5 |
| AUSENTE | 11 |
| PRESIDENCIA | 1 (Domínguez) |
| **Total bancas** | **257** |

Coincide exactamente con la metadata oficial del escenario.

## Por qué fue necesario reconstruir el dataset

El dataset previo era una **mezcla de composición 2008 con votos 2012**: tenía nombres de legisladores que pertenecían a la composición de la Cámara durante el tratamiento de Aerolíneas Argentinas (2008), pero con los votos del acta YPF (2012). Esto se detectó por las distribuciones territoriales descalibradas (Córdoba con 11 de 18 bancas, Santiago del Estero con 14 de 7, etc.) y se confirmó al cruzar nombre por nombre.

## Cambios aplicados

### 2 reemplazos de persona (composición 2008 → 2012)

| Sale del HTML | Entra del PDF | Voto |
|---|---|---|
| BALDATA, Griselda Angela (Córdoba) — mandato terminó 10/12/2011 | FAUSTINELLI, Hipólito (Córdoba, UCR) | AUSENTE |
| PÉREZ, Adrián (Buenos Aires) — no era diputado en 5/2012 | PEREZ, Alberto José (San Luis, Frente Peronista) | NEGATIVO |

### 67 correcciones de provincia

Algunos ejemplos representativos (la lista completa está en el cruce `/tmp/ypf_dip_cross.py` y el log de aplicación):

- ALBRIEU, Oscar: Buenos Aires → Río Negro
- BIANCHI, María del Carmen: Tucumán → CABA
- CARRANZA, Carlos Alberto: Córdoba → Santa Fe
- GAMBARO, Natalia: CABA → Buenos Aires
- IANNI, Ana María: Buenos Aires → Santa Cruz
- MAJDALANI, Silvia Cristina: CABA → Buenos Aires
- PANSA, Sergio Horacio: Córdoba → San Luis
- RIVAROLA, Rubén Armando: San Luis → Jujuy
- VEAUTE, Mariana Alejandra: Santiago del Estero → Catamarca
- ZIEBART, Cristina Isabel: Misiones → Chubut

Tras la corrección, las 24 jurisdicciones cierran al cupo legal:

```
Buenos Aires 70 ✓  CABA 25 ✓  Santa Fe 19 ✓  Córdoba 18 ✓
Mendoza 10 ✓  Entre Ríos 9 ✓  Tucumán 9 ✓
Chaco 7 ✓  Corrientes 7 ✓  Misiones 7 ✓  Salta 7 ✓  Santiago del Estero 7 ✓
Jujuy 6 ✓  San Juan 6 ✓
Catamarca 5 ✓  Chubut 5 ✓  Formosa 5 ✓  La Pampa 5 ✓  La Rioja 5 ✓
Neuquén 5 ✓  Río Negro 5 ✓  San Luis 5 ✓  Santa Cruz 5 ✓  Tierra del Fuego 5 ✓
```

### 54 cambios de etiqueta de bloque

El bloque pasa a su forma 2012 según el acta oficial. Ejemplos:

- "Peronismo Federal" → "Frente Peronista" (Camaño, Brown, De Narváez, Amadeo, Atanasof, etc.).
- "Propuesta Republicana" → "PRO" (Pinedo, Bertol, Tonelli, Schmidt-Liermann, etc.).
- "Coalición Cívica - ARI - GEN - UPT" → "Coalición Cívica - ARI" (Carrió, de Prat Gay, Re, Terada).
- "Frente Justicia Unión y Libertad" → "Frente Peronista" (Bianchi Ivana).
- "Unión Popular" → "Unidad Popular" (De Gennaro).

### 0 cambios de voto

Los conteos legislativos no cambiaron en ningún registro. Lo único que cambió fue la atribución persona / provincia / bloque, recuperando la composición correcta de la Cámara en mayo de 2012.

## Mapeo bloque → familia política

Criterio metodológico: **identidad partidaria**, sin mezclar con alineamiento legislativo coyuntural. Nuevo Encuentro queda en Centroizquierda; Frente Cívico por Santiago queda en Provinciales/otros, aunque hayan acompañado al oficialismo en este voto.

| Familia | Cantidad |
|---|---:|
| FPV-PJ | 119 |
| UCR | 38 |
| Provinciales / otros | 27 |
| Peronismo disidente | 27 |
| Centroizquierda | 17 |
| Socialista | 11 |
| PRO | 11 |
| Coalición Cívica | 7 |
| **Total** | **257** |

## Backup previo
`backups/pre_ypf_diputados/votaciones_consolidado.bak_pre_ypfdip.html` (preserva el HTML con el dataset incorrecto, por si fuera necesario rebobinar).
