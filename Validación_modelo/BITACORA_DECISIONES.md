# Bitácora de decisiones — Modelo LP → MILP

**Regla:** tabla *append-only*. Nunca se borra ni se edita una fila. Si una decisión se revierte,
se añade una fila nueva que la revierte, citando el ID de la original. El *porqué* vive aquí; el
*qué y cuándo* lo registra git.

**Campos.** `ID` · `Fecha` · `Decisión` · `Alternativas consideradas` · `Justificación` ·
`Fuente / evidencia` · `Estado`

---

## A. Alcance del proyecto

| ID | Fecha | Decisión | Alternativas | Justificación | Fuente | Estado |
|---|---|---|---|---|---|---|
| A-01 | 2026-11-02 | El doctorado es sobre **el método**, no sobre la empresa. La empresa aporta estructura y calibración de incertidumbre | tesis sobre el caso industrial | Permite barrido sintético y generalización; evita que el inventario obsoleto y los huecos de datos de la empresa bloqueen la tesis | Decisión del investigador | Firme |
| A-02 | 2026-11-02 | **Estructura y calibración de incertidumbre reales; niveles de régimen sintéticos y barridos** | (a) todo real; (b) todo sintético | (a) deja un solo punto del espacio de parámetros, en una región donde el efecto puede ser pequeño; (b) se lee como instancia de juguete | §3 del prompt | Firme |
| A-03 | 2026-11-02 | Instancia de **34 semiterminados / 68 SKU**; los otros 114 productos entran como capacidad reservada y constante en NOA | modelar los 148 | El catálogo real es 148, pero modelar la cola cuesta ~700 parámetros y su stock de ciclo es **constante en el objetivo** ⇒ no cambia el argmax | §1.1, D7 | Firme |
| A-04 | 2026-11-02 | **No** se hace estocástico, gemelo digital ni secuenciación en este ciclo | incluirlos | Son el segundo y tercer artículo. El LP validado es el cimiento y el entregable defendible de la semana ~11 | §9 del prompt | Firme |

---

## B. Estructura y unidades

| ID | Fecha | Decisión | Alternativas | Justificación | Fuente | Estado |
|---|---|---|---|---|---|---|
| B-01 | 2026-11-02 | `ki_π = ky_π = 1 litro` para granel y PT; insumos en su propia unidad | conversión por producto | El BOM ya está expresado **por litro de granel de salida** (prueba: `GNT001` = 0.05 en las 34 recetas = 1 garrafa / 20 L). Ahorra 366 parámetros | §1.8 | Firme |
| B-02 | 2026-11-02 | `CANT` del histórico está en **litros**, no en piezas | piezas | 2 087 de 2 092 líneas de 5 L son múltiplos de 5; 521 líneas de 1 L son fraccionarias; razón de precio 5 L/1 L = 0.88, no ≈5 | `HISTÓRICO` | Firme |
| B-03 | 2026-11-02 | $a^2_{qp} \in \{1.0,\ 5.0\}$ **más envase** en el nivel 2 | $\{0.5, 1.0\}$ (formulación original) | `BOM_PT`: 34 SKU de 1 L y 34 de 5 L, cada uno con 1 pza de `ENV_1` o `ENV_5` | `BOM_PT`, M6 | Firme |
| B-04 | 2026-11-02 | **$\delta = 2$ semanas** (percolación 7 d + cuarentena 7 d) | $\delta = 1$ | Nota de `TIEMPO_DEST`. **Arrastra:** cae la proposición $W_{pt}=X_{pt}$ que justifica (S4); pasa a $W_{pt}=X_{pt}+X_{p,t-1}$ y hay que rehacer la demostración | §1.2, M4 | Firme |
| B-05 | 2026-11-02 | **$L^M$ = 4 clases de crédito**, $\tau_z = 0, 1, 2, 4$ semanas | 3 zonas geográficas con $\tau = 2,4,6$ | No hay geografía en los datos: 352 prefijos distintos para 395 clientes activos (iniciales de razón social). Lo que hay son plazos de cobro. Se colapsan las clases de 20 d y 60 d (0.8 % juntas) | `CAT_CLIENT`, M5 | Firme |
| B-06 | 2026-11-02 | **Se elimina la partición $R^{(N)} / R^{(I)}$**, el arco especial $B_G \to B_P$ y la clase de pago por nacionalidad | conservarlas | Todo se compra a distribuidores nacionales; el título pasa al llegar al almacén de la instalación B. No hay insumo importado directo. **Es menos modelo, no más** | Confirmado por la empresa, M7 | Firme |
| B-07 | 2026-11-02 | **$\eta^{ef} = 0.95$**, no 0.85 | 0.85 | `HORA_UNIDAD` se midió de producción real (8–36 obs/receta), así que ya incluye paros y limpiezas. 0.85 contaría la ineficiencia dos veces | `TIEMPO_DEST` | Firme |
| B-08 | 2026-11-02 | **Período semanal, $T = 52$** | mensual $T=12$; híbrido 13 sem + 9 meses | **9 de 12 mecanismos quedan por debajo del período mensual** (plazo 0.26 m, δ 0.46 m, tres de cuatro clases de crédito < 0.5 m). Con período mensual se pierden la incertidumbre de aprovisionamiento, el término de WIP y el canal de CxC, y el CV de demanda cae de 0.76 a ~0.36 por agregación. El híbrido se descarta porque períodos desiguales ensucian la identidad (A1), que se audita a 1e-6 | §3, D10 | Firme |
| B-09 | 2026-11-02 | La preocupación del **MPS mensual** se resuelve con ventana de compromiso, no con longitud de período: semanal con congelado $t=1..4$, firme $t=5..13$, libre $t=14..52$; el plan se **reporta** agregado al mes | período mensual | Es como opera el MPS real. Encaja con la integralidad restringida del MILP | §3, D10 | Firme |

---

## C. Parámetros y fuentes

| ID | Fecha | Decisión | Alternativas | Justificación | Fuente | Estado |
|---|---|---|---|---|---|---|
| C-01 | 2026-11-02 | $e_r$ se toma **sólo** de `Insumos_PROV_SEMI`, indexado por par $(r,s)$. **Se descarta `COSTOU` de `BOM_SEMI`** | usar `COSTOU`; promediar las fuentes | 25 insumos tienen `COSTOU` distinto según receta y 25 caen fuera del rango de sus proveedores (`SFR002`: 394 vs 1 096). Los precios del catálogo **sí** son los reales: 97 % dentro de ±10 % de lo pagado, razón mediana 1.00. **Arrastra:** $K$ deja de ser partición de $R$; reescribir la propiedad de pago único sobre pares | §2, M7 | Firme |
| C-02 | 2026-11-02 | El **crédito de proveedor corre desde la recepción**, no desde la emisión de la orden | desde la orden | Confirmado por la empresa. **Corrige** la afirmación previa de que 38 % de las relaciones invertían el signo del crédito comercial: con crédito desde recepción nunca se paga antes de recibir | Empresa | Firme |
| C-03 | 2026-11-02 | **El inventario en tránsito no es propiedad de la empresa** ⇒ no consume capital | cargarlo a NOA | El título pasa al llegar al almacén de la instalación B. Elimina $142 500/año que se había cargado mal | Empresa | Firme |
| C-04 | 2026-11-02 | **(S2) es válido**: el plazo de aprovisionamiento es ≈ 1 semana | relajar (S2) con rezago de 13 semanas | Plazo **real** mediano de 8 d sobre 2 126 órdenes. El cruce contra el catálogo da med 8 d en **ambas** clases nominales (n = 818 y 486) ⇒ `TIEMPO_ENT = 90` no describe la entrega. Sólo 3.6 % de las órdenes se emiten con ≥60 d de anticipación | `Histórico_compras` | Firme |
| C-05 | 2026-11-02 | $\sigma_L$ **medida** = 10.9 d = 1.56 semanas (robusta, $(p90-\text{mediana})/1.2816$) | desviación muestral 43.9 d | La muestral está inflada por la cola (máx 437 d). Se usa la robusta y se declara | `Histórico_compras` | Firme |
| C-06 | 2026-11-02 | $\tau^{isr} = 0.30$ | — | Confirmado por la empresa | Empresa | Firme |
| C-07 | 2026-11-02 | Capacidad reservada para la cola: $\bar h^{cola} \approx 26$ h/sem, sensibilidad 21–31 | pseudo-producto agregado; modelar los 114 | Un número en lugar de 10 datos. El pseudo-producto **no puede reproducir a la vez** el conteo de lotes y el stock de ciclo: $CYC \propto \lvert P\rvert$ y $N \propto D$ (error de 34× en la prueba) | §3, D7 | Firme |
| C-08 | 2026-11-02 | $CYC^{cola} \approx \$270\,000$ como **constante en NOA** | modelarlo | La cola no es variable de decisión ⇒ su inventario es constante ⇒ **no cambia el argmax**, sólo el nivel reportado del EVA | §3, D7 | Firme |

---

## D. Formulación

| ID | Fecha | Decisión | Alternativas | Justificación | Fuente | Estado |
|---|---|---|---|---|---|---|
| F-01 | 2026-11-02 | $capX = h^{turno} n^{turno} d^{sem} n^{lin}\eta^{ef} - \bar h^{cola}$ | $168\,n^{lin}\eta^{ef}$ | La fórmula de 168 h supone operación continua 24/7; la planta opera por turnos | M1 | Firme |
| F-02 | 2026-11-02 | El **cambio de producto entra sin binarias**, escalando con el número de lotes: $\sum_p (kx_p LOTE_p + 1)\lambda_{pt} \le capX$ | absorberlo en $\eta^{ef}$; binarias de setup | Los lotes son grandes (cobertura mediana 8.1 semanas) ⇒ **5.1 lotes/semana, no 25 cambios**. El cambio es 6.3 % de la capacidad, no 31 % | M3 | Firme |
| F-03 | 2026-11-02 | **Nueva restricción de percolación por VOLUMEN DE CARGA**: $\sum_p \nu_p \lambda_{pt} LOTE_p \le 24\,V^{perc}$ | $\sum_p \lambda_{pt} \le 24$ (conteo de lotes) | El percolador se ocupa por el volumen que entra (alcohol + planta), no por el que sale. $\nu_p$ varía ~2× entre recetas ⇒ el conteo de lotes supone falsamente que todos ocupan lo mismo | §1.8, M2 | **Bloqueada por D4 y D11** |
| F-04 | 2026-11-02 | **Piso de stock de ciclo** $I_{p,B_S,t} \ge LOTE_p/2$ en el LP; **se elimina en el MILP** | sin piso; MILP directo | Un lote cubre 2.7–23.7 semanas (mediana 8.1), así que el LP continuo subestima NOA en ~$136 k. En el MILP el inventario sale endógeno de $n_{pt}$ | M8 | Firme |
| F-05 | 2026-11-02 | `LOTE_p` **congelado como dato en el LP**, endógeno en el MILP | endógeno desde el LP | Recalcular el EOQ fuera del modelo es optimizar dos veces: el modelo calcula el costo de capital mejor que la fórmula. En el LP no es variable, así que no distorsiona | §3, D5 | Firme |
| F-06 | 2026-11-02 | Asimetría fiscal con **cuenta lineal de arrastre de pérdidas** ($NOL_t$), no con binarias | 52 binarias con $\max(0,\cdot)$ | La cuenta lineal reproduce el óptimo **sin binarias** y además modela correctamente la amortización de pérdidas de la ley mexicana (hasta 10 ejercicios), que el $\max(0,\cdot)$ destruye. Requiere $NOL_0$ y verificar monotonía del objetivo en $TAX_t$ | §4 | Propuesta, por validar |
| F-07 | 2026-11-02 | **Sin binarias de secuenciación** | $\delta^{seq}_{pp't}$ | El cambio es **1 h plana, independiente del par**. Modelarlo inventaría un fenómeno que los datos niegan, y convertiría el problema en lot-sizing con secuenciamiento | §7, D-07 | Firme |
| F-08 | 2026-11-02 | MILP con **$y_{rs}$ (96) + $n_{pt}$ con integralidad en $t=1..4$ (136) + $z_t$ (52) = 284 discretas** | 4 516 sin restringir la ventana | $y_{rs}$ tiene estructura GUB, muy tratable. $n_{pt}$ entera (no binaria de setup) es la representación correcta: la decisión es *cuántos lotes*. La ventana implementable aprovecha el horizonte rodante | §7 | Firme |

---

## E. Decisiones pendientes

🔴 bloquea la implementación · 🟡 bloquea la interpretación

| ID | Decisión | Bloquea | Quién la resuelve | Estado |
|---|---|---|---|---|
| **D4** 🔴 | $V^{perc}$, volumen físico del percolador | M2 | Planta (ficha del equipo) | ⬜ |
| **D11** 🔴 | $\nu_p$, litros de carga por litro de extracto | M2 | Producción | ⬜ |
| **D12** 🔴 | ¿Se recupera el alcohol? (a) reciclo (b) merma (c) todo es de la cola | posible arco M2b; o corregir $a^1$ | Producción | ⬜ |
| **D13** 🔴 | `EFJ001`, `EFM077`, `EFA010` con alcohol ≈ 0 | sesga el plan hacia esos 3 | Producción / captura | ⬜ |
| **D6** 🟡 | Carga del destilador por los otros usos | si la capacidad es vinculante | Planta | ⬜ |
| **D1** | ¿Existe la presentación de 20 L? | $\lvert Q \rvert$ | Comercial | ⬜ |
| **D2** | BOM completo (165 pares) vs colapsado | el acoplamiento por insumos compartidos | Investigador | ⬜ |
| **D3** | Inventario obsoleto ($2.4 M): excluir o modelar liquidación | el elemento 4.3 del protocolo | Investigador | ⬜ |
| **D8** | Líneas con `CANT` ≤ 0 (44): netear o excluir | — | Investigador | ⬜ |
| **D9** | Redondeo de plazos a semanas | desplazamiento de $AP_t$, $AR_t$ | Investigador | ⬜ |
| — | Flujo de envases: ¿hay arco B→A para `GNT001`? | nodo e inventario de envase en A | Planta | ⬜ |
| — | Historial de producción (lotes por semi y fecha) | prueba de replay (Fase 1.5) | ERP | ⬜ |
| — | Segunda foto de inventario (p.ej. dic-2024) | balance de masa del año completo | ERP | ⬜ |

---

## F. Afirmaciones refutadas — no reabrir sin datos nuevos

| Fecha | Afirmación sostenida y luego refutada | Qué la refutó |
|---|---|---|
| 2026-10-29 | «Hay doble fuente con disyuntiva 7 vs 90 días» | Plazo real de 8 d mediano en **ambas** clases del catálogo |
| 2026-10-29 | «Hay que relajar (S2); el rezago es de 13 semanas» | (S2) es válido: 1.14 semanas |
| 2026-10-28 | «38 % de las relaciones tienen $\tau^{lead} > \tau^{pay}$, invirtiendo el signo del crédito» | El crédito corre desde la recepción |
| 2026-10-28 | «El inventario en tránsito cuesta capital» | No es propiedad de la empresa |
| 2026-10-27 | «Existe la partición nacional / importado» | Todo se compra a terceros nacionales |
| 2026-10-28 | «La capacidad está al 47 % y es holgada» | Se comparó la carga de 34 productos contra la capacidad de 2 turnos; el destilador es compartido y con 3 turnos puede estar saturado |
| 2026-10-27 | «`COSTOU` sirve como costo de insumo» | 25 insumos inconsistentes entre recetas; 25 fuera del rango de proveedores |
| 2026-10-28 | «El cambio de producto consume 31 % de la capacidad» | Se contó un cambio por producto demandado (25/sem) en vez de por lote (5.1/sem) |

**Lección metodológica a conservar** (es resultado publicable, pertenece a la Específica 3):
*los parámetros maestros del ERP no son datos, son declaraciones.* La calibración debe hacerse
contra el registro transaccional, y la arquitectura del gemelo debe incluir validación continua
de parámetros maestros como componente, no como paso previo.
