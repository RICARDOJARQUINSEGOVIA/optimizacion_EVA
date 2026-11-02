# Prompt de arranque — Modelo LP → MILP, implementación en Pyomo y LaTeX
**2 de noviembre de 2026 · v1.2**

> **v1.1:** se añade §1.8 (cadena de unidades y volumen de carga), se reescribe **M2** con el
> factor de carga $\nu_p$, se añaden las decisiones **D10–D13** y se endurece el criterio de
> aceptación de F2 (por receta, no sólo por insumo).
> **Cuatro de esas decisiones bloquean la implementación: D11, D12, D13 y D4.**
>
> **v1.2:** se añade **§6.4 — ficha de entregable** (estructura obligatoria de cinco campos,
> con la regla de que todo entregable se reduce a un número o un signo verificable en ≤10 min)
> y **§11 — expediente del proyecto** (diez documentos con objetivo y razón, más la bitácora de
> decisiones y el registro de validación, que hay que crear).

> Pega este documento completo como primer mensaje de la sesión nueva. Está escrito para que
> quien lo lea pueda trabajar sin acceso a la conversación anterior. Todo dato numérico de
> aquí está medido sobre los archivos del proyecto; lo que es supuesto está marcado como tal.

---

## 0. Qué estamos haciendo y en qué entorno

**Objeto del doctorado.** Un modelo de optimización matemática para planificación maestra de
cadenas de suministro multi-escalón que maximiza EVA bajo incertidumbre de demanda y de
tiempos de aprovisionamiento, integrado como motor prescriptivo en un gemelo digital. El
doctorado es sobre **el método**, no sobre la empresa.

**Papel de la empresa.** Caso de validación y fuente de dos cosas: la **estructura** real
(productos, insumos, proveedores, clientes, proceso) y la **calibración empírica de la
incertidumbre**. No es el objeto de estudio.

**Entorno de trabajo.** VS Code · Python con **Pyomo** · formulación en **LaTeX**.
La formulación LaTeX y el modelo Pyomo deben mantenerse sincronizados (ver §6).

**Alcance de esta sesión.** Cinco etapas en orden estricto; cada una tiene criterio de
salida y no se avanza sin cumplirlo:

| Etapa | Qué produce | Criterio de salida |
|---|---|---|
| **1** | Estructura homologada + generador de instancias | Tabla de decisión real/sintético cerrada y firmada |
| **2** | LP ajustado a esa estructura, en LaTeX y en Pyomo | El modelo resuelve y las dos versiones coinciden |
| **3** | LP validado según el protocolo del proyecto | Las 8 pruebas con evidencia tabulada |
| **4** | MILP con las binarias justificadas | Cota LP ≥ MILP; identidad contable intacta |
| **5** | MILP validado | Pruebas re-ejecutadas + gap reportado |

**Cada entregable de cada etapa llega con la ficha de cinco campos de §6.4, sin excepción.**
Un entregable por sesión de trabajo, no por día: la frecuencia no garantiza comprensión, la
estructura sí. El campo 3 de la ficha —*cómo lo verificas tú en diez minutos*— es el que hace
que el avance sea tuyo y no mío.

---

## 1. Hechos medidos — no renegociar sin datos nuevos

Todo lo de esta sección sale de `HOJA_DATOS.xlsx` (hojas `HISTÓRICO`, `BOM_SEMI`, `BOM_PT`,
`TIEMPO_DEST`, `CAT_CLIENT`, `Insumos_PROV_SEMI`, `Histórico_compras`,
`Inventarios_iniciales`, `Inventario_Dic_25`).

### 1.1 Estructura

| Elemento | Valor | Fuente |
|---|---|---|
| Semiterminados (granel) | **34** | `BOM_SEMI` |
| SKU de producto terminado | **68** = 34 × 2 presentaciones (1 L, 5 L) | `BOM_PT` |
| Insumos | **83** (incluye `ENV_1`, `ENV_5`, `GNT001`) | `Insumos_PROV_SEMI` |
| Relaciones BOM nivel 1 | **165** pares (semi, insumo) | `BOM_SEMI`, 183 filas |
| Componentes por receta | 3 a 22 | — |
| Insumos universales | `AET002` (alcohol) y `GNT001` (garrafa 20 L) están en **las 34 recetas** | — |
| Proveedores | 32 con compras reales; 21 en catálogo | `Histórico_compras` |
| Clientes | 838, cobertura 100 % del histórico | `CAT_CLIENT` |
| Plantas | 2, a ~100 km | confirmado por la empresa |

**Catálogo real completo: 148 extractos.** Los 34 modelados son ~60 % de las ventas. Los
otros 114 entran como capacidad reservada y constante en NOA (ver §3).

### 1.2 Proceso

```
molienda (≈2 h, independiente del lote)
   → percolación (7 días)
      → destilación (kx_p · litros)   ← CUELLO DE BOTELLA
         → cuarentena / retención de calidad (7 días)
```

| Parámetro | Valor | Fuente |
|---|---|---|
| **δ (retención total)** | **2 semanas** (percolación 7 d + cuarentena 7 d) | nota de `TIEMPO_DEST` |
| `kx_p` | 0.0515 – 0.1437 h/L; **ponderado por demanda 0.0730** | `TIEMPO_DEST`, 8–36 obs/receta |
| σ de `kx_p` | CV 0.18 – 0.49 por receta | `TIEMPO_DEST` col. `DESV_EST_HORA_DEST` |
| `LOTE_p` | 48 – 177 L, mediana 74 | `TIEMPO_DEST` |
| Horas por lote | 3.9 – 18.8 h (promedio real 6.38 h + 1 h cambio) | derivado |
| Cobertura de un lote | 2.7 – 23.7 semanas de demanda, **mediana 8.1** | derivado |
| Destiladores | **1**, hasta 3 turnos (120 h/sem), **compartido con otros usos** | empresa |
| Percoladores | **24**, ciclo 7 d ⇒ **24 lotes/semana** | empresa |
| Cambio de producto | **1 h plana**, independiente del par | empresa |

**`LOTE_p` no es la carga física del percolador.** Correlación 0.72 con √demanda, 26 valores
distintos: es un lote económico precalculado tipo EOQ. El volumen físico del percolador
(`V_perc`) **no se tiene** y es decisión pendiente (§3).

### 1.8 Cadena de unidades y volumen de carga — CRÍTICO, no resuelto

**Lo que sí está resuelto.** El BOM está expresado **por litro de granel de salida**, el que
se envasa después de destilar. La prueba limpia es la garrafa: `GNT001` vale **0.05 en las 34
recetas** sin excepción = 1 garrafa / 20 L, lo que sólo tiene sentido con denominador en
litros de producto terminado. `kx_p` también es por litro de salida (`TIEMPO_DEST`: *"es
tiempo que se requiere para hacer 1 L de extracto"*). ⇒ **El eje de unidades es homogéneo:
litros de salida.** Los insumos van en su propia unidad (kg de planta, L de alcohol); sólo el
denominador debe ser homogéneo, y lo es. Esto confirma $ki_\pi = ky_\pi = 1$ litro.

| Coeficiente por litro de granel de salida | Mediana | Rango |
|---|---|---|
| Alcohol `AET002` | **0.897 L** | 0.0001 – 1.3835 |
| Planta molida (suma de botánicos) | **0.362 kg** | 0.100 – 1.480 |
| Garrafa `GNT001` | 0.05 | 0.05 en las 34 |

**Lo que NO está resuelto.** El proceso real es:

```
percolación:  entra alcohol (L) + planta molida (kg)  →  sale mezcla (L)
destilación:  entra la mezcla  →  se retira alcohol  →  sale extracto concentrado (L)
```

Es decir: **el percolador está ocupado por el volumen de CARGA, no por el de salida**, y la
destilación concentra, así que carga > salida. El BOM da consumo **neto por litro de salida**,
que es correcto para costo e inventario, pero **no sirve para dimensionar la ocupación del
percolador**. Hace falta

$$\nu_p = \frac{\text{litros de carga al percolador}}{\text{litros de extracto de salida}}$$

que con los coeficientes actuales andaría entre ~1.1 y ~1.8 — **varía por receta en casi 2×**.
Esa dispersión reordena el plan cuando la percolación aprieta: dos recetas con el mismo
`LOTE` pueden ocupar volúmenes muy distintos. Ver **M2** reescrita en §4 y **D11** en §3.

**Y una pregunta abierta: ¿se recupera el alcohol?**

| Alcohol `AET002`, 12 meses | |
|---|---|
| Consumo **neto** que implica el BOM | 20 310 L |
| **Compras reales** | **88 620 L** |
| **Razón** | **4.36×** |

Parte se explica porque el alcohol alimenta también a los 114 productos no modelados (es el
insumo compartido por excelencia), pero el factor es grande. Contraste: la garrafa da 2.79, y
ésa sí se explica casi toda por la cola. Tres lecturas, con consecuencias distintas para el
modelo — ver **D12**.

**Tres recetas con alcohol ≈ 0 en un producto llamado "extracto fluido"** (que por definición
es alcohólico). Parece error de captura:

| Receta | Alcohol (L/L) | $e_p$ actual | Con alcohol mediano | Costo no registrado |
|---|---|---|---|---|
| `EFJ001` jengibre | **0.0001** | $34.78 | $54.75 (+57 %) | **$12 220/año** |
| `EFM077` moringa | 0.0010 | $56.96 | $76.91 (+35 %) | $6 244/año |
| `EFA010` árnica | 0.0010 | $20.45 | $40.40 (+98 %) | $4 578/año |

Son ~$23 000/año de material no registrado. **Hay que resolverlo antes de calibrar**, porque
el modelo va a preferir producir esas tres por su costo artificialmente bajo. Ver **D13**.

### 1.3 Demanda

| | Valor |
|---|---|
| Horizonte disponible | 199 semanas (2023-01-02 a 2026-10-26), 14 872 líneas |
| Media | **442 L/semana**; p50 343, p90 895, máx 1 633 |
| CV agregado semanal | **0.76** |
| CV por semiterminado | 1.21 (p10) – 2.83 (p90), mediana 1.74. **Los 34 con CV > 1** |
| CV de las participaciones (mix) | **1.67** — el mix es más volátil que el nivel |
| Productos distintos por semana | p50 **25**, p90 29, máx 34 |
| Desviación respecto de la media | >5 % en **96 %** de las semanas; >25 % en 77 % |
| Unidad de `CANT` | **litros**, no piezas (2 087 de 2 092 líneas de 5 L son múltiplos de 5) |
| `TOT_PARTIDA` | = `CANT` × `PREC` en las 14 872 líneas, sin excepción |
| Líneas con `CANT` ≤ 0 | 44 (devoluciones; netear o excluir y declararlo) |

### 1.4 Clientes y cobranza

| Plazo | Clientes | % facturación (52 sem) | → semanas |
|---|---|---|---|
| 0 d | 647 | 23.3 % | 0 |
| 8 d | 94 | 17.5 % | 1 |
| 15 d | 22 | 38.4 % | 2 |
| 20 d | 7 | 0.8 % | → colapsar en 2 |
| 30 d | 65 | 20.0 % | 4 |
| 60 d | 3 | 0.0 % | → colapsar en 4 |

**$L^M$ = 4 clases de crédito con τ = 0, 1, 2, 4 semanas.** No hay zonas geográficas: 352
prefijos distintos para 395 clientes activos, son iniciales de razón social.
Plazo medio ponderado por ingreso: **13.3 d = 1.91 semanas**.
El mix de producto es casi idéntico entre clases (33–34 semis en cada una) ⇒ se puede
desagregar $d_{qzt}$ proporcionalmente sin sesgo de mezcla.
Concentración: top 5 clientes = 58.4 %, top 20 = 74.0 %, top 50 = 84.9 %.

### 1.5 Aprovisionamiento

De `Histórico_compras`: **2 126 partidas con las tres fechas** (orden, esperada, recepción),
2022-01-03 a 2026-10-21.

| Plazo (días) | Mediana | Media | p90 | σ |
|---|---|---|---|---|
| Prometido (esperada − orden) | 4 | 12.4 | 16 | — |
| **Real (recepción − orden)** | **8** | 17.5 | 22 | **σ robusta 10.9 d = 1.56 sem** |
| Retraso (recepción − esperada) | 4 | 4.5 | 9 | 15.8 d (máx 372) |

| | |
|---|---|
| Entregas en fecha o antes | **17.6 %** |
| Entregas tarde | **82.4 %** |
| Cola: > 7 d tarde | 4.55 % |
| Cola: > 14 d tarde | 0.53 % |
| Órdenes con ≥ 60 d de anticipación | **3.6 %** |
| Órdenes con anticipación ≤ 0 (retroactivas/urgentes) | **18.5 %** |
| Frecuencia de compra | mediana 62 d entre pedidos; `AET002` cada 6 d |
| Precio pagado vs catálogo | **97 % dentro de ±10 %**, razón mediana 1.00 |

### 1.6 Inventario al 1-dic-2025

| Categoría | Valor | % |
|---|---|---|
| Insumos | **$3 794 494** | 72.2 % |
| Granel / semiterminado | $1 100 226 | 20.9 % |
| Producto terminado | $315 038 | 6.0 % |
| Envase | $47 795 | 0.9 % |
| **Total** | **$5 257 553** | |

- Granel físico: **2 195 L** (36 renglones, `Inventarios_iniciales`)
- Insumos en BOM de los 34 activos: $1 439 756
- **Insumos ni en BOM ni comprados desde 2022: $2 402 533 (63 % del inventario de insumos)**
  → obsolescencia, no política de inventarios
- Inventario justificable teóricamente (seguridad 95 % + ciclo): **$534 881** ⇒ exceso 7.1×

### 1.7 Economía de referencia (34 productos, 52 semanas)

| | Valor |
|---|---|
| Facturación | $8 672 016 |
| Material (fuente corta) | $2 279 019 |
| Horas de destilación | 1 678 h/año = 32.3 h/sem |
| Lotes | 263/año = 5.1/sem |
| Margen de contribución (precio − material) | mediana **$296/L** |
| Margen en riesgo por lote bloqueado | promedio **$25 643** |

---

## 2. Correcciones — errores ya cometidos, no repetir

Durante el análisis se sostuvieron y después se refutaron las siguientes afirmaciones. Se
listan para que no se reabran.

| Afirmación refutada | Qué dice la evidencia |
|---|---|
| «Hay doble fuente con disyuntiva 7 vs 90 días» | El plazo **real** es 8 d mediano en **ambas** clases del catálogo (n = 818 y 486). `TIEMPO_ENT = 90` no describe la entrega |
| «Hay que relajar (S2); el rezago es de 13 semanas» | **(S2) es válido**: plazo real ≈ 1.14 semanas = un período. No hay rezago largo, ni efecto de fin de horizonte por ese canal, ni stock de seguridad por plazo largo |
| «38 % de las relaciones tienen τ_lead > τ_pay, invirtiendo el signo del crédito comercial» | **Falso.** El crédito corre **desde la recepción**: orden → llegada → pago. Nunca se paga antes de recibir |
| «El inventario en tránsito cuesta capital» | **No es propiedad de la empresa.** El título pasa al llegar al almacén de la instalación B; se compra a terceros nacionales |
| «Existe la partición nacional / importado» | **No existe.** Todo se compra a distribuidores nacionales. Se eliminan $R^{(I)}$, el arco especial $B_G\to B_P$ y la clase de pago por nacionalidad |
| «La capacidad está al 47 % y es holgada» | Artefacto: se comparó la carga de 34 productos contra la capacidad de 2 turnos. El destilador es **compartido** y con 3 turnos puede estar saturado (ver §3, decisión D6) |
| «`COSTOU` de `BOM_SEMI` sirve como costo de insumo» | **Descartar.** 25 insumos con valor distinto según receta; 25 fuera del rango de sus proveedores. Usar sólo el precio por proveedor |

**Lección metodológica a conservar** (es resultado publicable): *los parámetros maestros del
ERP no son datos, son declaraciones.* La calibración debe hacerse contra el registro
transaccional. Esto pertenece a la pregunta específica 3 del protocolo (arquitectura del
gemelo).

---

## 3. ETAPA 1 — Homologar estructura y cerrar la frontera real / sintético

**Primera tarea de la sesión: llenar y firmar esta tabla.** Nada se implementa antes.

Principio acordado: **estructura y calibración de incertidumbre reales; niveles de régimen
sintéticos y barridos.** Es un generador de instancias calibrado sobre un caso real.

| Nivel | Contenido | Decisión |
|---|---|---|
| Estructura | 34 semis, 68 SKU, 83 insumos, coeficientes BOM, 4 clases de crédito, 2 plantas, 3 etapas de proceso | **REAL** |
| Calibración de incertidumbre | σ_L = 1.56 sem, distribución de retraso, CV demanda 0.76, σ de `kx_p` | **REAL** |
| Niveles de régimen | capacidad disponible, dispersión de plazos de cobro, razón $i^{wacc}/h$, saldos financieros | **SINTÉTICOS y barridos** |

### Decisiones que hay que cerrar en esta etapa

| # | Decisión | Opciones | Nota |
|---|---|---|---|
| **D1** | Presentación de 20 L | (a) no existe — `HISTÓRICO` sólo tiene 1 L y 5 L; (b) sí se vende y faltaba en el extracto | `GNT001` es envase **interno** del granel, no presentación de venta. Si se adopta (a), $\|Q\| = 68$ |
| **D2** | BOM nivel 1 | (a) completo, 165 pares; (b) colapsado a un insumo agregado por granel | (b) elimina el acoplamiento por insumos compartidos (`AET002` y `GNT001` en las 34 recetas), que es el mecanismo interesante. **Recomendación: (a)**, o mínimo conservar los dos universales explícitos |
| **D3** | Inventario obsoleto ($2.4 M) | (a) excluir de la instancia, reportar como hallazgo; (b) modelar decisión de liquidación con valor de recuperación y efecto fiscal | (b) es más ambicioso y encaja con el bloque de ISR; (a) es lo mínimo. **La conciliación con el balance (elemento 4.3) falla si no se declara** |
| **D4** | `V_perc` (volumen físico del percolador) | dato faltante | Tope duro del lote. `LOTE` máx observado = 177 L ⇒ `V_perc` ≥ 177 |
| **D5** | Tratamiento de `LOTE_p` | (a) congelado como dato en el LP (sólo entra en el piso de ciclo y en lotes/semana); (b) endógeno | **(a) en el LP, (b) en el MILP.** Recalcular el EOQ fuera del modelo es optimizar dos veces |
| **D6** | Carga del destilador por otros usos | dato faltante; estimado 80–85 h/sem si está saturado | Define si la capacidad es vinculante. Con 3 turnos (120 h/sem) y 82 h de otros usos ⇒ capX efectiva 38 h vs 37.3 h requeridas = **98 %** |
| **D7** | Cola de los 114 productos | capacidad reservada ≈ **26 h/sem** (rango 21–31) + constante en NOA ≈ **$270 000** | Es constante en el objetivo ⇒ no cambia el argmax, sí el nivel reportado |
| **D8** | Líneas con `CANT` ≤ 0 (44) | netear o excluir | Declarar cuál |
| **D9** | Redondeo de plazos | 8 d → 1 sem; 15 d crédito → 2 sem; 30 d → 4 sem | Declarar: desplaza $AP_t$ y $AR_t$ sistemáticamente |
| **D10** | **Longitud del período** | (a) semanal, $T=52$; (b) mensual, $T=12$; (c) híbrido 13 sem + 9 meses | **Recomendación firme: (a).** Ver justificación abajo |
| **D11** 🔴 | **Factor de carga $\nu_p$** | dato faltante | Litros de carga al percolador por litro de extracto. **Bloquea M2.** Puede venir de la hoja de proceso, del factor de concentración de la destilación, o derivarse del volumen de alcohol cargado + densidad aparente de la planta molida (~0.3–0.5 kg/L si no hay dato) |
| **D12** 🔴 | **Reciclo de alcohol** | (a) el coeficiente es neto y la destilación **recupera** alcohol que se reutiliza; (b) es neto y el exceso es **merma** por evaporación; (c) todo el exceso es de la cola | **(a)** ⇒ falta un **arco de retorno** de alcohol recuperado al almacén: el costo está bien pero el flujo físico y el inventario no, y un gemelo vería desviarse el inventario de alcohol. **(b)** ⇒ $a^1$ está **subestimado**, hay que corregirlo. **(c)** ⇒ sólo declararlo. Es pregunta a producción, no cálculo |
| **D13** 🔴 | **Tres recetas con alcohol ≈ 0** | (a) error de captura → corregir; (b) son extractos acuosos y el nombre miente | `EFJ001`, `EFM077`, `EFA010`. ~$23 000/año de material no registrado. Sin resolver, el modelo prefiere producirlas por su costo artificialmente bajo |

🔴 = bloquea la implementación.

### Justificación de D10 — por qué el período es semanal

Un período discreto no puede representar una dinámica más corta que él mismo. Con la escala
temporal de esta instancia, **nueve de doce mecanismos quedan por debajo del período mensual**:

| Mecanismo | Días | Semanas | **Meses** | ¿Representable mensualmente? |
|---|---|---|---|---|
| Plazo de entrega real (mediana) | 8 | 1.14 | 0.26 | **No — subperíodo** |
| Percolación | 7 | 1.00 | 0.23 | **No** |
| Cuarentena | 7 | 1.00 | 0.23 | **No** |
| **δ total** | 14 | 2.00 | 0.46 | **No** |
| Retraso de proveedor p90 | 9 | 1.29 | 0.30 | **No** |
| Ciclo de percolador | 7 | 1.00 | 0.23 | **No** |
| Crédito cliente, clase 8 d | 8 | 1.14 | 0.26 | **No** |
| Crédito cliente, clase 15 d | 15 | 2.14 | 0.49 | **No** |
| Crédito cliente, clase 30 d | 30 | 4.29 | 0.99 | límite |
| Crédito de proveedor | 15 | 2.14 | 0.49 | **No** |
| Cobertura de un lote | 57 | 8.14 | 1.88 | Sí |
| Intervalo entre pedidos | 62 | 8.86 | 2.04 | Sí |

Con período mensual se perderían, concretamente: **la incertidumbre de aprovisionamiento**
(media hipótesis de la tesis), **el término de WIP** (la extensión declarada sobre Hahn y Kuhn
se vuelve inmedible, porque lo que entra y sale en el mismo período no acumula), **el canal de
cuentas por cobrar** (tres de cuatro clases indistinguibles de cero — y es el canal principal
por el que el EVA se separa del min-costo), y la volatilidad misma (**CV de demanda 0.76 →
~0.36** por agregación: se reduciría por decisión de modelado el fenómeno que se estudia).

Lo que se ganaría no compensa: 58 814 → 13 572 columnas, pero **el LP semanal ya es pequeño**
(338 k no-ceros, segundos con barrera) y el MILP se resuelve restringiendo la integralidad a
la ventana implementable, no engrosando el período.

**El híbrido (c) se descarta** por una razón específica: con períodos de longitud desigual,
$i^{wacc}$ hay que ajustarlo por longitud y los operadores de rezago $(H1)/(H2)$ dejan de ser
desplazamientos enteros uniformes. Eso introduce errores justo en la identidad (A1), que es la
prueba de verificación más fuerte del proyecto y se audita a $10^{-6}$.

**La preocupación legítima detrás de la pregunta** —que el MPS de la empresa sea mensual— es un
problema de **ventana de compromiso**, no de longitud de período, y se resuelve como lo
resuelve el MPS real: períodos semanales, compromiso mensual.

$$\underbrace{t=1..4}_{\text{congelado, se ejecuta}} \;\Big|\; \underbrace{t=5..13}_{\text{firme, revisable}} \;\Big|\; \underbrace{t=14..52}_{\text{libre, sólo balance}}$$

El plan se **reporta** agregado al mes, que es lo que la empresa consume, y el modelo conserva
resolución donde los mecanismos operan. Encaja además con la integralidad restringida del MILP:
la ventana congelada es la que lleva $n_{pt}$ entera.

**Y esto es resultado publicable, no sólo decisión.** Correr el modelo con período semanal y
mensual sobre la misma instancia y medir cuánto del EVA diferencial desaparece da una
afirmación verificable: *la literatura que modela planeación táctica con períodos mensuales o
más gruesos no puede detectar los efectos de capital de trabajo que afirma optimizar.* Es
contrastable contra el propio corpus — 65 artículos ya codificados — contando cuántos usan
períodos mensuales o mayores. Aporta a la Específica 1 (condición de representabilidad) y a la
Específica 3 (la cadencia de reoptimización del gemelo no puede ser más fina que el período).

### Convenciones que ya están decididas

| Parámetro | Valor | Razón |
|---|---|---|
| $\eta^{ef}$ | **0.95**, no 0.85 | `HORA_UNIDAD` ya es tiempo observado; 0.85 contaría la ineficiencia dos veces |
| $ki_\pi$, $ky_\pi$ | 1.0 = 1 litro | Todo el negocio opera en litros; ahorra 366 parámetros |
| $\tau^{isr}$ | 0.30 | Confirmado |
| $a^2_{qp}$ | {1.0, 5.0} + 1 pza de envase | `BOM_PT` |
| Fuente de $e_r$ | sólo `Insumos_PROV_SEMI`; descartar `COSTOU` | §2 |
| $\delta$ | 2 semanas | §1.2 |

### Barrido experimental mínimo (para la etapa 3 y para la tesis)

| Factor | Niveles | Justificación |
|---|---|---|
| Utilización del cuello de botella | 60, 75, 90, 98 % | el destilador es compartido; 3 turnos dejan 30–60 h disponibles |
| Dispersión de plazos de cobro | real (0/1/2/4 sem), comprimida, ampliada | la real vale 1.15 % del precio entre contado y 30 d |
| Razón $i^{wacc}/h$ | < 1, ≈ 1, > 1 | **la brecha EVA vs min-costo puede tener signo negativo**; hay que verificarlo, no suponerlo |
| σ_L | 0 (determinista), 1.56 sem (real), 3 sem | calibrado en §1.5 |

**Reportar siempre el punto ancla** (configuración real) además de la rejilla. Publicar sólo
el régimen favorable es seleccionar el resultado.

---

## 4. ETAPA 2 — Ajustar el LP

Siete modificaciones sobre la formulación vigente. La formulación actual todavía describe
5 semis / 10 SKU / 11 insumos / 3 zonas de 15-30-45 d / δ = 1 — **nada de eso corresponde a
la instancia**.

| # | Cambio | De | A | Arrastra |
|---|---|---|---|---|
| **M1** | $capX$ | $168\,n^{lin}\eta^{ef}$ | $h^{turno}n^{turno}d^{sem}n^{lin}\eta^{ef} - \bar h^{cola}$ | La fórmula de 168 h supone operación continua; son turnos |
| **M2** | **Nueva restricción**: percolación | — | $\sum_p \nu_p\,\lambda_{pt}\,LOTE_p \le 24\,V^{perc}$ | **Restricción de VOLUMEN de carga, no de conteo de lotes.** $\lambda_{pt}=X_{pt}/LOTE_p$ = nº de lotes (continuo); $\nu_p$ = litros de carga por litro de salida (**D11**). La versión $\sum_p\lambda_{pt}\le 24$ es incorrecta: supone que todos los lotes ocupan lo mismo, y $\nu_p$ varía ~2× entre recetas. Requiere **$V^{perc}$ (D4)** y **$\nu_p$ (D11)** |
| **M2b** | **Posible nuevo arco**: reciclo de alcohol | — | retorno de alcohol recuperado en destilación al almacén | Sólo si **D12** resuelve (a). Afecta el balance de insumos y el inventario de `AET002`, no el costo |
| **M3** | Cambio de producto | no modelado | $\sum_p (kx_p LOTE_p + 1)\lambda_{pt} \le capX$ | **Sin binarias**: el cambio escala con el nº de lotes |
| **M4** | Retención | $\delta = 1$ | $\delta = 2$ | **Cae la proposición $W_{pt}=X_{pt}$ que justifica (S4)**; pasa a $W_{pt}=X_{pt}+X_{p,t-1}$. Rehacer la demostración. Y fijar $W_{p,T}$ **y** $W_{p,T-1}$ (ver prueba 4) |
| **M5** | $L^M$ | 3 zonas geográficas τ = 15/30/45 d | 4 clases de crédito τ = 0/1/2/4 sem | Redefine el conjunto, no sólo el parámetro |
| **M6** | $\BOM^2$ | $a^2 \in \{0.5,1.0\}$ | $\{1.0,5.0\}$ + envase | Requiere nodo e inventario de envase en instalación B |
| **M7** | Abastecimiento | $e_r$, un proveedor por insumo, partición $K$ sobre $R$ | $e_{rs}$ indexado por par, **sin** partición nacional/importado | **$K$ deja de ser partición de $R$**; reescribir la propiedad de pago único sobre pares $(r,s)$. Crédito **desde la recepción** |
| **M8** | **Nueva restricción**: piso de stock de ciclo | — | $I_{p,B_S,t} \ge LOTE_p/2$ | Sin esto el LP continuo subestima NOA en ~$136 k. Se **elimina** en el MILP |

### Pendiente estructural por confirmar

**Flujo de envases.** `GNT001` (garrafa 20 L) está en las 34 recetas a $a^1 = 0.05$
(1 garrafa / 20 L) y se consume en la planta de destilación (instalación A). Pero se recibe
en el Almacén General (instalación B). Si es así, **el transporte inter-planta es
bidireccional**: granel y WIP van A→B, envase de 20 L va B→A. La formulación actual sólo
tiene el arco A→B. Confirmar antes de implementar.

### Reformulación recomendada y no obligatoria

**Asimetría fiscal sin binarias.** En lugar de $TAX_t = \tau\max(0,\cdot)$, usar cuenta de
arrastre de pérdidas:

$$NOL_t = NOL_{t-1} - (EBIT_t - NIE_t) + TI_t,\qquad NOL_t \ge 0,\ TI_t \ge 0,\qquad TAX_t = \tau^{isr} TI_t$$

El óptimo reproduce $TI_t=\max(0,\ base_t-NOL_{t-1})$ **sin binarias**, y además modela la
amortización de pérdidas de la ley mexicana (hasta 10 ejercicios), que el $\max(0,\cdot)$
destruye. Requiere: objetivo monótono decreciente en $TAX_t$ (verificar con el Término V) y
$NOL_0$.

---

## 5. ETAPA 3 — Validar el LP

Dos fuentes que hay que fusionar: el **protocolo de 4 niveles** de
`Balance_y_protocolo_v1.0_20261026.md` (§4.2–4.6) y las **8 pruebas** de
`Verificacion_computacional_v1.0_20261026.md`. Las 8 pruebas siguen siendo correctas; lo que
cambia es la instancia sobre la que corren.

### Mapeo entre el plan de validación y el protocolo

| Prueba | Elemento del protocolo | Frecuencia en el corpus | Estado |
|---|---|---|---|
| Identidad contable (A1) recalculada fuera del modelo | 2.1 (= Prueba 2) | E1: 49.2 % | por ejecutar |
| Instancias de solución conocida (1a–1e) | 2.1 (= Prueba 1) | — | por ejecutar |
| **Balance de masa: BOM vs compras** | 3.6 validar datos de entrada | **F2: 7.7 %** | ✅ **PASÓ** |
| **Stock de ciclo predicho vs observado** | 3.2 validación cruzada | **G3: 1.5 %** | ✅ **PASÓ** (7 % de error) |
| Condiciones extremas | 3.4 (= Prueba 3) | G8: 9.2 % | por ejecutar |
| Borde de fin de horizonte por $W_{pT}$ | — (= Prueba 4) | — | por ejecutar |
| No arbitraje entre $i^{wacc}$, $i^{FI}$, $i^{DS}$ | 4.2 (= Prueba 5) | **H2: 0/65** | por ejecutar |
| Monotonías y direcciones | 2.2 (= Prueba 6) | G7: 52.3 % | por ejecutar |
| Precios sombra interpretables | — (= Prueba 7) | — | por ejecutar |
| Degeneración / independencia del solver | — (= Prueba 8) | — | por ejecutar |
| SEQ-1 / SEQ-2 vs integrado | 3.7 comparar contra práctica actual | G4b: 15.4 % | por ejecutar |
| Sensibilidad financiera separada | 4.4 | H4: 10.8 % | por ejecutar |
| Parámetros financieros con fuente citada | 4.2 | **H2: 0/65** | plantilla lista |
| Revisión por experto financiero | 4.5 | **H5: 0/65** | 2 reuniones |

### Evidencia de las dos pruebas ya pasadas

**F2 — balance de masa (ejecutada 2-nov-2026).** Consumo implicado por BOM × ventas de 12
meses contra compras reales del mismo período:
- Razón compras/consumo **mínima global = 1.01**; **ningún insumo < 1.0** ⇒ el BOM nunca
  sobreestima consumo. Es cota inferior consistente.
- Mediana 3.67; el exceso se explica porque los insumos compartidos alimentan también a los
  114 productos no modelados.
- **Subconjunto de validación: 11 insumos** casi exclusivos de los 34 (`CMH002`, `CTA005`,
  `EHO001`, `GBH001`, `HHO001`, `LFL001`, `NHP001`, `PBA001`, `PBT001`, `PRE001`, `XFP001`),
  con razón entre 1.01 y 1.49.
- **Criterio de aceptación para el modelo:** sobre esos 11, el consumo calculado debe quedar
  en [0.7, 1.0] × compras observadas.
- **Criterio endurecido (v1.1): correr la prueba también POR RECETA, no sólo por insumo.**
  La versión agregada **no detectó** las tres recetas con alcohol ≈ 0 (D13), porque al sumar
  `AET002` sobre las 34 recetas el error de tres se diluye. Chequeo por receta: para cada
  semiterminado, verificar que el coeficiente de cada insumo esté dentro de un rango plausible
  respecto de la mediana del resto de las recetas que usan ese mismo insumo. Un coeficiente
  3 órdenes de magnitud por debajo de la mediana es error de captura, no variación de receta.

**G3 — validación cruzada del stock de ciclo (ejecutada).** Predicción por la vía del tamaño
de lote: $\sum_p LOTE_p/2 + $ percolación + cuarentena ≈ **2 367 L**. Observado en
`Inventarios_iniciales`: **2 195 L**. Error **7 %**.
En el MILP esta prueba se vuelve la más fuerte: con lotes enteros el modelo debe **predecir**
los 2 195 L sin que nadie se los imponga.

### Dos avisos sobre pruebas concretas

**Prueba 4 (borde de fin de horizonte) se agrava con δ = 2.** Con δ = 1 quedaba una cohorte
sin fijar; con δ = 2 quedan **dos**. Fijar $W_{p,T}$ y $W_{p,T-1}$, o reportar indicadores
sobre las primeras $T-2$ semanas declarando el borde.

**Elemento 4.3 (conciliar con datos contables) va a fallar**, y no por el modelo: los $2.4 M
de insumos obsoletos están en el balance y ninguna política razonable los explica. Declararlos
constante exógena en NOA (decisión D3) **antes** de intentar la conciliación, o se pierden
días buscando un error de formulación inexistente.

---

## 6. Convenciones de implementación

### Sincronía LaTeX ↔ Pyomo

El riesgo principal de este proyecto es que la formulación y el código divergan. Convención:

- Cada ecuación del LaTeX lleva etiqueta estable: `\label{eq:bal_insumo}`.
- Cada `Constraint` de Pyomo lleva `doc="eq:bal_insumo"` con la **misma** etiqueta.
- Un script `tests/test_sync.py` extrae las etiquetas del `.tex` y las `doc` del modelo y
  falla si alguna ecuación no está implementada o si hay una restricción sin ecuación.

### Estructura de carpetas sugerida

```
modelo/
  formulacion.tex          # fuente única de la formulación
  model_lp.py              # Pyomo, LP
  model_milp.py            # Pyomo, MILP (hereda bloques del LP)
  params.py                # tipos y validación de parámetros
datos/
  calibracion_real.py      # lee HOJA_DATOS.xlsx → estructura + distribuciones
  generador.py             # instancias sintéticas por régimen
  instancias/              # .json por instancia, con semilla y metadatos
pruebas/
  test_sync.py             # LaTeX ↔ Pyomo
  test_identidad.py        # Prueba 2: auditoría contable fuera del modelo
  test_conocidas.py        # Prueba 1: casos 1a–1e
  test_extremos.py         # Prueba 3
  test_monotonias.py       # Prueba 6
  test_duales.py           # Prueba 7
  test_balance_masa.py     # F2, criterio de los 11 insumos
resultados/
  <instancia>/solucion.csv, duales.csv, auditoria.json
```

### 6.4 Ficha de entregable — estructura obligatoria

**Un entregable por sesión de trabajo**, no por día. Cada uno llega con una ficha de cinco
campos. **Ningún entregable se acepta sin ficha completa.**

| # | Campo | Qué contiene | Regla |
|---|---|---|---|
| 1 | **Qué se produjo** | El artefacto, con su ruta en el repositorio | — |
| 2 | **Qué significa** | Interpretación en lenguaje llano, sin notación ni jerga | máx. 5 líneas |
| 3 | **Cómo lo verificas tú** | **Un número o un signo** comprobable en ≤ 10 min sin re-derivar nada | obligatorio y específico |
| 4 | **Qué haría dudar del resultado** | La condición que, si ocurre, lo invalida | obligatorio |
| 5 | **Qué decisión habilita** | Por qué existe este entregable | — |

**Regla de diseño del campo 3: todo entregable debe reducirse a UN número o UN signo
verificable.** Si no se puede, el entregable está mal definido o es demasiado grande: pártelo.
No vale "revisa la tabla y verás que es consistente" — eso no es verificable, es confianza.

**Regla de comprensión (el autocontrol que pediste).** Antes de aceptar un entregable,
escribe en **una oración** qué mostró, **sin volver a leer el campo 2**. Si no puedes,
no lo entendiste: pide la explicación otra vez, en otros términos, antes de avanzar.
Aceptar un entregable que no entiendes es cómo se acumula deuda que explota tres etapas
después.

#### Tres ejemplos de ficha bien hecha

**Entregable: auditoría de identidades contables (Prueba 2)**
1. `resultados/<instancia>/auditoria.json` + tabla por identidad
2. El EVA recalculado fuera del modelo, desde las variables primitivas, coincide con el valor
   objetivo del solver. Si coincide, las identidades contables están bien implementadas.
3. **Un número: el error relativo máximo. Debe ser < 1e-6.** Está en `auditoria.json`,
   campo `max_rel_error`.
4. Que el error sea < 1e-6 en el agregado pero > 1e-6 en alguna identidad individual: significa
   que dos errores se cancelan. **Exigir el máximo por identidad, no el del total.**
5. Habilita toda la etapa 3: sin esto, ninguna otra prueba es interpretable.

**Entregable: balance de masa por insumo y por receta (F2)**
1. `resultados/balance_masa.csv`
2. El consumo que implica el BOM nunca debe exceder lo que se compró. Y ningún coeficiente de
   receta debe estar órdenes de magnitud fuera de la mediana de las demás recetas que usan
   ese insumo.
3. **Dos números: la razón mínima compras/consumo (debe ser ≥ 1.0) y el número de
   coeficientes marcados como atípicos (debe ser 0 después de resolver D13).**
4. Que la razón mínima sea < 1.0 en algún insumo: el BOM pide más de lo que se compra, lo cual
   es imposible en estado estacionario ⇒ error de coeficiente o de unidad.
5. Habilita confiar en el BOM como estructura física. Es el elemento 3.6 del protocolo (F2:
   7.7 % del corpus).

**Entregable: monotonías (Prueba 6)**
1. `resultados/monotonias.csv`, 6 filas × 2 corridas
2. El modelo reacciona en la dirección correcta a cada parámetro: subir el WACC debe bajar el
   inventario, subir $\tau_z$ debe bajar el EVA, y así.
3. **Un signo por fila: la columna `coincide` debe ser `True` en las 6.**
4. Cualquier fila en `False` es un signo invertido en la formulación, y la prueba te dice
   exactamente en qué término buscarlo. **No se avanza con una sola fila en `False`.**
5. Habilita el elemento 2.2 del protocolo y descarta la clase de error más común y más
   silenciosa.

### Reglas de código

- **Toda instancia lleva semilla y metadatos** en su `.json`: qué es real, qué es sintético,
  con qué regla se generó. Es el insumo del elemento 4.2 del protocolo.
- **La auditoría contable corre siempre** después de resolver, no como test opcional.
  Criterio: error relativo < 1e-6 por identidad. Si falla, nombra la ecuación.
- **Exportar duales siempre** en el LP. Son evidencia de la prueba 7 y material de la
  discusión gerencial.
- **No hardcodear parámetros** en el modelo: todos vienen del `.json` de la instancia.
- Unidades en litros y pesos, declaradas en los nombres (`litros`, `mxn`).

---

## 7. ETAPA 4 — MILP: binarias justificadas

Principio: **una binaria de primera etapa cuesta una; una de recurso cuesta una × escenarios.**
Por eso importa clasificarlas antes de escribirlas, incluso en el MILP determinista.

| Variable | Qué decide | Cantidad | Dato que falta | Veredicto |
|---|---|---|---|---|
| **$n_{pt}$** enteras | nº de lotes por receta y semana: $X_{pt}=LOTE^{fis}_p n_{pt}$ | 34 × 52 = 1 768, acotadas 0..3. Con integralidad sólo en $t=1..4$: **136** | `V_perc` (D4) | **SÍ — la más fiel.** Hace endógeno el lote y **elimina M8** (el piso de ciclo). La variable entera correcta es el nº de lotes, no una binaria de setup |
| **$y_{rs}$** binarias | proveedor habilitado por insumo | **96** (37 insumos ya fijos por presolve) | ninguno | **SÍ.** Estructura GUB ($\sum_s y_{rs}=1$), muy tratable por B&B |
| **$z_t$** binarias | tercer turno | 52 | costo incremental del turno | **SÍ, pero sin valor hoy** si la capacidad es holgada. Útil para escenarios de crecimiento |
| **$w_{rt}$** binarias | lote mínimo de compra | 50 relaciones × 52 = 2 600 | $Q^{min}_{rs}$ | **PROBABLE.** Empezar sin él: el rezago **ya es lineal** |
| $TAX^+/TAX^-$ | asimetría fiscal | 52 | $NOL_0$ | **NO** — usar la cuenta NOL lineal (§4) |
| $\delta^{seq}_{pp't}$ | secuenciación de corridas | ~34²×52 | matriz de cambio por par | **NO APLICA.** El cambio es 1 h plana, independiente del par. Modelarlo inventa un fenómeno que los datos niegan |
| $z_q$ | surtido de SKU | 68 | costo fijo por SKU | **NO con 68 SKU.** Sería muy relevante con los 148: la cola amarra ~$270 k con un tercio del volumen |

**Configuración recomendada para el primer MILP:**
$y_{rs}$ (96) + $n_{pt}$ con integralidad en $t=1..4$ (136) + $z_t$ (52) = **284 discretas.**
Meter todo sin restringir la ventana serían 4 516.

**Advertencia:** $n_{pt}$ es la que más cambia los resultados. Al volverse grumosa la
producción, **la capacidad aprieta más que en el LP continuo.** El MILP probablemente dirá que
la capacidad importa más de lo que el LP sugiere; no concluir del LP que es irrelevante.

**Resultado esperado y publicable:** comparar el lote que el MILP elige contra el `LOTE` tipo
EOQ actual. *"El lote económico clásico se desvía X % porque omite el escudo fiscal y el plazo
de pago."*

---

## 8. ETAPA 5 — Validar el MILP

| Prueba | Qué cambia |
|---|---|
| Cota de relajación | Objetivo del LP relajado **≥** objetivo del MILP. Si no, hay error de implementación |
| **Identidad contable (A1)** | **Revalidar completa.** Las binarias no la deben romper; si la rompen, hay un término mal acoplado |
| Simetría de proveedor | Con precios iguales entre proveedores, el modelo debe ser indiferente |
| $z_t$ tercer turno | Con costo 0 lo toma siempre; con costo infinito nunca |
| **G3 — stock de ciclo** | **La prueba estrella.** Con M8 eliminado, el modelo debe predecir ≈ 2 195 L de granel por sí solo |
| Degeneración (Prueba 8) | Cambia de naturaleza: reportar **gap de optimalidad**, no sólo unicidad del plan |
| Nuevo | Tiempo de resolución y tamaño de instancia ⇒ alimenta la pregunta específica 2 |

---

## 9. Lo que NO se hace en esta sesión

- Estocástico, escenarios, VSS, descomposición.
- Gemelo digital, disparadores de reoptimización.
- Modelar los 114 productos de la cola uno por uno.
- Secuenciación de corridas.
- Levantar más datos de la empresa de los que §3 declara faltantes (D4, D6).

---

## 10. Primera acción concreta

0. **Resolver las cuatro decisiones bloqueantes antes de escribir código**: **D4** ($V^{perc}$),
   **D11** ($\nu_p$), **D12** (reciclo de alcohol) y **D13** (las tres recetas con alcohol ≈ 0).
   D4 y D11 impiden escribir M2; D12 puede agregar un arco; D13 sesga el plan hacia tres
   productos. Son preguntas a planta y a producción, no cálculos.
1. Llenar y firmar la tabla de decisiones **D1–D13** de §3.
2. Correr el modelo actual **tal como está** y reportar tres cosas: si resuelve, el valor del
   objetivo, y cuánto tardó. Es el chequeo más barato del proyecto y determina si la etapa 2
   empieza en implementación o en reparación.
3. Montar `test_sync.py` **antes** de modificar la formulación, para que las siete
   modificaciones de §4 queden verificadas por construcción.

---

## 11. Expediente del proyecto — qué documentos, para qué, y por qué importa cada uno

Carpeta de trabajo propia, separada de los archivos del corpus. Diez documentos, ni uno más.

### 11.1 Núcleo — sin esto no se puede trabajar

| Archivo | Objetivo | Por qué importa | Estado |
|---|---|---|---|
| **Este prompt** | Contrato del proyecto: hechos, correcciones, decisiones, etapas | Es la única memoria entre sesiones. Si algo contradice al prompt, se actualiza el prompt — no se trabaja contra él | ✅ v1.1 |
| `HOJA_DATOS.xlsx` | Toda la estructura y los datos reales | Fuente única de la calibración. Cualquier número del modelo se rastrea aquí | ✅ |
| `formulacion.tex` | Fuente única de la formulación | Si el modelo y el LaTeX divergen, no sabes qué validaste. `test_sync.py` lo garantiza | ⬜ por crear |
| **`BITACORA_DECISIONES.md`** | Una fila por decisión: fecha, qué se decidió, alternativas, justificación, quién | **El documento más importante y el que no existe.** Sin él, en marzo nadie recuerda por qué δ = 2 ni por qué se descartó el período mensual. Y es evidencia directa del elemento 2.4 del protocolo | 🔴 **por crear** |
| **`REGISTRO_VALIDACION.md`** | Una fila por prueba: criterio preinscrito, resultado, evidencia, fecha, estado | Es el expediente de la etapa 3 y la tabla que va al artículo. **Criterio escrito ANTES de correr la prueba**, o no es validación: es justificación a posteriori | 🔴 **por crear** |

### 11.2 Soporte — se consultan, no se editan

| Archivo | Objetivo | Por qué importa |
|---|---|---|
| `LEVANTAMIENTO_DATOS_MODELO_LP.xlsx` | 41 parámetros faltantes con fuente, ejemplo y celda de captura | **Su bloque B es el artefacto del elemento 4.2 (H2 = 0/65 del corpus).** Llenarlo produce la tabla que ningún estudio comparable reporta |
| `Balance_y_protocolo_v1.0_20261026.md` | Protocolo de validación de 4 niveles con la frecuencia del corpus por elemento | Permite escribir *"aplicamos una comprobación que 1 de 65 estudios reporta"*, que es verificable. Define también el mínimo de 7 elementos (§4.6) |
| `Verificacion_computacional_v1.0_20261026.md` | Las 8 pruebas de verificación | Las pruebas son correctas. **Advertencia: describe la formulación antigua** (5 semis, 3 zonas, δ=1). La instancia cambió, las pruebas no |
| `Tablas_bloques_Janova_20261026.xlsx` | Frecuencias del corpus por elemento | De aquí sale cada porcentaje que se cite |

### 11.3 Generados por el trabajo

| Archivo | Objetivo | Por qué importa |
|---|---|---|
| `datos/instancias/*.json` | Una instancia por archivo, con semilla y metadatos | **Cada una declara qué es real y qué es sintético, con su regla de generación.** Es lo que hace reproducible el barrido y lo que sostiene el elemento 4.2 |
| `resultados/<instancia>/` | Solución, duales, auditoría contable | Los duales son evidencia de la Prueba 7 y material de la discusión gerencial. La auditoría corre siempre, no es opcional |

### 11.4 Lo que NO va en esta carpeta

Los archivos del corpus —`Libro_maestro_v*.xlsx`, `Concordancia_*.xlsx`,
`Verificacion_G12_*.xlsx`, `Codificacion_ciega_*.xlsx`, `Muestreo_ciego_*`, `Scopus/`— pertenecen
al **otro artículo** (validación del instrumento sobre 65 artículos). Se citan desde aquí, no se
mezclan. Mezclarlos es cómo se repite el desplazamiento de objeto que el propio
`Balance_y_protocolo` documenta en su §2.3.

`Productos_Clase_A_MILP_Estocastico.xlsx` queda como **borrador a revisar**, no como insumo:
tiene 3 presentaciones (ver D1) y BOM colapsado (ver D2). No construir sobre él sin cerrar esas
dos decisiones.

### 11.5 Estructura de la bitácora y del registro

**`BITACORA_DECISIONES.md`** — una tabla, append-only, nunca se borra una fila:

| Fecha | ID | Decisión | Alternativas consideradas | Justificación | Fuente / quién |
|---|---|---|---|---|---|
| 2026-11-02 | D10 | Período semanal, T=52 | mensual T=12; híbrido 13+9 | 9 de 12 mecanismos son subperíodo mensual; el híbrido ensucia (A1) | §3 del prompt |

**`REGISTRO_VALIDACION.md`** — el criterio se escribe antes de correr:

| Prueba | Elemento | Criterio **preinscrito** | Fecha criterio | Resultado | Fecha resultado | Evidencia | Estado |
|---|---|---|---|---|---|---|---|
| F2 balance de masa | 3.6 | razón mín ≥ 1.0; 11 insumos en [0.7, 1.0] | 2026-11-02 | mín 1.01; 11/11 en rango | 2026-11-02 | `balance_masa.csv` | ✅ |
| G3 stock de ciclo | 3.2 | predicho vs observado dentro de ±15 % | 2026-11-02 | 2 367 vs 2 195 L = 7 % | 2026-11-02 | §5 del prompt | ✅ |
| A1 identidad contable | 2.1 | error rel. máx < 1e-6 **por identidad** | ⬜ | ⬜ | ⬜ | ⬜ | ⬜ |

Las dos primeras filas ya están cerradas y hay que registrarlas con su trazabilidad; las demás
se agregan con el criterio escrito antes de la corrida.
