# Plan de verificación computacional del modelo LP integrado
**26 de octubre de 2026** · Elemento 2.1 del protocolo · Adaptado a tu formulación

---

## Lo que leí

LP continuo, horizonte táctico $T = 52$ semanas. Cadena multi-escalón con 11 insumos (1 solvente, 5 componentes nacionales, 5 importados), 5 semiterminados, 10 SKUs, cuatro localizaciones con inventario ($A_P$, $B_G$, $B_S$, $B_T$) y tres zonas de mercado con plazos de cobro de 15, 30 y 45 días. Objetivo: maximizar EVA acumulado = NOPAT − cargo de capital − penalización por demanda perdida. Dos extensiones declaradas sobre Hahn y Kuhn (2011): el término de WIP en los activos corrientes y las cuentas por cobrar diferenciadas por zona. Comparador propio: el enfoque secuencial SEQ-1 / SEQ-2.

**Por qué esto es fácil de verificar.** Un LP no tiene heurísticas ni aleatoriedad: dadas las mismas entradas, da el mismo óptimo. Y tus ecuaciones (3) a (7) no son restricciones de comportamiento sino **identidades contables**, lo que permite una prueba demoledora y casi gratis: recalcular el EVA desde las variables primitivas, fuera del modelo, y comprobar que coincide con el valor objetivo. Eso se llama auditoría post-solución y es el núcleo de la prueba 2 de abajo.

---

## Antes de empezar: una pregunta que bloquea

**¿En qué está implementado?** La formulación es un documento LaTeX, no código. Dime el entorno —Python con Pyomo o PuLP, GAMS, AMPL, CPLEX/Gurobi directo, Excel con Solver— y te escribo los scripts de las pruebas 1 a 6. Si está en Python, te los dejo corriendo; si está en GAMS o AMPL, te doy el código y tú lo pegas.

**Y un chequeo de 10 minutos para mañana:** corre el modelo una vez tal como está y dime tres cosas: si resuelve, el valor del objetivo, y cuánto tardó. Si no resuelve hoy, el plan cambia y hay que saberlo el día 27, no el 29.

---

## Las ocho pruebas, en orden de valor

### Prueba 1 — Instancias de solución conocida

**Qué es.** Construir casos tan pequeños que el óptimo se calcule a mano, y comprobar que el modelo lo reproduce.

**Casos concretos para tu modelo:**

| Caso | Configuración | Óptimo esperado |
|---|---|---|
| 1a | Demanda cero en todos los $q, z, t$ | Todas las variables físicas en cero. EVA = $-\sum_t (fc_t(1-z)) - \sum_t (fa + CA_{t-1}) i^{wacc}$. Solo costos fijos y cargo de capital sobre los activos fijos y las posiciones iniciales |
| 1b | 1 insumo, 1 semiterminado, 1 SKU, 1 zona, $T = 2$, capacidad holgada | Calculable a mano en 20 minutos. Comprueba los cinco balances de masa, el lead time y las cinco identidades contables simultáneamente |
| 1c | $p^{lost} = 0$ y margen de contribución negativo | El modelo debe dejar de producir y servir demanda cero. Si produce, hay un signo mal puesto en (7) o en el objetivo |
| 1d | $p^{lost}$ muy alto, capacidad holgada | Demanda servida al 100 %, $LS_{qzt} = 0$ en todo $q,z,t$ |
| 1e | $i^{wacc} = 0$ | El modelo debe dejar de penalizar inventario. Compara contra 1d: el inventario promedio debe subir. Si no cambia nada, el cargo de capital no está actuando |

**Evidencia que produces.** Una tabla de cinco filas: caso, valor objetivo esperado, valor obtenido, diferencia. **Tiempo: medio día.**

### Prueba 2 — Auditoría de las identidades contables

**Qué es.** La más valiosa y la que casi nadie reporta. Con la solución óptima en la mano, recalcular **fuera del modelo** cada identidad y comprobar que cierra.

**Qué recalculas, ecuación por ecuación:**

1. $NS_t$ desde $Y_{q,B_T,z,t} \cdot v_q$ — ecuación (5)
2. $VCO_t$ sumando sus cinco componentes — ecuación (7)
3. $IC_t$ desde las diferencias de inventario y WIP — ecuación (6)
4. $TCM_t = NS_t - VCO_t + IC_t$ — ecuación (3)
5. $CA_t$ desde sus seis rubros — ecuación (4)
6. **El EVA completo** desde la ecuación (2), con los $TCM_t$ y $CA_t$ recalculados

**El criterio.** La diferencia entre el EVA recalculado y el valor objetivo del solver debe ser cero salvo error de punto flotante: menos de $10^{-6}$ en términos relativos. Si no cierra, tienes un error de formulación o de implementación, y la auditoría te dice **en qué ecuación**, porque cada identidad se comprueba por separado.

**Y una comprobación extra que no cuesta nada:** el balance de caja (21) y los balances de masa (8)-(12) también se recalculan igual. Para cada nodo y cada período: entradas − salidas − Δinventario = 0.

**Evidencia que produces.** Una tabla con 52 filas por identidad, o el máximo error absoluto y relativo por identidad, que es más compacto. **Tiempo: medio día si el modelo exporta la solución a CSV.**

### Prueba 3 — Condiciones extremas

**Qué es.** Llevar los parámetros a los límites y comprobar que el modelo se comporta como debe o se declara infactible por la razón correcta.

| Escenario | Comportamiento correcto |
|---|---|
| Capacidad de transformación primaria = 0 | Infactible, o factible con $LS = d$ en todo. Si da factible con producción positiva, (15) no está actuando |
| Capacidad de almacenamiento = 0 | Fuerza producción justo a tiempo o infactibilidad, según (16) |
| Línea de crédito máxima = 0 y caja inicial mínima | Debe restringir la operación. Si el EVA no cambia respecto del caso base, la restricción (26) no está atada |
| Caja mínima necesaria muy alta | Debe forzar deuda o liquidación de $FI$. Si no, (25) está floja |
| $\tau_z$ de las tres zonas = 0 (cobro inmediato) | $AR_{zt} = 0$, $CA_t$ baja, EVA sube. La dirección es la prueba |
| $\tau_z$ muy largo | $AR$ crece, cargo de capital crece, EVA baja |
| Demanda = capacidad exacta | Solución única y al límite. Útil para detectar degeneración |

**Evidencia.** Tabla de escenario, resultado esperado, resultado obtenido. **Tiempo: medio día.**

### Prueba 4 — El efecto de borde de fin de horizonte, por el canal del WIP

Hay una asimetría temporal en tus ecuaciones (2), (3) y (6):

- El cargo de capital del período $t$ se calcula sobre $CA_{t-1}$, no sobre $CA_t$. Es decir, $CA_T$ **nunca recibe cargo**.
- $IC_t$ entra sumando en $TCM_t$: inventariar **aumenta** el margen del período.

**La buena noticia: el canal del inventario está cerrado.** Tu ecuación (13) fija $I_{\pi l T} = I^T_{\pi l}$ como **igualdad**, así que el modelo no puede acumular inventario en la última semana. Eso ya está bien resuelto y conviene decirlo en el artículo, porque es una decisión de diseño que la mayoría no explicita.

**Pero queda un canal abierto: $W_{pT}$.** La ecuación (13) fija $W_{p,0} = W^0_p$ y los lanzamientos previos $X_{p,s}$ para $s = 1-\delta, \dots, 0$ —lo cual, dicho sea de paso, resuelve bien el arranque del lead time—, pero **no fija $W_{p,T}$**. Con $\delta = 1$, lo lanzado en $T$ se libera en $T+1$, que no existe: queda en $W_{pT}$, entra en $CA_T$ (nunca cargado) y entra sumando en $IC_T$.

**Qué comprobar, concretamente:**
1. Grafica $\sum_p W_{pt} \cdot e^W_p$ contra $t$. ¿Hay un salto en $t = T$?
2. Mira $X_{pT}$. ¿Es anormalmente alto respecto de $X_{p,T-1}$?
3. La clave está en **qué incluye $e^W_p$**. Si valora solo el costo de los insumos consumidos y no el costo de transformación $kx_p \cdot cd_{A_P}$, entonces lanzar en $T$ cuesta la transformación sin compensación en $IC_T$ y el modelo no lo hará. Si $e^W_p$ incluye la transformación, el efecto en $TCM_T$ se cancela y lo único que queda es un $CA_T$ inflado que nunca se cobra: neutral en el objetivo, pero contamina cualquier indicador que leas de la última semana.

**Si aparece, dos salidas limpias:** fijar también $W_{p,T} = W^T_p$ por simetría con (13), o reportar los indicadores sobre las primeras 51 semanas declarando el borde. La primera es una línea de código. **Tiempo: 1 hora.**

### Prueba 5 — No arbitraje financiero

**Otro hallazgo potencial.** Tu modelo tiene tres tasas: el WACC semanal $i^{wacc}$ del cargo de capital, el rendimiento de las inversiones financieras $FI$ y el costo de la deuda de corto plazo $DS$. Si esas tres no satisfacen una condición de no arbitraje, el LP encuentra dinero gratis.

**Los dos casos a comprobar:**
- Si el **rendimiento de $FI$ supera el costo de $DS$**, el modelo tomará deuda para invertir, sin límite salvo la línea de crédito. Síntoma: $DS_t$ y $FI_t$ los dos pegados a su cota superior sin relación con la operación.
- $FI$ está dentro de $CA_t$ en la ecuación (4), así que mantener $FI$ **paga cargo de capital**. Si el rendimiento de $FI$ es menor que $i^{wacc}$, el modelo nunca tendrá $FI$ por encima de lo forzado. Comprueba si eso es lo que quieres: si $FI$ sale siempre en cero, el mecanismo de inversión financiera no está haciendo nada y conviene decirlo o quitarlo.

**La prueba.** Grafica $DS_t$ y $FI_t$ contra $t$ y comprueba que no están los dos positivos simultáneamente sin motivo operativo. Y escribe explícitamente la relación de orden entre las tres tasas que usas, con su fuente. **Esto es además el elemento 4.2 del protocolo**, el cero absoluto del corpus: ningún artículo de los 65 cita la fuente de sus parámetros financieros. **Tiempo: 1 hora.**

### Prueba 6 — Monotonías y direcciones

**Qué es.** Comprobar que el modelo reacciona en la dirección correcta. Barato y convincente.

| Si aumentas… | El EVA debe… | Y además |
|---|---|---|
| el precio de venta $v_q$ | subir | la demanda servida no baja |
| el costo de insumo $e_r$ | bajar | — |
| $i^{wacc}$ | bajar | el inventario promedio baja |
| el lead time $\delta$ | bajar | el WIP sube |
| $\tau_z$ de una zona | bajar | $AR_z$ de esa zona sube |
| $p^{lost}$ | bajar o igual | $LS$ baja o se mantiene |

Cada fila son dos corridas. Doce corridas en total y la tabla sale sola. **Si alguna fila va al revés, hay un signo invertido y la has encontrado.** **Tiempo: medio día, y se solapa con la sensibilidad del elemento 2.2.**

### Prueba 7 — Precios sombra con sentido

**Qué es.** Un LP te da duales gratis. Los duales de las restricciones de capacidad (15) y (16) y de la demanda (17) tienen interpretación económica directa.

**Lo que compruebas:** que el dual de la capacidad de transformación primaria sea ≥ 0 cuando la restricción está activa y cero cuando está holgada, y que su magnitud sea plausible como «valor marginal de una unidad más de capacidad». Si el dual de una capacidad activa es absurdo —órdenes de magnitud fuera del margen de contribución— hay un problema de escalado de unidades.

**Por qué vale la pena.** Esta prueba, además de verificar, te da material para la discusión gerencial del capítulo. **Tiempo: 2 horas.**

### Prueba 8 — Degeneración e independencia del solver

**Qué es.** Resolver el mismo modelo con dos solvers distintos, o con dos configuraciones del mismo (simplex primal, dual, barrera) y comprobar que el **valor objetivo** coincide.

**Lo importante que hay que saber y declarar:** en un LP con identidades contables es muy probable que haya **soluciones óptimas múltiples** —distintos planes físicos con el mismo EVA—. Si los dos solvers dan el mismo objetivo pero planes distintos, eso **no es un error**: es degeneración, y hay que reportarla, porque significa que las decisiones específicas que recomiendas no son únicas. Esa es una afirmación honesta que casi ningún artículo del corpus hace.

**Evidencia.** Valor objetivo con dos métodos, diferencia relativa, y una frase sobre unicidad. **Tiempo: 2 horas.**

---

## Lo que esto te produce para el artículo

Un apartado de verificación con cinco tablas:

1. Instancias de solución conocida: esperado contra obtenido (prueba 1)
2. Error máximo por identidad contable (prueba 2)
3. Comportamiento en condiciones extremas (prueba 3)
4. Direcciones de respuesta (prueba 6)
5. Valor objetivo por método de solución, y declaración de unicidad (prueba 8)

Más tres párrafos: el efecto de borde de fin de horizonte y cómo lo tratas (prueba 4), la relación entre las tres tasas y su fuente (prueba 5), y la lectura de los precios sombra (prueba 7).

**En el corpus, E1 «verificación computacional» = 32 de 65 (49.2 %)**, y de esos la mayoría reporta una frase, no una tabla. Con esto quedas en la cola alta de ese 49 %.

---

## Reparto en tus dos horas diarias

| Día | Tú | Yo |
|---|---|---|
| **Mar 27** | 10 min: correr el modelo tal como está y decirme entorno, objetivo y tiempo | Escribo los scripts de las pruebas 1 y 2 para tu entorno |
| **Jue 29** | 2 h: ejecutar pruebas 1 y 2 | Reviso los resultados y redacto el apartado |
| **Vie 30** | 2 h: ejecutar pruebas 3, 4 y 5 | Tablas y los tres párrafos |
| **Lun 2** | se solapa con 2.2: pruebas 6 y 7 | Tabla de sensibilidad conjunta |
| **Mié 4** | 30 min: prueba 8 | Declaración de unicidad |

Las pruebas 4 y 5 son las que pueden darte un hallazgo de formulación. Si alguna sale mal, mejor saberlo el 30 de octubre que el 9 de noviembre.

---

## Dos observaciones sobre la formulación, no sobre la verificación

**1. El comparador SEQ es una ventaja que conviene explotar.** SEQ-1/SEQ-2 te da exactamente lo que el 21.5 % del corpus construye como «benchmark ideal», pero con una diferencia: el secuencial **es** lo que hacen las empresas en la práctica, planear físico primero y financiero después. Si lo presentas así —«comparamos contra la práctica secuencial habitual», no «contra un benchmark»— te mueves del 21.5 % al 15.4 % de artículos que comparan contra la práctica actual, que es la comparación creíble. Es un cambio de encuadre, no de código.

**2. El término de WIP es tu contribución más concreta y conviene aislarla.** Tienes una extensión declarada sobre Hahn y Kuhn: $\sum_p W_{pt} e^W_p$ dentro de $CA_t$. Corre el modelo **con y sin ese término** y reporta la diferencia en EVA y en el plan. Si la diferencia es apreciable, tienes una contribución medida, no argumentada. Si es despreciable, también conviene saberlo antes de escribir que es relevante. Son dos corridas.

**3. Y hay un detalle que afecta justamente a esa contribución.** Tu nota sobre $\delta$ dice que el proceso real dura unos 10 días ($\approx 1.43$ semanas) y que adoptas $\delta = 1$ redondeando a la baja, «lo cual implica que el capital en WIP se subestima ligeramente».

Es decir: **tu contribución principal se mide con un parámetro que la sesga hacia abajo.** Eso no está mal, está declarado, pero deja sobre la mesa la pregunta obvia del revisor: *¿cuánto cambia si $\delta = 2$?*

Son dos corridas más y te dan tres cosas de una vez:
- una **cota** para tu contribución, en lugar de una estimación puntual sesgada;
- el elemento **2.4** del protocolo resuelto con el mejor material posible: una limitación cuyo efecto no solo declaras, sino que **cuantificas** —«con $\delta = 2$ el capital en WIP sube un X % y el EVA baja un Y %»—, que es exactamente lo que el 61.5 % del corpus no hace;
- un argumento para pasar a $\delta$ fraccionario en el futuro, si la diferencia resulta grande.

Si solo puedes hacer dos corridas extra en toda la quincena, que sean estas.
