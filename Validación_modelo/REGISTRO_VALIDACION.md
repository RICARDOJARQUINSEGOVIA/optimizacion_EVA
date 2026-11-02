# Registro de validación — Modelo LP → MILP

**Regla fundamental: el criterio se escribe ANTES de correr la prueba.** Una columna de criterio
llenada después del resultado no es validación, es justificación a posteriori. La fecha del
criterio y la del resultado se registran por separado precisamente para que esto sea verificable.

**Regla de aceptación:** ninguna prueba se marca ✅ sin evidencia en archivo y sin que el campo
3 de su ficha (§6.4 del prompt) se haya comprobado de forma independiente.

Leyenda: ✅ pasa · ❌ falla · ⬜ pendiente · ⏸ bloqueada

---

## 1. Verificación — no requiere datos reales

| Prueba | Elemento | Criterio preinscrito | Fecha criterio | Resultado | Fecha resultado | Evidencia | Estado |
|---|---|---|---|---|---|---|---|
| **V-01** Identidad contable (A1) | 2.1 · Prueba 2 | Error relativo máx **< 1e-6 por identidad**, no sólo en el total | 2026-11-02 | | | `resultados/*/auditoria.json` | ⬜ |
| **V-02** Caso 1a: demanda cero | 2.1 · Prueba 1 | Todas las variables físicas en 0; EVA = −(costos fijos + cargo de capital sobre $fa_0$ y posiciones iniciales) | 2026-11-02 | | | | ⬜ |
| **V-03** Caso 1b: instancia mínima resoluble a mano | 2.1 · Prueba 1 | 1 insumo, 1 semi, 1 SKU, 1 clase, $T=2$. Óptimo calculado a mano = óptimo del solver | 2026-11-02 | | | | ⬜ |
| **V-04** Caso 1c: margen negativo, $p^{lost}=0$ | 2.1 · Prueba 1 | El modelo deja de producir y sirve demanda cero. Si produce, hay un signo invertido en el objetivo | 2026-11-02 | | | | ⬜ |
| **V-05** Caso 1d: $p^{lost}$ alto, capacidad holgada | 2.1 · Prueba 1 | $LS_{qzt}=0$ en todo $q,z,t$ | 2026-11-02 | | | | ⬜ |
| **V-06** Caso 1e: $i^{wacc}=0$ | 2.1 · Prueba 1 | **El modelo debe colapsar al de min-costo.** Si no colapsa, los dos objetivos no son comparables y el experimento central de la tesis queda inválido | 2026-11-02 | | | | ⬜ |
| **V-07** Balances de masa por nodo y período | 2.1 · Prueba 2 | Entradas − salidas − Δinventario = 0, error < 1e-6, para cada nodo y cada $t$ | 2026-11-02 | | | | ⬜ |
| **V-08** Sincronía LaTeX ↔ Pyomo | — | 0 ecuaciones sin implementar y 0 restricciones sin ecuación | 2026-11-02 | | | `pruebas/test_sync.py` | ⬜ |

> **V-06 es la prueba más informativa del bloque.** Toda la hipótesis de la tesis depende de que
> EVA y min-costo sean el mismo modelo con objetivos distintos. Correrla primero.

---

## 2. Validación estructural contra la instancia real

| Prueba | Elemento | Corpus | Criterio preinscrito | Fecha criterio | Resultado | Fecha resultado | Evidencia | Estado |
|---|---|---|---|---|---|---|---|---|
| **F2** Balance de masa BOM vs compras | 3.6 | **7.7 %** | Razón compras/consumo **≥ 1.0 en todos** los insumos; en los 11 casi exclusivos, consumo calculado ∈ [0.7, 1.0] × compras | 2026-11-02 | **Razón mín. global = 1.01; ningún insumo < 1.0; los 11 entre 1.01 y 1.49** | 2026-11-02 | §5 del prompt | **✅** |
| **F2b** Balance de masa **por receta** | 3.6 | — | 0 coeficientes a más de 2 órdenes de magnitud de la mediana del mismo insumo en otras recetas | 2026-11-02 | **3 atípicos detectados** (`EFJ001`, `EFM077`, `EFA010`, alcohol ≈ 0) | 2026-11-02 | §1.8 del prompt | **❌ → D13** |
| **G3** Stock de ciclo predicho vs observado | 3.2 | **1.5 %** | Predicción por tamaño de lote dentro de ±15 % del granel observado | 2026-11-02 | **2 367 L predichos vs 2 195 L observados = 7 % de error** | 2026-11-02 | `Inventarios_iniciales` | **✅** |
| **E-03** Cierre de capacidad | 2.1 | — | Horas implicadas por las ventas = 1 678 h/año ± 10 % | 2026-11-02 | | | | ⬜ |
| **E-04** Conteo de lotes | 2.1 | — | 263 lotes/año ± 15 % | 2026-11-02 | | | | ⬜ |
| **E-05** Replay histórico | 2.3 | 36.9 % | Con decisiones fijadas a lo observado, el modelo es factible y reproduce las trayectorias de inventario | 2026-11-02 | | | | ⏸ falta historial de producción |

> **F2b falló y es un hallazgo, no un error del modelo.** La versión agregada de F2 **no lo
> detectó**: al sumar `AET002` sobre 34 recetas, el error de tres se diluye. De ahí la regla de
> correr la prueba también por receta. Resolver D13 y re-ejecutar.

---

## 3. Comportamiento del modelo

| Prueba | Elemento | Corpus | Criterio preinscrito | Fecha criterio | Resultado | Fecha resultado | Estado |
|---|---|---|---|---|---|---|---|
| **C-01** Monotonías (6 filas × 2 corridas) | 2.2 · Prueba 6 | 52.3 % | Las 6 filas con signo correcto. **Una sola en `False` detiene el avance** | 2026-11-02 | | | ⬜ |
| **C-02** Condiciones extremas (7 escenarios) | 3.4 · Prueba 3 | 9.2 % | Cada escenario se comporta como se predijo o es infactible **por la razón correcta** | 2026-11-02 | | | ⬜ |
| **C-03** Borde de fin de horizonte vía $W_{pT}$ | Prueba 4 | — | Sin salto en $\sum_p W_{pt}e^W_p$ en $t=T$; $X_{pT}$ no anómalo respecto de $X_{p,T-1}$. **Con δ=2 hay que fijar $W_{p,T}$ y $W_{p,T-1}$** | 2026-11-02 | | | ⬜ |
| **C-04** No arbitraje entre las tres tasas | 4.2 · Prueba 5 | **0/65** | $DS_t$ y $FI_t$ no positivos simultáneamente sin motivo operativo. Relación de orden entre $i^{wacc}$, $i^{FI}$, $i^{DS}$ escrita **con su fuente** | 2026-11-02 | | | ⬜ |
| **C-05** Precios sombra interpretables | Prueba 7 | — | Dual de capacidad ≥ 0 si activa, 0 si holgada, y magnitud plausible: del orden de margen/hora ≈ $296/L ÷ 0.073 h/L ≈ **$4 000/h** | 2026-11-02 | | | ⬜ |
| **C-06** Degeneración / independencia del solver | Prueba 8 | — | Mismo valor objetivo con dos métodos, diferencia relativa < 1e-8. **Planes distintos con igual objetivo NO es error: es degeneración y se reporta** | 2026-11-02 | | | ⬜ |
| **C-07** Sensibilidad financiera, separada de la operativa | 4.4 | 10.8 % | Tabla propia: cuánto se mueve la **decisión** (no sólo el EVA) si la tasa cambia 200 pb | 2026-11-02 | | | ⬜ |
| **C-08** SEQ-1 / SEQ-2 vs integrado | 3.7 | 15.4 % | Brecha de EVA reportada con su signo. **Puede salir negativa si $h > i^{wacc}(1-\tau)$** — hay que verificarlo, no suponerlo | 2026-11-02 | | | ⬜ |
| **C-09** Período semanal vs mensual | — | — | Cuantificar cuánto del EVA diferencial desaparece al agregar a mes. Predicción: los canales de CxC y WIP se anulan casi por completo | 2026-11-02 | | | ⬜ |

---

## 4. Capa financiera — los ceros del corpus

| Prueba | Elemento | Corpus | Criterio preinscrito | Fecha criterio | Estado |
|---|---|---|---|---|---|
| **H-01** Métrica financiera definida con su fórmula y fuente contable | 4.1 | 81 % de los que aplican | EVA definido como lo define la disciplina financiera, con cita | 2026-11-02 | ⬜ |
| **H-02** **Fuente citada de cada parámetro financiero** | 4.2 | **0/65** | Tabla completa: tasa de descuento, costo de capital, plazos. Sin celdas vacías en la columna de fuente | 2026-11-02 | ⬜ · plantilla en `LEVANTAMIENTO_DATOS`, bloque B |
| **H-03** Conciliar con datos contables reales | 4.3 | 7.7 % | NOA inicial reconcilia con el balance ±15 % **excluyendo el inventario obsoleto declarado** | 2026-11-02 | ⏸ depende de D3 y del corte contable |
| **H-04** **Revisión por experto financiero** | 4.5 | **0/65** | Párrafo con perfil del revisor, qué revisó y **qué objetó** | 2026-11-02 | ⬜ · agendar |
| **H-05** Validación de cara con externos | 3.1 | 1.5 % | Párrafo con el perfil del revisor y qué objetó | 2026-11-02 | ⬜ · agendar |

> **H-02 y H-04 son ceros absolutos sobre 65 artículos.** Cuestan un día y dos reuniones, y son
> lo más diferencial del expediente. H-04 y H-05 dependen de agenda ajena: **pedirlas esta
> semana**, o no ocurren.
>
> **H-03 va a fallar** si no se declara antes el inventario obsoleto ($2.4 M, 63 % del inventario
> de insumos) como constante exógena. No es un error del modelo: ninguna política razonable lo
> explica. Resolver D3 primero.

---

## 5. MILP — se ejecuta después de cerrar las secciones 1 a 4

| Prueba | Criterio preinscrito | Fecha criterio | Estado |
|---|---|---|---|
| **M-01** Cota de relajación | Objetivo del LP relajado **≥** objetivo del MILP | 2026-11-02 | ⬜ |
| **M-02** Identidad contable (A1) con binarias | **Re-ejecutar V-01 completa.** Error < 1e-6 por identidad. Si falla, hay un término mal acoplado | 2026-11-02 | ⬜ |
| **M-03** Simetría de proveedor | Con precios iguales entre proveedores, el modelo es indiferente | 2026-11-02 | ⬜ |
| **M-04** Tercer turno | Con costo 0 lo toma siempre; con costo infinito nunca | 2026-11-02 | ⬜ |
| **M-05** **G3 en versión fuerte** | Con M8 (piso de ciclo) eliminado, el modelo **predice** ≈ 2 195 L de granel por sí solo, ±20 % | 2026-11-02 | ⬜ |
| **M-06** Gap de optimalidad | Reportado por instancia. Es parte del resultado, no una nota al pie | 2026-11-02 | ⬜ |
| **M-07** Lote MILP vs lote EOQ actual | Desviación porcentual reportada por receta. **Es el resultado publicable** | 2026-11-02 | ⬜ |

> **M-05 es la prueba estrella del proyecto.** Es validación cruzada en su forma fuerte: el
> modelo reproduce un nivel de inventario que no está en sus datos de entrada. G3 está al 1.5 %
> del corpus; esta versión es más exigente todavía.

---

## Resumen de avance

| Bloque | Total | ✅ | ❌ | ⏸ | ⬜ |
|---|---|---|---|---|---|
| 1 · Verificación | 8 | 0 | 0 | 0 | 8 |
| 2 · Estructural | 6 | **2** | 1 | 1 | 2 |
| 3 · Comportamiento | 9 | 0 | 0 | 0 | 9 |
| 4 · Financiera | 5 | 0 | 0 | 1 | 4 |
| 5 · MILP | 7 | 0 | 0 | 0 | 7 |
| **Total** | **35** | **2** | **1** | **2** | **30** |

**Cerradas antes de escribir una línea de modelo:** F2 y G3 — las dos pruebas con menor
frecuencia en el corpus (7.7 % y 1.5 %).
**Falla abierta:** F2b, que destapó D13 y motivó endurecer el criterio de F2.
