::: center
**Formulación del Modelo de Programación Lineal**\
**para la Planificación Integrada Físico-Financiera**\
**de una Cadena de Suministro Multi-Escalón**\
Documento técnico de referencia --- Versión 2.5\
2026-11-02
:::

# Descripción general del modelo {#sec:descripcion}

El presente documento formula un programa lineal continuo que integra
dos dominios de decisión bajo un horizonte de planeación táctica de
$T = 52$ semanas:

1.  **Dominio físico:** Decisiones de aprovisionamiento, transformación
    primaria, transformación secundaria, transporte inter-planta,
    gestión de inventarios y satisfacción de demanda.

2.  **Dominio financiero:** Decisiones de gestión de caja, deuda de
    corto plazo, inversiones financieras de corto plazo y cuentas por
    cobrar diferenciadas por plazo según zona de mercado.

El sistema considerado es una cadena multi-escalón, multi-producto y
multi-período, operada por una empresa de manufactura por lotes, bajo
política de fabricación contra inventario (*make-to-stock*), con dos
etapas de transformación separadas por un arco logístico inter-planta y
un nodo de trabajo en proceso (WIP):

1.  **Transformación primaria** en la Instalación A, donde las materias
    primas se procesan por lotes con un período de retención de calidad
    antes de liberar el semiterminado.

2.  **Transformación secundaria** en la Instalación B, donde los
    semiterminados liberados se convierten en presentaciones finales
    (SKUs) de dos volúmenes: 500 ml y 1 L.

El nodo WIP captura la inmovilización de capital durante el período que
transcurre entre el lanzamiento a transformación y la liberación del
semiterminado al almacén de semiterminados liberados ($B_{wip}$). Los
semiterminados liberados permanecen en $B_{wip}$ hasta que se embarcan
hacia la Instalación B vía el arco inter-planta. Esta inmovilización es
relevante para el cálculo del cargo de capital en la función objetivo.

La función objetivo maximiza el Valor Económico Agregado (EVA) acumulado
a lo largo de las 52 semanas, formulado en estricto apego a la
definición de Stewart (1991): el cargo de capital se aplica sobre los
**activos operativos netos** (inventarios, WIP, cuentas por cobrar,
menos cuentas por pagar) y sobre los activos fijos operativos, ambos
medidos al **saldo de apertura** de cada período. Las posiciones
financieras de corto plazo (caja, inversiones financieras y deuda
bancaria) quedan fuera de esa base de capital y se valoran de forma
explícita mediante un término propio de costo financiero neto, de modo
que el plan financiero incide directamente sobre el valor y no sólo
sobre la factibilidad de caja. Los activos fijos operativos siguen una
rampa descendente de depreciación, cuya carga se reconoce de manera
consistente tanto en la base de capital como en la utilidad gravable. El
horizonte de cargos se extiende un período más allá del horizonte de
decisiones ($T+1$) para valorar las posiciones que el plan deja abiertas
al cierre. Adicionalmente, se formula un enfoque secuencial de dos pasos
(SEQ-1 / SEQ-2) como punto de comparación.

El modelo incorpora cinco simplificaciones que fueron identificadas y
evaluadas de forma explícita, y que se retienen de manera deliberada en
esta versión. No son omisiones: cada una se documenta en la
[15](#sec:supuestos_declarados){reference-type="ref+Label"
reference="sec:supuestos_declarados"} con su enunciado formal, la
dirección y magnitud del sesgo que introduce, la condición verificable
bajo la cual permanece admisible y la extensión disponible para
relajarla. El lector que busque el alcance exacto de la formulación debe
consultar esa sección antes de interpretar cualquier resultado.

<figure id="fig:red_cadena" data-latex-placement="htbp">

<figcaption>Topología de la red de la cadena de suministro. Los nodos de
inventario (<span
class="math inline"><em>B</em><sub><em>P</em></sub></span>, <span
class="math inline"><em>B</em><sub><em>G</em></sub></span>, <span
class="math inline"><em>B</em><sub><em>w</em><em>i</em><em>p</em></sub></span>,
<span class="math inline"><em>B</em><sub><em>S</em></sub></span>, <span
class="math inline"><em>B</em><sub><em>T</em></sub></span>) son puntos
de acumulación de material con capacidad acotada; los nodos de
transformación (Nodo 1 y Nodo 2) representan las dos etapas productivas;
el nodo WIP captura la inmovilización de capital durante el período de
retención de calidad (<span class="math inline"><em>δ</em> = 1</span>
semana). Las materias primas importadas (<span
class="math inline"><em>R</em><sup><em>I</em></sup></span>) se reciben
exclusivamente en <span
class="math inline"><em>B</em><sub><em>G</em></sub></span> y alcanzan el
punto de consumo en la Instalación A a través del arco de transferencia
interna <span
class="math inline"><em>B</em><sub><em>G</em></sub> → <em>B</em><sub><em>P</em></sub></span>,
que opera sobre <span
class="math inline"><em>R</em><sup><em>N</em></sup> ∪ <em>R</em><sup><em>I</em></sup></span>
(ver <a href="#tab:auditoria_pc" data-reference-type="ref+Label"
data-reference="tab:auditoria_pc">2</a>). El material liberado del WIP
se acumula en <span
class="math inline"><em>B</em><sub><em>w</em><em>i</em><em>p</em></sub></span>
hasta su embarque a la Instalación B vía el arco inter-planta. Las
Instalaciones A y B se encuentran separadas geográficamente por 100 km;
el tiempo de tránsito (<span class="math inline">≈ 1.5</span> h) es
despreciable frente al período semanal, por lo que el arco inter-planta
se modela como instantáneo (ver <a href="#sec:supuestos_operativos"
data-reference-type="ref+Label"
data-reference="sec:supuestos_operativos">5.2</a>). Los tres canales de
mercado operan con plazos de cobro diferenciados (<span
class="math inline"><em>τ</em><sub>1</sub> &lt; <em>τ</em><sub>2</sub> &lt; <em>τ</em><sub>3</sub></span>).</figcaption>
</figure>

# Conjuntos e índices {#sec:conjuntos}

Los conjuntos del modelo se organizan en tres niveles de producto, tres
tipos de localización, los conjuntos derivados de combinaciones válidas
y dos listas de materiales (BOM) que reflejan la doble transformación de
la cadena.

::: xltabular
l X

\
**Conjunto** & **Descripción**\

**Conjunto** & **Descripción**\

\
$R$ & Insumos (materias primas), $|R| = 11$\
$R^E$ & Subconjunto de MP tipo A (solvente), $R^E \subset R$,
$|R^E| = 1$\
$R^N$ & Subconjunto de MP tipo B nacionales (componentes sólidos
nacionales), $R^N \subset R$, $|R^N| = 5$\
$R^I$ & Subconjunto de MP tipo B importadas (componentes sólidos
importados), $R^I \subset R$, $|R^I| = 5$\
$R^{(N)}$ & Clase de pago nacional: $R^{(N)} = R^N \cup R^E$, con plazo
$\tau^{pay}_N$, $|R^{(N)}| = 6$\
$R^{(I)}$ & Clase de pago importada: $R^{(I)} = R^I$, con plazo
$\tau^{pay}_I$, $|R^{(I)}| = 5$\
$K$ & Índice de clase de pago a proveedores, $K = \{N, I\}$.
$\{R^{(N)}, R^{(I)}\}$ constituye una **partición** de $R$\
$P$ & Semiterminados, $|P| = 5$ (familias $p_1, \dots, p_5$)\
$Q$ & Presentaciones finales (SKUs terminados), $|Q| = 10$: dos por
semiterminado (500 ml y 1 L)\
$\Pi$ & Conjunto unión de productos, $\Pi = R \cup P \cup Q$,
$|\Pi| = 26$\
\
$L^S$ & Proveedores externos (uno por insumo), $|L^S| = 11$\
$L^{Op}$ & Localizaciones operativas con inventario,
$L^{Op} = \{B_P, B_G, B_{wip}, B_S, B_T\}$\
$L^M$ & Zonas de mercado, $L^M = \{z^{loc}, z^{for}, z^{exp}\}$ con
plazos $\tau_{z^{loc}} = 2$, $\tau_{z^{for}} = 4$, $\tau_{z^{exp}} = 6$
semanas\
\
$PL^S$ & Combinaciones válidas insumo--localización receptora,
$|PL^S| = 16$ (desglose en
[2](#tab:auditoria_pc){reference-type="ref+Label"
reference="tab:auditoria_pc"})\
$PL^{Op}$ & Combinaciones válidas producto--localización para
inventario, $PL^{Op} \subset \Pi \times L^{Op}$, $|PL^{Op}| = 41$
(desglose en [2](#tab:auditoria_pc){reference-type="ref+Label"
reference="tab:auditoria_pc"})\
$PL^M$ & Combinaciones válidas presentación--zona para ventas,
$PL^M = Q \times L^M$, $|PL^M| = 30$\
$\mathit{TC}$ & Conexiones de transporte entre localizaciones $(l, j)$,
$|\mathit{TC}| = 21$\
$PC$ & Combinaciones válidas producto--conexión de transporte
$(\pi, (l, j))$, $|PC| = 61$. Incluye el arco de transferencia interna
$B_G \to B_P$ para los **diez** insumos $R^N \cup R^I$ (ver
[2](#tab:auditoria_pc){reference-type="ref+Label"
reference="tab:auditoria_pc"})\
\
$\mathit{BOM}^1$ & Recetas nivel 1: pares $(p, r)$ con $p \in P$,
$r \in R$, $|\mathit{BOM}^1| = 27$\
$\mathit{BOM}^2$ & Recetas nivel 2: pares $(q, p)$ con $q \in Q$,
$p \in P$, $|\mathit{BOM}^2| = 10$. Coeficientes diferenciados según el
volumen de la presentación: $a^2_{qp} = 0.5$ para 500 ml;
$a^2_{qp} = 1.0$ para 1 L\
\
\
$t$ & Índice de período (semana), $t = 1, \dots, T$ con $T = 52$\
:::

::: {#tab:auditoria_pc}
+-------------------+-----------------+-----------------------+----------------+
| **Arco / nodo**   | **Arcos en      | **Productos por       | **Pares en     |
|                   | $\mathit{TC}$** | arco**                | $PC$**         |
+:==================+:===============:+:=====================:+:==============:+
| *Arcos de aprovisionamiento externo* ($l^S \to l$, equivalentes a $PL^S$)    |
+-------------------+-----------------+-----------------------+----------------+
| $R^E \to B_P$     | 1               | 1                     | 1              |
| (solvente,        |                 |                       |                |
| exclusivo)        |                 |                       |                |
+-------------------+-----------------+-----------------------+----------------+
| $R^N \to B_P$     | 5               | 1                     | 5              |
| (nacional,        |                 |                       |                |
| directo)          |                 |                       |                |
+-------------------+-----------------+-----------------------+----------------+
| $R^N \to B_G$     | 5               | 1                     | 5              |
| (nacional, vía    |                 |                       |                |
| almacén general)  |                 |                       |                |
+-------------------+-----------------+-----------------------+----------------+
| $R^I \to B_G$     | 5               | 1                     | 5              |
| (importado,       |                 |                       |                |
| exclusivo)        |                 |                       |                |
+-------------------+-----------------+-----------------------+----------------+
| *Arcos operativos internos*                                                  |
+-------------------+-----------------+-----------------------+----------------+
| $B_G \to B_P$     | 1               | $|R^N \cup R^I| = 10$ | 10             |
| (transferencia    |                 |                       |                |
| interna)          |                 |                       |                |
+-------------------+-----------------+-----------------------+----------------+
| $B_{wip} \to B_S$ | 1               | $|P| = 5$             | 5              |
| (inter-planta)    |                 |                       |                |
+-------------------+-----------------+-----------------------+----------------+
| $B_T \to z$,      | 3               | $|Q| = 10$            | 30             |
| $z \in L^M$       |                 |                       |                |
| (despacho a       |                 |                       |                |
| mercado)          |                 |                       |                |
+-------------------+-----------------+-----------------------+----------------+
| **Total**         | **21**          | ---                   | **61**         |
+-------------------+-----------------+-----------------------+----------------+

: Auditoría de los conjuntos derivados de localización y transporte. El
desglose hace explícito que el arco de transferencia interna
$B_G \to B_P$ opera sobre los diez insumos de tipo B (nacionales *e*
importados), condición necesaria para que el $\mathit{BOM}^1$ pueda
consumir $R^I$ en el Nodo 1.
:::

::: {#tab:auditoria_pc}
  **Nodo de inventario**                         **Productos almacenados**
  ------------------------ ----------------------------------------------------------------------
  $B_P$                     $|R| = 11$ (los once insumos, incluidos los importados transferidos)
  $B_G$                                            $|R^N \cup R^I| = 10$
  $B_{wip}$                                              $|P| = 5$
  $B_S$                                                  $|P| = 5$
  $B_T$                                                  $|Q| = 10$
  **Total** $|PL^{Op}|$                                    **41**

  : Auditoría de los conjuntos derivados de localización y transporte.
  El desglose hace explícito que el arco de transferencia interna
  $B_G \to B_P$ opera sobre los diez insumos de tipo B (nacionales *e*
  importados), condición necesaria para que el $\mathit{BOM}^1$ pueda
  consumir $R^I$ en el Nodo 1.
:::

::: {#tab:bom1_detalle}
  ---------------------- --- --- --- --- --- --- --- --- --- --- ---
  2-2 (lr)3-7 (lr)8-12                                           
  $p_1$                                                          
  $p_2$                                                          
  $p_3$                                                          
  $p_4$                                                          
  $p_5$                                                          
  *Insumos usados*       5   3   3   2   2   2   3   2   2   2   1
  ---------------------- --- --- --- --- --- --- --- --- --- --- ---

  : Estructura detallada de $\mathit{BOM}^1$: coeficientes técnicos
  $a^1_{pr}$ de la transformación primaria.
:::

*Nota:* Los semiterminados $p_3$, $p_4$ y $p_5$ consumen insumos
importados ($R^I$). Puesto que $R^I$ se recibe únicamente en $B_G$ y el
consumo ocurre contra el inventario de $B_P$
(ec. [\[eq:balance_bp\]](#eq:balance_bp){reference-type="ref"
reference="eq:balance_bp"}), el arco $B_G \to B_P$ debe estar definido
para $R^I$; de lo contrario el modelo resulta infactible.

::: {#tab:bom2_detalle}
+-------------------+------------------+-------------+------------------+
| **Semiterminado** | **Presentación** | **Volumen** | **Coeficiente    |
|                   |                  |             | $a^2_{qp}$**     |
+:==================+:=================+:============+:=================+
| $p_1$             | $q_1$            | 500 ml      | 0.5              |
|                   +------------------+-------------+------------------+
|                   | $q_2$            | 1 L         | 1.0              |
+-------------------+------------------+-------------+------------------+
| $p_2$             | $q_3$            | 500 ml      | 0.5              |
|                   +------------------+-------------+------------------+
|                   | $q_4$            | 1 L         | 1.0              |
+-------------------+------------------+-------------+------------------+
| $p_3$             | $q_5$            | 500 ml      | 0.5              |
|                   +------------------+-------------+------------------+
|                   | $q_6$            | 1 L         | 1.0              |
+-------------------+------------------+-------------+------------------+
| $p_4$             | $q_7$            | 500 ml      | 0.5              |
|                   +------------------+-------------+------------------+
|                   | $q_8$            | 1 L         | 1.0              |
+-------------------+------------------+-------------+------------------+
| $p_5$             | $q_9$            | 500 ml      | 0.5              |
|                   +------------------+-------------+------------------+
|                   | $q_{10}$         | 1 L         | 1.0              |
+-------------------+------------------+-------------+------------------+

: Estructura detallada de $\mathit{BOM}^2$: relación
semiterminado--presentación en la transformación secundaria. Cada
semiterminado genera dos presentaciones diferenciadas por volumen; los
coeficientes reflejan la cantidad de semiterminado (en unidades de
granel) requerida por unidad de presentación.
:::

**Interpretación de los coeficientes $a^2_{qp}$:** El semiterminado $p$
se mide en unidades de granel. La presentación de 500 ml requiere $0.5$
unidades de granel, mientras que la de 1 L requiere $1.0$ unidad. Esto
implica que los precios de venta ($v_q$), los costos de transformación
secundaria ($cc_q$) y las demandas ($d_{qzt}$) se definen por *unidad de
presentación* (botella de 500 ml o de 1 L, respectivamente), no por
unidad de volumen.

# Variables de decisión {#sec:variables}

## Variables del dominio físico {#sec:vars_fisicas}

::: xltabular
l X l

\
**Variable** & **Descripción** & **Indexación**\

**Variable** & **Descripción** & **Indexación**\

$I_{\pi l t}$ & Inventario del producto $\pi$ en la localización $l$ al
final del período $t$ & $(\pi, l) \in PL^{Op},\; t$\
$X_{pt}$ & Cantidad de semiterminado $p$ lanzada a transformación
primaria en el período $t$ & $p \in P,\; t$\
$W_{pt}$ & Trabajo en proceso (WIP) del semiterminado $p$ al final del
período $t$ & $p \in P,\; t$\
$Z_{qt}$ & Cantidad de presentación $q$ producida en transformación
secundaria en el período $t$ & $q \in Q,\; t$\
$Y_{\pi l j t}$ & Cantidad transportada del producto $\pi$ desde $l$
hasta $j$ en el período $t$ & $(\pi, (l, j)) \in PC,\; t$\
$LS_{qzt}$ & Demanda insatisfecha de la presentación $q$ en la zona $z$
en el período $t$ & $q \in Q,\; z \in L^M,\; t$\
$\Delta^+_{pt}$ & Incremento en la producción del semiterminado $p$
respecto al período anterior (variable de regularización) &
$p \in P,\; t$\
$\Delta^-_{pt}$ & Decremento en la producción del semiterminado $p$
respecto al período anterior (variable de regularización) &
$p \in P,\; t$\
:::

**Nota sobre $X_{pt}$:** Representa el *lanzamiento* a transformación
primaria, no la salida liberada. La salida se produce tras $\delta$
períodos de retención.

**Nota sobre $\Delta^+_{pt}$ y $\Delta^-_{pt}$:** Estas variables
descomponen el cambio absoluto en producción $|X_{pt} - X_{p,t-1}|$ en
sus componentes positiva y negativa, permitiendo la regularización en un
entorno LP sin necesidad de variables enteras (ver
[8.11](#sec:regularizacion){reference-type="ref+Label"
reference="sec:regularizacion"}).

**Convención sobre $Y_{\pi l j t}$:** La variable existe únicamente para
las combinaciones $(\pi, (l,j)) \in PC$
([2](#tab:auditoria_pc){reference-type="ref+Label"
reference="tab:auditoria_pc"}). En cualquier expresión donde aparezca un
índice fuera de $PC$, el término se toma idénticamente igual a cero y no
genera columna en la matriz del LP.

## Variables del dominio financiero {#sec:vars_financieras}

::: xltabular
l X l

\
**Variable** & **Descripción** & **Indexación**\

$FI_t$ & Posición en inversiones financieras de corto plazo al final de
$t$ & $t$\
$AR_{zt}$ & Saldo de cuentas por cobrar de la zona $z$ al final de $t$ &
$z \in L^M,\; t$\
$C_t$ & Posición de caja al final de $t$ & $t$\
$DS_t$ & Saldo de deuda de corto plazo al final de $t$ & $t$\
$AP_t$ & Saldo de cuentas por pagar al final de $t$ & $t$\
:::

# Variables auxiliares {#sec:vars_auxiliares}

Las variables auxiliares son decisiones dependientes que el modelo
calcula a partir de las variables de decisión. Simplifican la lectura de
la función objetivo y las restricciones sin añadir grados de libertad.

::: {#tab:vars_aux}
  **Variable**   **Descripción**                                                                                                                         **Indexación**
  -------------- --------------------------------------------------------------------------------------------------------------------------------------- ---------------------
  $TCM_t$        Margen total de contribución del período                                                                                                $t$
  $NS_t$         Ventas netas del período                                                                                                                $t$
  $VCO_t$        Costos variables de operación del período                                                                                               $t$
  $IC_t$         Cambio en inventario al final de $t$ (valorado al costo)                                                                                $t$
  $NOA_t$        Activos operativos netos al final de $t$ (base de capital para el EVA)                                                                  $t = 0, \dots, T$
  $EBIT_t$       Utilidad operativa antes de impuestos del período                                                                                       $t$
  $NIE_t$        Gasto financiero neto de corto plazo devengado en $t$ (intereses de deuda menos rendimiento de inversiones, sobre saldos de apertura)   $t = 1, \dots, T+1$
  $TAX_t$        Impuesto sobre la renta del período, gravado sobre $EBIT_t - NIE_t$ (endógeno)                                                          $t$
  $OCF_t$        Flujo de caja de operaciones en $t$                                                                                                     $t$
  $OM_t$         Flujo de caja por gestión de cuentas abiertas en $t$                                                                                    $t$
  $FM_t$         Flujo de caja por gestión financiera de corto plazo en $t$                                                                              $t$

  : Variables auxiliares contables
:::

**Nota sobre $NOA_t$ vs. $CA_t$ de la versión anterior:** Siguiendo a
Stewart (1991), el capital empleado para el cálculo del EVA debe incluir
únicamente los activos **operativos** netos de pasivos corrientes no
financieros (cuentas por pagar), excluyendo las posiciones financieras
($FI_t$, $C_t$, $DS_t$) que constituyen decisiones de tesorería. Esto
evita penalizar con el WACC las posiciones de caja e inversiones cuyo
rendimiento se captura ya en la ecuación de gestión financiera, y
elimina el doble cargo sobre la deuda de corto plazo.

## Dominio de las variables auxiliares {#sec:dominio_aux}

A diferencia de las variables de decisión físicas y financieras, que
están acotadas inferiormente en cero por las
ecuaciones ([\[eq:noneg_fisicas\]](#eq:noneg_fisicas){reference-type="ref"
reference="eq:noneg_fisicas"})
y ([\[eq:noneg_financieras\]](#eq:noneg_financieras){reference-type="ref"
reference="eq:noneg_financieras"}), **ocho de las once variables
auxiliares admiten valores negativos** y deben declararse explícitamente
como libres de signo. Esta declaración no es redundante: los modeladores
algebraicos de uso corriente (Pyomo, GAMS, JuMP, AMPL) instancian
variables con dominio no negativo por omisión, de modo que la ausencia
de esta declaración produce un modelo infactible o, en el peor de los
casos, silenciosamente incorrecto.

**Variables libres de signo:** $$\begin{equation}
\label{eq:dominio_libre}
\tag{D1}
\begin{aligned}
    TCM_t,\; IC_t,\; EBIT_t,\; TAX_t,\; OM_t,\; FM_t &\in \mathbb{R}, \quad \forall\, t = 1, \dots, T; \\
    NIE_t &\in \mathbb{R}, \quad \forall\, t = 1, \dots, T+1; \\
    NOA_t &\in \mathbb{R}, \quad \forall\, t = 0, \dots, T
\end{aligned}
\end{equation}$$

**Variables no negativas:** $$\begin{equation}
    NS_t \geq 0, \qquad VCO_t \geq 0, \qquad OCF_t \geq 0 \qquad \forall\, t = 1, \dots, T
\label{eq:dominio_noneg}
\tag{D2}
\end{equation}$$\
**Justificación variable por variable:**

::: {#tab:dominio_aux}
  **Variable**   **Dominio**    **Justificación**
  -------------- -------------- ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  $TCM_t$        $\mathbb{R}$   $TCM_t = NS_t - VCO_t + IC_t$. Es negativa en semanas de baja venta con desacumulación de inventario ($IC_t < 0$) o con costos variables que superan el ingreso del período.
  $IC_t$         $\mathbb{R}$   Es negativa por definición siempre que el sistema consume inventario neto, situación normal en semanas de demanda alta. Forzarla a $\geq 0$ impediría al modelo vaciar inventario y lo volvería infactible frente a la condición terminal ([\[eq:inv_terminal\]](#eq:inv_terminal){reference-type="ref" reference="eq:inv_terminal"}).
  $EBIT_t$       $\mathbb{R}$   $EBIT_t = TCM_t - fc_t$. Con costos fijos positivos, es negativa en cualquier semana de baja actividad.
  $NIE_t$        $\mathbb{R}$   Negativa en cualquier período en que el rendimiento de las inversiones financieras supere el costo de la deuda, es decir siempre que $i^{FI}FI_{t-1} > i^{DS}DS_{t-1}$. Acotarla a $\geq 0$ impediría al modelo reconocer el ingreso financiero neto y distorsionaría la base gravable de ([\[eq:tax\]](#eq:tax){reference-type="ref" reference="eq:tax"}).
  $TAX_t$        $\mathbb{R}$   Negativa cuando la base gravable $EBIT_t - NIE_t$ es negativa, conforme al supuesto de simetría fiscal declarado en la [7.8](#sec:tax){reference-type="ref+Label" reference="sec:tax"}. Acotarla a $\geq 0$ equivaldría a imponer $TAX_t = \tau^{isr}\max(0, EBIT_t - NIE_t)$ sin las variables binarias que esa formulación requiere, rompiendo la consistencia entre el ISR pagado en ([\[eq:caja\]](#eq:caja){reference-type="ref" reference="eq:caja"}) y los Términos I y V de la función objetivo.
  $OM_t$         $\mathbb{R}$   Diferencia entre cobros y pagos de cuentas abiertas; el signo depende del calendario de vencimientos y es negativo en cualquier semana de pago concentrado a proveedores.
  $FM_t$         $\mathbb{R}$   Flujo neto financiero; negativo cuando el modelo coloca inversiones o amortiza deuda por encima de lo que recupera o contrata.
  $NOA_t$        $\mathbb{R}$   Aunque en la práctica es positiva, puede ser negativa si $AP_t$ supera la suma de inventarios, WIP y CxC. Acotarla artificialmente introduciría una restricción no declarada sobre el crédito comercial.
  $NS_t$         $\geq 0$       Suma de productos $Y_{q,B_T,z,t}\, v_q$ con ambos factores no negativos. La cota es *implicada* por ([\[eq:ns\]](#eq:ns){reference-type="ref" reference="eq:ns"}); se declara por higiene de modelo.
  $VCO_t$        $\geq 0$       Todos los sumandos de ([\[eq:vco\]](#eq:vco){reference-type="ref" reference="eq:vco"}) son productos de variables no negativas por parámetros de costo no negativos. Cota implicada.
  $OCF_t$        $\geq 0$       Ídem para ([\[eq:ocf\]](#eq:ocf){reference-type="ref" reference="eq:ocf"}). Esta cota es además la que sustenta la lectura del signo en el balance de caja ([\[eq:caja\]](#eq:caja){reference-type="ref" reference="eq:caja"}), donde $OCF_t$ aparece restando.

  : Dominio de cada variable auxiliar y condición que lo genera
:::

**Nota de implementación:** En Pyomo la declaración correcta es
`Var(m.T, domain=Reals)` para el grupo (D1) y `domain=NonNegativeReals`
para (D2); en GAMS corresponde `free variable` frente a
`positive variable`; en JuMP, `@variable(m, TCM[t])` sin cota frente a
`@variable(m, NS[t] >= 0)`. Se recomienda incluir una prueba unitaria
que verifique que el modelo construido contiene exactamente ocho
variables auxiliares libres por período, y que $NIE$ está indexada hasta
$T+1$ mientras el resto llega hasta $T$.

# Parámetros {#sec:parametros}

::: xltabular
l X

\
**Parámetro** & **Descripción**\

**Parámetro** & **Descripción**\

\

\
$T$ & Horizonte de planeación (52 semanas)\
$\tau^{isr}$ & Tasa de impuesto sobre la renta (ISR)\
$i^{wacc}$ & Costo ponderado promedio de capital (WACC) por período
semanal, derivado como
$i^{wacc} = (1 + \text{WACC}_{anual})^{1/52} - 1$. **Se calibra sobre la
estructura de capital de largo plazo** (capital contable y deuda de
largo plazo, esta última después de impuestos), excluyendo la deuda
bancaria de corto plazo, cuyo costo se cobra de forma explícita en el
Término V de la función objetivo (ver
[6.2](#sec:consistencia_wacc){reference-type="ref+Label"
reference="sec:consistencia_wacc"})\
$fa_0$ & Valor en libros de los activos fijos netos operativos al inicio
del horizonte ($t = 0$)\
$dep^{sem}$ & Depreciación semanal de los activos fijos operativos\
$fa_t$ & Activos fijos netos operativos en el período $t$:
$fa_t = fa_0 - t \cdot dep^{sem}$, con $t = 0, 1, \dots, T$\
$fc^{caja}_t$ & Costos fijos **desembolsables** del período $t$ (nómina
de estructura, rentas, servicios, seguros). Excluye depreciación,
intereses e ISR\
$int^{LP}_t$ & Intereses de la deuda de largo plazo devengados y pagados
en $t$ (parámetro exógeno)\
$div_t$ & Dividendos y demás distribuciones a accionistas pagadas en $t$
(parámetro exógeno)\
$D^{LP}$ & Saldo de deuda de largo plazo, constante en el horizonte
táctico (no se amortiza; ver
[5.2](#sec:supuestos_operativos){reference-type="ref+Label"
reference="sec:supuestos_operativos"}). Interviene únicamente en la
identidad de
articulación ([\[eq:articulacion\]](#eq:articulacion){reference-type="ref"
reference="eq:articulacion"})\
$fc_t$ & Costos fijos **devengados** del período $t$:
$fc_t = fc^{caja}_t + dep^{sem}$
(ec. [\[eq:fc_devengado\]](#eq:fc_devengado){reference-type="ref"
reference="eq:fc_devengado"}). Incluye la depreciación, que es deducible
del ISR pero no desembolsable\
\
$v_q$ & Precio unitario de venta de la presentación $q$ (por unidad de
presentación: botella de 500 ml o de 1 L)\
$e_r$ & Costo unitario del insumo $r$ (precio de compra al proveedor,
**incluye flete de entrega en planta**; ver
[5.2](#sec:supuestos_operativos){reference-type="ref+Label"
reference="sec:supuestos_operativos"})\
$e_p$ & Costo unitario completo del semiterminado $p$:
$e_p = \sum_{(p,r) \in \mathit{BOM}^1} a^1_{pr} \cdot e_r + kx_p \cdot cd_{B_P}$\
$e_q$ & Costo unitario de la presentación $q$:
$e_q = a^2_{qp} \cdot e_p + cc_q$\
\
$cd_l$ & Costo unitario de transformación primaria por unidad de
capacidad en $l$\
$cc_q$ & Costo unitario de transformación secundaria por unidad de la
presentación $q$\
$ci_l$ & Costo unitario de almacenamiento por unidad de capacidad en
$l$\
$cw$ & Costo unitario de mantener WIP por unidad de semiterminado\
$cy_{lj}$ & Costo unitario de transporte por unidad de capacidad de $l$
a $j$ (aplica sólo a arcos operativos internos: $B_G \to B_P$,
$B_{wip} \to B_S$ y $B_T \to z$; el flete desde proveedores está
incluido en $e_r$)\
\
$kx_p$ & Capacidad de transformación primaria requerida por unidad del
semiterminado $p$\
$ki_\pi$ & Capacidad de almacenamiento requerida por unidad del producto
$\pi$\
$ky_\pi$ & Capacidad de transporte requerida por unidad del producto
$\pi$\
$a^1_{pr}$ & Coeficiente BOM nivel 1: unidades de insumo $r$ por unidad
del semiterminado $p$\
$a^2_{qp}$ & Coeficiente BOM nivel 2: unidades de semiterminado $p$ por
unidad de la presentación $q$. Valores: $a^2_{qp} = 0.5$ para
presentaciones de 500 ml; $a^2_{qp} = 1.0$ para presentaciones de 1 L\
\
$\delta$ & Retraso entero del lead time de producción en períodos
($\delta = 1$)\
$capX_{B_P}$ & Capacidad de transformación primaria en la Instalación A
(horas semanales): $capX_{B_P} = 168 \cdot n^{lin} \cdot \eta^{ef}$, con
$n^{lin}$ líneas de destilación en operación continua y
$\eta^{ef} \in (0,1]$ factor de eficiencia efectiva. Debe declararse
$n^{lin}$ de forma explícita: con una sola línea, $capX_{B_P} \leq 168$
(ver [11](#sec:factibilidad_capacidad){reference-type="ref+Label"
reference="sec:factibilidad_capacidad"})\
$n^{lin}$ & Número de líneas de destilación en la Instalación A\
$\eta^{ef}$ & Factor de eficiencia efectiva de la capacidad nominal
(paros programados, mantenimiento, cambios de receta)\
$capI_l$ & Capacidad de almacenamiento en la localización $l$\
$d_{qzt}$ & Demanda de la presentación $q$ en la zona $z$ en el período
$t$ (por unidad de presentación)\
$\tau_z$ & Plazo de cobro de la zona $z$ en períodos:
$\tau_{z^{loc}}=2$, $\tau_{z^{for}}=4$, $\tau_{z^{exp}}=6$\
\
$p^{lost}_{qz}$ & Costo unitario de oportunidad por demanda insatisfecha
de la presentación $q$ en la zona $z$ (diferenciado por producto y
canal; ver condición de consistencia en
[6.3](#sec:consistencia_plost){reference-type="ref+Label"
reference="sec:consistencia_plost"})\
$\rho$ & Coeficiente de penalización por variabilidad del plan de
producción\
$\alpha$ & Factor de avance promedio del WIP, $\alpha \in (0, 1)$:
fracción del costo completo $e_p$ que se capitaliza en el WIP durante la
retención de calidad\
\
$DS^{max}$ & Línea de crédito bancaria máxima\
$C^{min}$ & Saldo de caja mínimo exigido por la institución bancaria\
$ec'_t$ & Flujo de caja exógeno en $t$ **sin ISR y sin depreciación**:
$ec'_t = fc^{caja}_t + int^{LP}_t(1 - \tau^{isr}) + div_t$
(ec. [\[eq:ec_prima\]](#eq:ec_prima){reference-type="ref"
reference="eq:ec_prima"}). El ISR operativo se modela endógenamente en
$TAX_t$; los intereses de largo plazo entran netos de su escudo fiscal
porque dicho escudo ya está incorporado en $i^{wacc}$\
$i^{FI}$ & Tasa de interés semanal de inversiones financieras de corto
plazo, derivada como $i^{FI} = (1 + i^{FI}_{anual})^{1/52} - 1$\
$i^{DS}$ & Tasa de interés semanal de deuda de corto plazo, derivada
como $i^{DS} = (1 + i^{DS}_{anual})^{1/52} - 1$\
\
$\tau^{pay}_N$ & Plazo de pago de la clase nacional
$R^{(N)} = R^N \cup R^E$, en períodos semanales\
$\tau^{pay}_I$ & Plazo de pago de la clase importada $R^{(I)} = R^I$, en
períodos semanales\
$AP^{max}$ & Exposición máxima acumulada con proveedores (límite de
crédito comercial agregado)\
\
$FI^0,\; AR^0_z,\; C^0,\; DS^0,\; AP^0$ & Posiciones iniciales en los
rubros financieros respectivos\
$I^0_{\pi l}$ & Inventario inicial por producto y localización\
$I^T_{\pi l}$ & Inventario meta terminal mínimo por producto y
localización\
$W^0_p$ & WIP inicial del semiterminado $p$\
$X^0_{p,s}$ & Lanzamientos históricos del semiterminado $p$ en el
período $s$, con soporte $s \in \{-\delta+1, \dots, 0\}$ (frontera del
WIP); nulo fuera de ese soporte\
$S^0_{z,s}$ & Monto de ventas históricas en la zona $z$ realizadas en el
período $s$, pendientes de cobro al inicio del horizonte, con soporte
$s \in \{-\tau_z+1, \dots, 0\}$; nulo fuera de ese soporte (frontera de
cobros)\
$B^0_{r,s}$ & Monto de compras históricas del insumo $r$ realizadas en
el período $s$, pendientes de pago al inicio del horizonte, con soporte
$s \in \{-\tau^{pay}_{k(r)}+1, \dots, 0\}$; nulo fuera de ese soporte
(frontera de pagos)\
$X^{ref}_p$ & Nivel de producción de referencia previo al horizonte
(frontera de la regularización); debe coincidir con el último
lanzamiento histórico,
ec. ([\[eq:cons_xref\]](#eq:cons_xref){reference-type="ref"
reference="eq:cons_xref"})\
:::

**Convención de tasas semanales:** Todas las tasas de interés semanales
($i^{wacc}$, $i^{FI}$, $i^{DS}$) se derivan de sus correspondientes
tasas anuales mediante capitalización compuesta:
$i^{sem} = (1 + i^{anual})^{1/52} - 1$. Esta convención asegura la
equivalencia financiera exacta entre la tasa anual y su aplicación
semanal a lo largo de las 52 semanas del horizonte.

## Estructura de los costos fijos y tratamiento de la depreciación {#sec:depreciacion}

La depreciación es el único rubro del modelo que es simultáneamente
**deducible del ISR** y **no desembolsable**. En la versión anterior, el
parámetro $dep^{sem}$ definía la rampa descendente de $fa_t$ pero no
aparecía en $TCM_t$, $EBIT_t$ ni $TAX_t$: si $fc_t$ no la contenía, el
NOPAT quedaba sobrestimado y el ISR mal calculado; si sí la contenía, el
documento no lo declaraba y la distinción entre $fc_t$ y $ec'_t$ quedaba
sin explicar. Esta subsección cierra esa ambigüedad.

**Descomposición de los costos fijos devengados:** $$\begin{equation}
    fc_t = fc^{caja}_t + dep^{sem} \qquad \forall\, t = 1, \dots, T
\label{eq:fc_devengado}
\tag{P1}
\end{equation}$$

**Composición del flujo de caja exógeno:** $$\begin{equation}
    ec'_t = fc^{caja}_t + int^{LP}_t\big(1 - \tau^{isr}\big) + div_t \qquad \forall\, t = 1, \dots, T
\label{eq:ec_prima}
\tag{P2}
\end{equation}$$

**Condición de dominio de los activos fijos:** $$\begin{equation}
    dep^{sem} \;\leq\; \frac{fa_0}{T} \qquad \Longleftrightarrow \qquad fa_T = fa_0 - T \cdot dep^{sem} \;\geq\; 0
\label{eq:dominio_fa}
\tag{P3}
\end{equation}$$

**Lógica del tratamiento:** La depreciación entra en $fc_t$ y, por
tanto, en $EBIT_t$ y en la base gravable de $TAX_t$; con ello el ISR
reconoce correctamente el escudo fiscal de la depreciación y el NOPAT
del Término I se mide después de depreciación, como exige la definición
de Stewart (1991) cuando la base de capital se mide al valor en libros
*neto*. Simultáneamente, la depreciación queda **fuera** de $ec'_t$,
porque no representa salida de efectivo: el balance de
caja ([\[eq:caja\]](#eq:caja){reference-type="ref" reference="eq:caja"})
sólo debe registrar $fc^{caja}_t$. La coherencia entre ambos lados es lo
que hace que la rampa $fa_t = fa_0 - t \cdot dep^{sem}$ tenga
contrapartida en el estado de resultados en lugar de ser una reducción
gratuita de la base de capital.

**Escudo fiscal de la deuda de largo plazo:** Los intereses $int^{LP}_t$
**no** se deducen en $TAX_t$. La razón es que su escudo fiscal ya está
incorporado en $i^{wacc}$, que por construcción emplea el costo de la
deuda de largo plazo después de impuestos. Deducirlos también en $TAX_t$
sería un doble reconocimiento. Para que el balance de caja registre no
obstante el ISR efectivamente desembolsado, los intereses de largo plazo
entran en $ec'_t$ **netos de su escudo**, es decir multiplicados por
$(1 - \tau^{isr})$. Puesto que $int^{LP}_t$ es exógeno, este ajuste
desplaza el nivel de caja sin alterar el *argmax* del problema; su
efecto práctico es sobre la holgura de la restricción de caja
mínima ([\[eq:caja_min\]](#eq:caja_min){reference-type="ref"
reference="eq:caja_min"}).

**Contraste con el crédito de corto plazo:** El tratamiento anterior es
deliberadamente distinto del que reciben $DS_t$ y $FI_t$. La deuda
bancaria de corto plazo **sí** se deduce en $TAX_t$ a través de $NIE_t$
(ec. [\[eq:tax\]](#eq:tax){reference-type="ref" reference="eq:tax"}),
precisamente porque su costo se cobra de forma explícita en el Término V
y **no** está incluido en $i^{wacc}$
([6.2](#sec:consistencia_wacc){reference-type="ref+Label"
reference="sec:consistencia_wacc"}). La regla general es: cada fuente de
financiamiento paga su escudo fiscal exactamente una vez, ya sea dentro
del WACC o dentro de $TAX_t$, nunca en ambos.

::: {#tab:conciliacion_dep}
  **Rubro**                                    **¿En $EBIT_t$?**   **¿En la base de $TAX_t$?**         **¿En el flujo de caja?**
  ------------------------------------------- ------------------- ----------------------------- ---------------------------------------
  Costos fijos desembolsables $fc^{caja}_t$     Sí (vía $fc_t$)                Sí                          Sí (vía $ec'_t$)
  Depreciación $dep^{sem}$                      Sí (vía $fc_t$)                Sí                                 No
  Intereses de largo plazo $int^{LP}_t$               No            No (escudo en $i^{wacc}$)    Sí, netos: $int^{LP}_t(1-\tau^{isr})$
  Interés neto de corto plazo $NIE_t$                 No                  Sí (se resta)                     Sí (vía $FM_t$)
  Dividendos $div_t$                                  No                       No                          Sí (vía $ec'_t$)

  : Tratamiento de cada rubro fijo o financiero en el devengo, en el
  efectivo y en la base gravable.
:::

## Supuestos operativos {#sec:supuestos_operativos}

- **Demanda determinista:** La demanda $d_{qzt}$ se asume completamente
  conocida para todo el horizonte. El modelo es un programa lineal
  determinista de información completa. No se imponen restricciones de
  no anticipación. Si se utiliza en un esquema de horizonte rodante
  (*rolling horizon*), la demanda se actualiza y el modelo se reoptimiza
  en cada ventana.

- **Ruteo de aprovisionamiento diferenciado:** El solvente ($R^E$) se
  entrega directamente en $B_P$. Los componentes nacionales ($R^N$)
  pueden entregarse tanto en $B_P$ como en $B_G$. Los componentes
  importados ($R^I$) se reciben **exclusivamente** en $B_G$, por lo que
  su disponibilidad en el punto de consumo depende enteramente del arco
  de transferencia interna $B_G \to B_P$, definido sobre $R^N \cup R^I$
  ([2](#tab:auditoria_pc){reference-type="ref+Label"
  reference="tab:auditoria_pc"}).

- **Capacidad de transporte no restrictiva.**

- **Capacidad de transformación secundaria no restrictiva.**

- **Transporte inter-planta instantáneo:** Las Instalaciones A y B están
  separadas por 100 km con un tiempo de tránsito de aproximadamente
  1.5 horas. Dado que el período de discretización es semanal
  (${\geq}\,168$ h), el tiempo de tránsito representa menos del 1% del
  período y se modela como instantáneo: el embarque en $B_{wip}$ en el
  período $t$ se recibe en $B_S$ en el mismo período $t$. La misma
  consideración aplica a la transferencia interna $B_G \to B_P$.

- **Costo de flete de proveedores incluido en $e_r$:** Los proveedores
  entregan los insumos en la localización receptora ($B_P$ o $B_G$) bajo
  condición de entrega en planta (*Delivered at Place* -- DAP). El costo
  de flete está internalizado en el costo unitario $e_r$ y no se modela
  como una variable de transporte separada. En consecuencia, los arcos
  proveedor $\to$ almacén ($l^S \to l$) no generan un costo de
  transporte $cy_{lj}$ adicional. El arco $B_G \to B_P$, en cambio,
  **sí** devenga $cy_{B_G, B_P}$ por tratarse de un movimiento interno
  posterior a la entrega.

- **Partición de clases de pago:** $\{R^{(N)}, R^{(I)}\}$ es una
  partición de $R$. Toda compra pertenece a exactamente una clase de
  pago, lo que garantiza que cada peso comprado se descargue de $AP_t$
  exactamente una vez (ver [9.3](#sec:cxp){reference-type="ref+Label"
  reference="sec:cxp"}).

- **Costos operativos con pago inmediato** (excepto compras de MP, que
  operan con plazo diferido vía CxP).

- **Simetría fiscal (supuesto LP):** El impuesto $TAX_t$ puede ser
  negativo (crédito fiscal inmediato). Este supuesto se relajará en la
  extensión MILP con variables binarias para modelar la asimetría
  fiscal.

- **Depreciación lineal de activos fijos:** Los activos fijos netos
  operativos se deprecian linealmente a razón de $dep^{sem}$ por
  período, de forma que $fa_t = fa_0 - t \cdot dep^{sem}$, sujeto a la
  condición de
  dominio ([\[eq:dominio_fa\]](#eq:dominio_fa){reference-type="ref"
  reference="eq:dominio_fa"}). Se asume que no hay inversiones en
  activos fijos durante el horizonte táctico. La carga de depreciación
  se reconoce en $fc_t$ y por tanto en la base gravable
  ([5.1](#sec:depreciacion){reference-type="ref+Label"
  reference="sec:depreciacion"}).

- **Devengo de cargos sobre saldo de apertura:** Todo cargo de capital y
  todo costo financiero del período $t$ se calcula sobre el saldo
  vigente al *inicio* de $t$, es decir el saldo de cierre de $t-1$. Es
  el capital disponible al comienzo del período el que genera el
  resultado de ese período
  ([6.1](#sec:convencion_cargos){reference-type="ref+Label"
  reference="sec:convencion_cargos"}).

- **Período de continuación:** El horizonte de *decisiones* es
  $t = 1, \dots, T$; el horizonte de *cargos* se extiende hasta $T+1$.
  El período $T+1$ no contiene decisiones ni flujos de efectivo: es un
  dispositivo de valuación que cobra el costo de continuación de las
  posiciones abiertas al cierre
  ([6.1](#sec:convencion_cargos){reference-type="ref+Label"
  reference="sec:convencion_cargos"}).

- **Jerarquía de tasas de corto plazo:** Se asume $i^{DS} > i^{FI}$,
  condición ([\[eq:consistencia_tasas\]](#eq:consistencia_tasas){reference-type="ref"
  reference="eq:consistencia_tasas"}). Sin ella el modelo admitiría un
  arbitraje financiero espurio consistente en endeudarse para invertir.

- **Deuda de largo plazo constante:** El saldo $D^{LP}$ no se amortiza
  ni se incrementa durante el horizonte táctico; sus intereses
  $int^{LP}_t$ son exógenos. Este supuesto es el que permite que la
  identidad de
  articulación ([\[eq:articulacion\]](#eq:articulacion){reference-type="ref"
  reference="eq:articulacion"}) cierre con un único término financiero
  de largo plazo. Si se relajara, habría que añadir el flujo de
  principal al balance de caja y el término correspondiente a la
  identidad.

- **Estado inicial internamente consistente:** Los parámetros de
  frontera no son independientes entre sí. Los saldos $AR^0_z$, $AP^0$ y
  $W^0_p$ deben reproducir exactamente sus respectivos historiales
  pendientes, y los saldos iniciales deben satisfacer las mismas cotas
  que el modelo impone en el horizonte. El conjunto completo de
  condiciones se formula en la
  [5.3](#sec:consistencia_iniciales){reference-type="ref+Label"
  reference="sec:consistencia_iniciales"} y **debe verificarse antes de
  resolver**.

## Condiciones de consistencia de los datos de frontera {#sec:consistencia_iniciales}

Los parámetros de frontera del modelo **no son grados de libertad
independientes**. Los saldos iniciales de cuentas por cobrar, cuentas
por pagar y trabajo en proceso son, por construcción, la agregación de
transacciones históricas que aún no han vencido; declararlos por
separado de sus historiales abre la posibilidad de un estado inicial
contradictorio. Las consecuencias no son cosméticas: dependiendo del
signo del error, el modelo resulta **infactible** o genera **cobros y
pagos fantasma** que inflan el EVA reportado sin contrapartida real.

### Condiciones de agregación {#condiciones-de-agregación .unnumbered}

**Cuentas por cobrar iniciales:** el saldo debe igualar la suma de las
ventas históricas cuyo plazo aún no vence. $$\begin{equation}
    AR^0_z = \sum_{s=-\tau_z+1}^{0} S^0_{z,s} \qquad \forall\, z \in L^M
\label{eq:cons_ar0}
\tag{C1}
\end{equation}$$

**Cuentas por pagar iniciales:** el saldo debe igualar la suma de las
compras históricas pendientes de pago, recorriendo la partición de
clases de pago. $$\begin{equation}
    AP^0 = \sum_{k \in K} \; \sum_{r \in R^{(k)}} \; \sum_{s=-\tau^{pay}_k+1}^{0} B^0_{r,s}
\label{eq:cons_ap0}
\tag{C2}
\end{equation}$$

**Trabajo en proceso inicial:** el WIP heredado debe igualar los
lanzamientos históricos aún no liberados. $$\begin{equation}
    W^0_p = \sum_{s=-\delta+1}^{0} X^0_{p,s} \qquad \forall\, p \in P
\label{eq:cons_w0}
\tag{C3}
\end{equation}$$

**No negatividad de los historiales:** $$\begin{equation}
    S^0_{z,s} \geq 0, \qquad B^0_{r,s} \geq 0, \qquad X^0_{p,s} \geq 0, \qquad I^0_{\pi l} \geq 0, \qquad I^T_{\pi l} \geq 0
\label{eq:cons_noneg0}
\tag{C4}
\end{equation}$$

### Condiciones de cota sobre el estado inicial {#condiciones-de-cota-sobre-el-estado-inicial .unnumbered}

Las restricciones del modelo están indexadas $t = 1, \dots, T$ y por
tanto **no acotan el período $t = 0$**. Si el estado inicial viola
alguna de ellas, el modelo puede resultar infactible en la primera
semana o, peor, partir de un estado físicamente imposible.

$$\begin{equation}
    \sum_{\pi : (\pi, l) \in PL^{Op}} ki_\pi \cdot I^0_{\pi l} \;\leq\; capI_l \qquad \forall\, l \in L^{Op}
\label{eq:cons_capi0}
\tag{C5}
\end{equation}$$

$$\begin{equation}
    C^0 \geq C^{min}, \qquad DS^0 \leq DS^{max}, \qquad AP^0 \leq AP^{max}, \qquad FI^0 \geq 0
\label{eq:cons_cotas0}
\tag{C6}
\end{equation}$$

$$\begin{equation}
    \sum_{p \in P} kx_p \cdot X^0_{p,s} \;\leq\; capX_{B_P} \qquad \forall\, s \in \{-\delta+1, \dots, 0\}
\label{eq:cons_capx0}
\tag{C7}
\end{equation}$$

### Condición sobre la referencia de regularización {#condición-sobre-la-referencia-de-regularización .unnumbered}

$$\begin{equation}
    X^{ref}_p = X^0_{p,0} \qquad \forall\, p \in P
\label{eq:cons_xref}
\tag{C8}
\end{equation}$$

Si $X^{ref}_p$ se fijara arbitrariamente, la ecuación de frontera de la
regularización ([\[eq:regularizacion_frontera\]](#eq:regularizacion_frontera){reference-type="ref"
reference="eq:regularizacion_frontera"}) penalizaría un salto de
producción inexistente en la primera semana, sesgando el plan completo
contra un nivel de referencia que nunca ocurrió.

### Consecuencias de violar cada condición {#consecuencias-de-violar-cada-condición .unnumbered}

::: {#tab:consecuencias_c}
  **Cond.**   **Si se viola**                                                                                                                                                                                                                                                                                                                                                             **Diagnóstico**
  ----------- --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------------------------------------------------------------------------------------------------------------------------
  (C1)        Si $AR^0_z < \sum_s S^0_{z,s}$, la ec. ([\[eq:cxc\]](#eq:cxc){reference-type="ref" reference="eq:cxc"}) descarga más de lo que hay y $AR_{zt} \geq 0$ se vuelve infactible en alguna de las primeras $\tau_z$ semanas. Si $AR^0_z > \sum_s S^0_{z,s}$, el exceso queda atrapado en $NOA_t$ de forma permanente: paga cargo de capital todo el horizonte y nunca se cobra.   Infactibilidad temprana localizada en la zona $z$, o bien $AR_{zT} > 0$ inexplicado con $\sum_q Y_{q,B_T,z,t}=0$ en las últimas $\tau_z$ semanas.
  (C2)        Si $AP^0 < \sum B^0$, los pagos exceden el pasivo y $AP_t \geq 0$ se viola. Si $AP^0 > \sum B^0$, aparece un pasivo que nunca se paga: reduce $NOA_t$ de forma permanente e **infla el EVA** sin contrapartida de caja.                                                                                                                                                     Infactibilidad en las primeras $\max_k \tau^{pay}_k$ semanas, o $AP_T$ con piso positivo irreductible.
  (C3)        Si $W^0_p \neq \sum_s X^0_{p,s}$, la ec. ([\[eq:wip_frontera\]](#eq:wip_frontera){reference-type="ref" reference="eq:wip_frontera"}) libera material que no estaba en proceso (creación de masa) o retiene material inexistente ($W_{pt} < 0$).                                                                                                                             Violación del balance de masa detectable en la prueba de articulación; con $\delta = 1$ el síntoma es $W_{p,1} \neq X_{p,1}$.
  (C5)        El almacén arranca sobrecargado. La ec. ([\[eq:cap_almacen\]](#eq:cap_almacen){reference-type="ref" reference="eq:cap_almacen"}) fuerza el vaciado en $t=1$, lo que puede ser imposible si no hay demanda o capacidad de transformación suficiente.                                                                                                                         Infactibilidad en $t = 1$ con holgura nula en ([\[eq:cap_almacen\]](#eq:cap_almacen){reference-type="ref" reference="eq:cap_almacen"}).
  (C6)        $C^0 < C^{min}$ obliga a contratar deuda en $t=1$ aunque no haya necesidad operativa; $AP^0 > AP^{max}$ hace infactible ([\[eq:ap_max\]](#eq:ap_max){reference-type="ref" reference="eq:ap_max"}) desde el primer período.                                                                                                                                                  Infactibilidad inmediata o $DS_1 > 0$ sin justificación operativa.
  (C8)        Penalización espuria de la regularización en $t=1$, de magnitud $\rho\,|X_{p,1} - X^{ref}_p|$.                                                                                                                                                                                                                                                                              Discontinuidad del plan entre $t=1$ y $t=2$ sin causa en la demanda.

  : Efecto de una violación de las condiciones de consistencia inicial.
  La columna de diagnóstico indica el síntoma observable en la solución.
:::

### Implicación para el esquema de horizonte rodante {#implicación-para-el-esquema-de-horizonte-rodante .unnumbered}

Estas condiciones no se verifican una sola vez. En un esquema de
reoptimización --- y con mayor razón en el gemelo digital proyectado ---
**cada ciclo re-siembra el estado inicial desde el sistema real**, de
modo que $AR^0_z$, $AP^0$, $W^0_p$, $I^0_{\pi l}$, $X^0_{p,s}$,
$S^0_{z,s}$ y $B^0_{r,s}$ se reconstruyen en cada ventana a partir de
datos transaccionales. La verificación (C1)--(C8) debe por tanto
ejecutarse como **rutina de admisión de datos en cada ciclo**, antes de
construir el modelo, y su fallo debe bloquear la optimización en lugar
de degradarla silenciosamente. Es la diferencia entre un gemelo digital
que detecta una inconsistencia de sus propias fuentes y uno que la
propaga al plan.

**Nota sobre $NOA_0$:** Estas condiciones determinan por completo el
valor de $NOA_0$, que por la
ec. ([\[eq:noa\]](#eq:noa){reference-type="ref" reference="eq:noa"}) es
un parámetro compuesto y no una decisión. $NOA_0$ recibe el primer cargo
del Término II, de modo que un error en las condiciones de frontera
desplaza el EVA reportado aunque, siendo constante, no altere el
*argmax*. La excepción es (C1) y (C2): al afectar la trayectoria
completa de $AR_{zt}$ y $AP_t$, sí modifican las decisiones.

::: {#tab:kx_p}
  **Semiterminado**    $kx_p$ **(hrs/unidad)**  **Nota**
  ------------------- ------------------------- --------------------------------------
  $p_1$                         1.20            Receta simple, ciclo corto
  $p_2$                         1.35            Receta simple, ciclo similar a $p_1$
  $p_3$                         1.80            4 importados, proceso más largo
  $p_4$                         2.10            Receta compleja, 7 componentes
  $p_5$                         2.50            Receta más compleja del portafolio

  : Factor de capacidad de destilación por semiterminado ($kx_p$).
:::

# Función objetivo {#sec:objetivo}

La función objetivo maximiza el Valor Económico Agregado acumulado en el
horizonte, formulado en estricto apego a Stewart (1991). Está compuesta
por cuatro términos:

$$\begin{equation}
\boxed{
\begin{aligned}
    \max \; \text{EVA} = \;
    & \underbrace{\sum_{t=1}^{T} \big(TCM_t - fc_t\big)\big(1 - \tau^{isr}\big)}_{\text{I. NOPAT (después de depreciación)}} \\[0.8em]
    & - \underbrace{\sum_{t=1}^{T} \big(fa_{t-1} + NOA_{t-1}\big) \cdot i^{wacc}}_{\text{II. Cargo de capital sobre saldo de apertura}} \\[0.8em]
    & - \underbrace{\sum_{t=1}^{T}\sum_{q \in Q}\sum_{z \in L^M} p^{lost}_{qz} \cdot LS_{qzt} \cdot \big(1 - \tau^{isr}\big)}_{\text{III. Penalización demanda insatisfecha}} \\[0.8em]
    & - \underbrace{\rho \sum_{t=1}^{T}\sum_{p \in P} \big(\Delta^+_{pt} + \Delta^-_{pt}\big)}_{\text{IV. Regularización}} \\[0.8em]
    & - \underbrace{\sum_{t=1}^{T} NIE_t \cdot \big(1 - \tau^{isr}\big)}_{\text{V. Costo financiero neto de corto plazo}} \\[0.8em]
    & - \underbrace{\Big[\, i^{wacc}\big(fa_T + NOA_T\big) \;+\; NIE_{T+1}\big(1 - \tau^{isr}\big) \Big]}_{\text{VI. Valor terminal: cargo de continuación en } T+1}
\end{aligned}
}
\label{eq:objetivo}
\tag{1}
\end{equation}$$

**Donde:**

- = Margen total de contribución del período $t$ (definido en
  ec. [\[eq:tcm\]](#eq:tcm){reference-type="ref" reference="eq:tcm"}).

- = Costos fijos devengados del período $t$,
  $fc_t = fc^{caja}_t + dep^{sem}$
  (ec. [\[eq:fc_devengado\]](#eq:fc_devengado){reference-type="ref"
  reference="eq:fc_devengado"}); el NOPAT queda así medido después de
  depreciación.

- = Tasa de impuesto sobre la renta.

- = Activos operativos netos al **inicio** de $t$, esto es al cierre de
  $t-1$ (definido en ec. [\[eq:noa\]](#eq:noa){reference-type="ref"
  reference="eq:noa"}); excluye $FI_t$, $C_t$ y $DS_t$.

- = Activos fijos netos operativos al inicio de $t$:
  $fa_{t-1} = fa_0 - (t-1) \cdot dep^{sem}$ (rampa descendente por
  depreciación).

- = Costo ponderado promedio de capital por período semanal, calibrado
  sobre la estructura de capital de **largo plazo**
  ([6.2](#sec:consistencia_wacc){reference-type="ref+Label"
  reference="sec:consistencia_wacc"}).

- = Gasto financiero neto de corto plazo devengado en $t$ sobre los
  saldos de apertura (ec. [\[eq:nie\]](#eq:nie){reference-type="ref"
  reference="eq:nie"}).

- = Gasto financiero neto de continuación, devengado sobre las
  posiciones $DS_T$ y $FI_T$ que el plan deja abiertas al cierre del
  horizonte.

- = Costo de oportunidad unitario diferenciado por presentación $q$ y
  zona $z$ (ver condición de consistencia en
  [6.3](#sec:consistencia_plost){reference-type="ref+Label"
  reference="sec:consistencia_plost"}).

- = Demanda insatisfecha de $q$ en la zona $z$ en $t$ (definida en
  ec. [\[eq:demanda\]](#eq:demanda){reference-type="ref"
  reference="eq:demanda"}).

- = Coeficiente de penalización por variabilidad del plan de producción.

- = Incremento y decremento en la producción respecto al período
  anterior
  (ec. [\[eq:regularizacion\]](#eq:regularizacion){reference-type="ref"
  reference="eq:regularizacion"}).

**Justificación de cada término:**

**Término I --- NOPAT:** Beneficio operativo neto después de impuestos y
**después de depreciación**, puesto que $fc_t$ incorpora $dep^{sem}$ por
la ec. ([\[eq:fc_devengado\]](#eq:fc_devengado){reference-type="ref"
reference="eq:fc_devengado"}). Esta es la medida que corresponde a una
base de capital valorada a libros *neto*: si el NOPAT se midiera antes
de depreciación mientras $fa_t$ decrece, el modelo reconocería una
utilidad que no ha absorbido el desgaste del activo que la produce. La
tasa $(1 - \tau^{isr})$ se aplica período a período bajo el supuesto de
simetría fiscal (las pérdidas generan crédito fiscal inmediato). Este
supuesto se relajará en la extensión MILP.

**Término II --- Cargo de capital (Stewart, 1991):** Penaliza la
inmovilización de capital en activos *operativos*, medidos al **saldo de
apertura** $(fa_{t-1} + NOA_{t-1})$. La justificación es de causalidad
contable: el capital que genera el resultado del período $t$ es el que
estaba disponible al comenzar $t$, no el que queda al cerrarlo. Cobrar
sobre el saldo de cierre permitiría al modelo crear valor en $t$ y pagar
el cargo de $t$ sobre el propio activo que ese valor acaba de generar,
lo que subsidia la acumulación intraperíodo. Con esta convención el
rango $t = 1, \dots, T$ produce exactamente **52 cargos semanales para
52 semanas** y recorre los saldos $NOA_0, \dots, NOA_{T-1}$: el estado
inicial heredado entra de forma natural en el primer término, sin
necesidad de un cargo adicional en $t = 0$. Conforme a la definición de
capital empleado de Stewart, $NOA_t$ incluye inventarios, WIP (valorado
al grado de avance $\alpha$) y cuentas por cobrar, netos de cuentas por
pagar. Se excluyen las posiciones financieras de corto plazo ($FI_t$,
$C_t$, $DS_t$), cuyo costo y rendimiento se valoran de forma explícita
en el Término V; la ausencia de traslape entre ambos tratamientos se
demuestra en la [6.2](#sec:consistencia_wacc){reference-type="ref+Label"
reference="sec:consistencia_wacc"}.

**Término III --- Penalización por demanda insatisfecha:** Cada unidad
no servida se penaliza con $p^{lost}_{qz}$, diferenciado por
presentación y zona para reflejar la heterogeneidad del margen de
contribución $(v_q - e_q)$ y del costo de capital asociado al plazo de
cobro $\tau_z$. La tasa $(1 - \tau^{isr})$ se aplica para mantener la
consistencia fiscal con el NOPAT: el costo de oportunidad de no vender
se trata como un ingreso operativo renunciado, sujeto a la misma carga
impositiva. El parámetro $p^{lost}_{qz}$ debe satisfacer la condición de
consistencia paramétrica documentada en la
[6.3](#sec:consistencia_plost){reference-type="ref+Label"
reference="sec:consistencia_plost"}.

**Término IV --- Regularización:** Penaliza la variabilidad del plan de
producción para evitar soluciones degeneradas con patrones de producción
erráticos. El coeficiente $\rho > 0$ debe calibrarse como un costo menor
respecto al margen de contribución para no distorsionar la optimalidad
económica (típicamente $\rho \ll \min_{q} (v_q - e_q)$).

**Término V --- Costo financiero neto de corto plazo:** Valora el plan
de tesorería. En la versión anterior las tasas $i^{DS}$ e $i^{FI}$
intervenían únicamente en $FM_t$, que alimenta $C_t$, y $C_t$ no
aparecía en la función objetivo: el modelo era **estrictamente
indiferente** entre financiarse con deuda bancaria cara o con recursos
propios, y la integración físico-financiera quedaba reducida a una
condición de factibilidad de caja. El Término V cierra ese canal. Se
formula después de impuestos, $(1 - \tau^{isr})$, en consistencia con la
base gravable $EBIT_t - NIE_t$ de la
ec. ([\[eq:tax\]](#eq:tax){reference-type="ref" reference="eq:tax"}): el
escudo fiscal del crédito de corto plazo se reconoce exactamente una
vez, en el ISR, y el Término V registra el costo neto de ese escudo.
Tres consecuencias inmediatas: (a) el modelo sólo recurre a $DS_t$
cuando la restricción de caja
mínima ([\[eq:caja_min\]](#eq:caja_min){reference-type="ref"
reference="eq:caja_min"}) lo obliga; (b) prefiere colocar los excedentes
en $FI_t$ antes que dejarlos ociosos en $C_t$, porque sólo los primeros
devengan rendimiento; (c) bajo la
condición ([\[eq:consistencia_tasas\]](#eq:consistencia_tasas){reference-type="ref"
reference="eq:consistencia_tasas"}) nunca mantiene deuda e inversión
simultáneamente, lo que elimina una fuente de óptimos alternativos sin
necesidad de variables binarias.

**Término VI --- Valor terminal como cargo de continuación:** Cobra un
período adicional de costo de capital y de costo financiero sobre las
posiciones que el plan deja abiertas al cierre: $NOA_T$, $fa_T$, $DS_T$
y $FI_T$. Su función es doble. Primero, cierra el rango del Término II,
que por la convención de apertura sólo alcanza hasta $NOA_{T-1}$.
Segundo, y más importante, elimina la degeneración terminal del bloque
financiero: sin él, $DS_T$ y $FI_T$ tienen coeficiente objetivo **nulo**
--- sus intereses se devengarían en $T+1$, fuera del horizonte --- de
modo que el solver es indiferente entre $DS_T = 0$ y $DS_T = DS^{max}$ y
reporta un plan de tesorería terminal arbitrario. La formulación como
**cargo** y no como crédito al valor en libros es esencial y se
justifica en la [6.1](#sec:convencion_cargos){reference-type="ref+Label"
reference="sec:convencion_cargos"}.

## Convención de devengo de cargos y tratamiento terminal {#sec:convencion_cargos}

**Convención.** Todo cargo de capital y todo costo financiero se devenga
sobre el **saldo de apertura** del período. El horizonte de *decisiones*
abarca $t = 1, \dots, T$; el horizonte de *cargos* se extiende hasta
$T + 1$. El período $T + 1$ no contiene variables de decisión,
restricciones de balance ni flujos de efectivo: es exclusivamente un
dispositivo de valuación que cobra el costo de continuación de las
posiciones abiertas al cierre, bajo el supuesto de que el estado en $T$
persiste durante un período adicional.

### Por qué el valor terminal debe ser un cargo y no un crédito {#por-qué-el-valor-terminal-debe-ser-un-cargo-y-no-un-crédito .unnumbered}

La formulación intuitiva de un valor terminal consistiría en acreditar a
la función objetivo el valor en libros del capital liberado al cierre,
por ejemplo $+\big(fa_T + NOA_T + C_T + FI_T - DS_T\big)$. **Esa
formulación es incorrecta bajo un objetivo de ingreso residual y produce
un doble conteo del margen.** La demostración es directa.

Considérense dos planes idénticos salvo en el período $T$, donde
disponen de una unidad de la presentación $q$ ya fabricada y en
inventario:

- *Plan A (conservar):* no vende. Su aportación es $TCM_T = 0$ y el
  inventario terminal vale $e_q$.

- *Plan B (vender):* despacha la unidad. Entonces $NS_T = v_q$ e
  $IC_T = -e_q$, de donde $TCM_T = v_q - e_q$; la cuenta por cobrar
  terminal vale $v_q$.

El Término I premia al Plan B con $(v_q - e_q)(1 - \tau^{isr})$, que es
exactamente el margen. Si además se acreditara el valor en libros
terminal, el Plan B recibiría una segunda vez la diferencia $v_q - e_q$,
ahora vía la sustitución de inventario por cuentas por cobrar dentro de
$NOA_T$. El margen se contaría dos veces y el modelo tendría un
incentivo espurio a concentrar ventas en las últimas semanas.

La raíz del problema es que la suma $\sum_t TCM_t$ ya incorpora la
variación total de inventario del horizonte,
$\big(\text{Inv}_T - \text{Inv}_0\big)$, a través del término $IC_t$ de
la ec. ([\[eq:ic\]](#eq:ic){reference-type="ref" reference="eq:ic"}); y
$\sum_t NS_t$ ya reconoce los ingresos por ventas con independencia de
que se hayan cobrado. El capital terminal, por tanto, **ya está
valorado** en el objetivo. Lo único que falta es el costo de
*mantenerlo* un período más, que es precisamente el Término VI.

Esta conclusión es la que predice la teoría del ingreso residual
(Preinreich 1937; Lücke 1955; Ohlson 1995): bajo la condición de *clean
surplus*, la suma de ingresos residuales reproduce el valor creado sin
necesidad de un valor terminal contable, porque el capital en libros al
cierre es neutral por construcción --- se supone que a partir de $T$
rinde exactamente su costo de capital, de modo que su ingreso residual
futuro es cero.

### Alcance efectivo del Término VI {#alcance-efectivo-del-término-vi .unnumbered}

Sumando los Términos II y VI, el cargo de capital total resulta
$$\sum_{t=1}^{T}\big(fa_{t-1} + NOA_{t-1}\big) i^{wacc} \;+\; \big(fa_T + NOA_T\big) i^{wacc}
    \;=\; \sum_{t=0}^{T}\big(fa_t + NOA_t\big) i^{wacc},$$ que coincide
numéricamente con el rango empleado en la versión 2.2. Conviene ser
explícito al respecto: la versión anterior llegaba al **conjunto
correcto de términos por un razonamiento equivocado**. Su justificación
era *ad hoc* --- un cargo en $t = 0$ "para penalizar el estado inicial"
y otro en $t = T$ "para desincentivar el inventario terminal" --- y
dejaba sin explicar por qué un horizonte de 52 semanas debía soportar 53
cargos. La convención de apertura más el período de continuación
**deriva** ese mismo conjunto: 52 cargos operativos, uno por semana, más
un cargo de continuación que es un dispositivo de valuación declarado.
Lo que sí cambia de manera sustantiva es la **atribución** --- qué
capital genera qué resultado --- y, sobre todo, el hecho de que la misma
convención se aplica ahora al bloque financiero, donde no existía ningún
cargo y donde su ausencia producía degeneración.

### Degeneración residual {#degeneración-residual .unnumbered}

Puesto que $NOA_T$ sí recibe cargo pero el margen de una venta en $T$ no
recibe crédito terminal, la única indiferencia que subsiste es la de las
compras de materia prima realizadas en la última semana: incrementan el
inventario en $e_r$ y las cuentas por pagar en $e_r$, con efecto neto
nulo sobre $NOA_T$ y sobre $VCO_T - IC_T$. Es una indiferencia inocua,
acotada al volumen de compras de un período y sin efecto sobre las
decisiones de producción o venta. Si se desea eliminarla por completo,
basta con imponer la condición terminal de
inventario ([\[eq:inv_terminal\]](#eq:inv_terminal){reference-type="ref"
reference="eq:inv_terminal"}) como igualdad para los insumos, o añadir
una cota superior de compra en $t = T$.

## Consistencia entre el WACC y el costo financiero explícito {#sec:consistencia_wacc}

La coexistencia del cargo de capital al WACC (Término II) y del costo
financiero explícito (Término V) exige una regla de calibración precisa,
sin la cual reaparecería el doble cargo que la versión 2.1 eliminó.

**Regla de calibración.** El parámetro $i^{wacc}$ se estima sobre la
estructura de capital de **largo plazo** exclusivamente:
$$\text{WACC}_{anual} = w_E \cdot k_E + w_{D}^{LP} \cdot k_{D}^{LP}\big(1 - \tau^{isr}\big),
    \qquad w_E + w_{D}^{LP} = 1,$$ donde las ponderaciones se calculan
sobre capital contable y deuda de largo plazo únicamente. La deuda
bancaria de corto plazo $DS_t$ **no** entra en las ponderaciones, porque
su costo se cobra íntegramente en el Término V a la tasa
$i^{DS}(1 - \tau^{isr})$.

**Identidad de balance y alcance del supuesto.** La identidad contable
del sistema es
$$\underbrace{fa_t + NOA_t}_{\text{base del Término II}} \;+\; \underbrace{C_t + FI_t}_{\text{tesorería}}
    \;=\; \underbrace{E_t + D^{LP}_t}_{\text{capital de largo plazo}} \;+\; \underbrace{DS_t}_{\text{crédito de corto plazo}},$$
de donde la base del Término II equivale a
$E_t + D^{LP}_t + DS_t - C_t - FI_t$. La formulación adoptada cobra
$i^{wacc}$ sobre la base completa y, adicionalmente,
$i^{DS}(1-\tau^{isr})$ sobre $DS_t$. En la medida en que el crédito de
corto plazo financie una parte de los activos operativos, subsiste un
traslape acotado por $i^{wacc} \cdot \overline{DS}$, donde
$\overline{DS}$ es el saldo medio de deuda de corto plazo en el
horizonte. El supuesto que lo hace admisible es que $DS_t$ se emplea
como instrumento de liquidez transitoria y no como fuente estructural de
financiamiento del capital de trabajo, de modo que
$\overline{DS} \ll fa_0 + \overline{NOA}$. **Este supuesto debe
verificarse numéricamente sobre la solución óptima** y reportarse junto
con los resultados.

**Formulación exacta (alternativa).** Si la verificación anterior
fallara, la corrección exacta consiste en cobrar el WACC únicamente
sobre el capital de largo plazo efectivamente empleado, sustituyendo la
base del Término II por
$$LTC_{t-1} \;=\; fa_{t-1} + NOA_{t-1} - DS_{t-1} + C_{t-1} + FI_{t-1},$$
que se deduce directamente de la identidad de balance anterior. Esta
variante permanece **lineal** y elimina el traslape por construcción; su
costo es reintroducir $C_t$, $FI_t$ y $DS_t$ en la base de capital, con
la consiguiente pérdida de correspondencia literal con la definición de
capital empleado de Stewart (1991). Se documenta aquí como extensión
disponible y se recomienda reservarla para la versión MILP, donde la
estructura de financiamiento admite un tratamiento más rico.

## Condición de consistencia paramétrica para $p^{lost}_{qz}$ {#sec:consistencia_plost}

Para que el modelo se comporte de manera económicamente razonable --- es
decir, para que el optimizador siempre prefiera servir la demanda cuando
la capacidad productiva lo permite, en lugar de dejar ventas
insatisfechas --- el parámetro de penalización $p^{lost}_{qz}$ debe
satisfacer la siguiente condición:

$$\begin{equation}
\boxed{
    p^{lost}_{qz} > \max\left\{0,\;\; \frac{i^{wacc} \cdot v_q \cdot \tau_z}{1 - \tau^{isr}} - (v_q - e_q)\right\} \qquad \forall\, q \in Q,\; z \in L^M
}
\label{eq:consistencia_plost}
\tag{1b}
\end{equation}$$

**Derivación:** El beneficio marginal neto de servir una unidad de
demanda de la presentación $q$ en la zona $z$ tiene dos componentes en
la función objetivo:

1.  *Contribución al NOPAT:* $(v_q - e_q)(1 - \tau^{isr})$, positiva
    siempre que el producto sea rentable.

2.  *Cargo de capital sobre la cuenta por cobrar generada:* La venta
    genera una CxC de $v_q$ que permanece $\tau_z$ períodos en $NOA_t$,
    acumulando un cargo de capital de aproximadamente
    $i^{wacc} \cdot v_q \cdot \tau_z$.

El beneficio neto de servir una unidad es, por tanto:
$$(v_q - e_q)(1 - \tau^{isr}) - i^{wacc} \cdot v_q \cdot \tau_z$$

El modelo prefiere servir la demanda si y sólo si el beneficio neto de
servir más la penalización evitada por no perder la venta es positivo:
$$\underbrace{(v_q - e_q)(1 - \tau^{isr}) - i^{wacc} \cdot v_q \cdot \tau_z}_{\text{Beneficio neto de servir}} + \underbrace{p^{lost}_{qz}(1 - \tau^{isr})}_{\text{Penalización evitada}} > 0$$

Despejando $p^{lost}_{qz}$:
$$p^{lost}_{qz} > \frac{i^{wacc} \cdot v_q \cdot \tau_z}{1 - \tau^{isr}} - (v_q - e_q)$$

Dado que $p^{lost}_{qz} \geq 0$ por definición, la condición se expresa
con el operador $\max\{0, \cdot\}$. Cuando el margen de contribución
neto del cargo de capital es positivo, la condición se satisface
automáticamente con cualquier $p^{lost}_{qz} \geq 0$. La condición es
vinculante en zonas con plazos de cobro largos (alto $\tau_z$), donde el
costo de inmovilizar capital en CxC puede erosionar o superar el margen
de contribución.

**Recomendación práctica:** Se sugiere fijar
$p^{lost}_{qz} = \beta \cdot (v_q - e_q)$ con $\beta \geq 1$,
verificando la
condición ([\[eq:consistencia_plost\]](#eq:consistencia_plost){reference-type="ref"
reference="eq:consistencia_plost"}) para cada combinación $(q, z)$ con
los valores instanciados de $i^{wacc}$, $v_q$, $e_q$, $\tau_z$ y
$\tau^{isr}$.

**Efecto de la convención de apertura:** Con el cargo devengado sobre el
saldo de apertura, el conteo de períodos deja de ser una aproximación.
Una venta realizada en $t$ genera una cuenta por cobrar que figura en
$NOA_t, NOA_{t+1}, \dots, NOA_{t+\tau_z-1}$ y que por tanto recibe cargo
en los períodos $t+1, \dots, t+\tau_z$: **exactamente $\tau_z$ cargos**,
ni uno más ni uno menos. El término $i^{wacc} \cdot v_q \cdot \tau_z$ de
la
condición ([\[eq:consistencia_plost\]](#eq:consistencia_plost){reference-type="ref"
reference="eq:consistencia_plost"}) pasa así de ser una aproximación a
ser el valor exacto del cargo acumulado.

**Efecto del Término V:** La incorporación del costo financiero
explícito hace que servir la demanda tenga un beneficio adicional no
contemplado en la derivación anterior: el cobro reduce la necesidad de
crédito de corto plazo y ahorra $i^{DS}(1 - \tau^{isr})$ por peso y por
período. La
condición ([\[eq:consistencia_plost\]](#eq:consistencia_plost){reference-type="ref"
reference="eq:consistencia_plost"}) sigue siendo por tanto **suficiente,
aunque conservadora**: todo $p^{lost}_{qz}$ que la satisfaga garantiza
la preferencia por servir, y algunos valores menores también lo harían.
Se mantiene en su forma conservadora por simplicidad y se recomienda
complementarla con la verificación numérica *ex post* de que
$LS_{qzt} > 0$ sólo ocurre cuando la restricción de
capacidad ([\[eq:cap_destilacion\]](#eq:cap_destilacion){reference-type="ref"
reference="eq:cap_destilacion"}) está activa.

## Condición de consistencia paramétrica entre tasas de corto plazo {#sec:consistencia_tasas}

La incorporación del Término V exige una segunda condición paramétrica,
esta vez sobre las tasas de corto plazo:

$$\begin{equation}
\boxed{
    i^{DS} \;>\; i^{FI}
}
\label{eq:consistencia_tasas}
\tag{1c}
\end{equation}$$

**Justificación:** Considérese un plan que contrata una unidad adicional
de deuda y coloca simultáneamente el producto en inversiones
financieras, de forma que $DS_t$ y $FI_t$ aumentan en la misma magnitud.
El flujo de caja $FM_t$ de la
ec. ([\[eq:fm\]](#eq:fm){reference-type="ref" reference="eq:fm"}) queda
inalterado y las restricciones de balance se satisfacen. La aportación
al objetivo vía el Término V es
$$-\big(i^{DS} - i^{FI}\big)\big(1 - \tau^{isr}\big),$$ estrictamente
negativa si y sólo si se
cumple ([\[eq:consistencia_tasas\]](#eq:consistencia_tasas){reference-type="ref"
reference="eq:consistencia_tasas"}). Bajo esa condición el modelo nunca
mantiene deuda e inversión simultáneamente en el óptimo, lo que
**elimina una familia completa de óptimos alternativos sin recurrir a
variables binarias**. Si la condición se violara --- situación
financieramente anómala, pues implicaría un arbitraje disponible en el
mercado --- el modelo escalaría $DS_t$ hasta $DS^{max}$ de forma
mecánica y la solución perdería sentido económico.

**Verificación:** La condición se comprueba una sola vez sobre los
parámetros instanciados, antes de resolver. No se implementa como
restricción del LP.

# Restricciones contables {#sec:contables}

## Margen total de contribución {#sec:tcm}

$$\begin{equation}
    TCM_t = NS_t - VCO_t + IC_t \qquad \forall\, t = 1, \dots, T
\label{eq:tcm}
\tag{2}
\end{equation}$$

**Donde:**

- = Margen total de contribución del período $t$ (variable libre; ver
  ec. [\[eq:dominio_libre\]](#eq:dominio_libre){reference-type="ref"
  reference="eq:dominio_libre"}).

- = Ventas netas del período $t$
  (ec. [\[eq:ns\]](#eq:ns){reference-type="ref" reference="eq:ns"}).

- = Costos variables de operación del período $t$
  (ec. [\[eq:vco\]](#eq:vco){reference-type="ref" reference="eq:vco"}).

- = Cambio en inventario valorado al costo
  (ec. [\[eq:ic\]](#eq:ic){reference-type="ref" reference="eq:ic"});
  refleja el principio de *matching*: la producción inventariada genera
  valor que se imputa al período de producción, no al de venta.

## Activos operativos netos (base de capital del EVA) {#sec:noa}

Los activos operativos netos se componen de tres rubros positivos
(inventarios físicos valorados al costo completo, WIP valorado al grado
de avance y cuentas por cobrar agregadas) menos las cuentas por pagar
como pasivo operativo no financiero. Conforme a Stewart (1991), este
concepto excluye las posiciones de tesorería ($FI_t$, $C_t$) y la deuda
financiera ($DS_t$).

$$\begin{equation}
    NOA_t = \sum_{(\pi, l) \in PL^{Op}} I_{\pi l t} \cdot e_\pi + \sum_{p \in P} W_{pt} \cdot \alpha \cdot e_p + \sum_{z \in L^M} AR_{zt} - AP_t \qquad \forall\, t = 0, \dots, T
\label{eq:noa}
\tag{3}
\end{equation}$$

**Donde:**

- = Inventario del producto $\pi$ en localización $l$ al final de $t$.

- = Costo unitario completo del producto $\pi$ ($e_r$ para insumos,
  $e_p$ para semiterminados, $e_q$ para presentaciones).

- = Trabajo en proceso del semiterminado $p$ al final de $t$.

- = Factor de avance promedio del WIP, $\alpha \in (0, 1)$. Refleja que
  el material en proceso no ha completado la totalidad de la
  transformación primaria, conforme a la NIF C-4 y la IAS 2 (valoración
  al grado de avance).

- = Costo unitario completo del semiterminado $p$ (materiales +
  transformación primaria).

- = Cuentas por cobrar de la zona $z$ al final de $t$.

- = Cuentas por pagar al final de $t$ (pasivo operativo no financiero).

- = Variable libre de signo
  (ec. [\[eq:dominio_libre\]](#eq:dominio_libre){reference-type="ref"
  reference="eq:dominio_libre"}).

**Nota sobre la coherencia con Stewart (1991):** El capital empleado en
la fórmula del EVA se define como el valor en libros de todos los
activos operativos menos los pasivos corrientes que no devengan interés.
Las cuentas por pagar ($AP_t$) son pasivos operativos no financieros y
reducen el capital empleado. La deuda de corto plazo ($DS_t$) es un
pasivo financiero y no se resta aquí; su costo se cobra de forma
explícita en el Término V de la función objetivo y su escudo fiscal se
reconoce en la base gravable de la
ec. ([\[eq:tax\]](#eq:tax){reference-type="ref" reference="eq:tax"}).

**Uso de $NOA_t$ en el objetivo:** Aunque la variable se define para
$t = 0, \dots, T$, el Término II la emplea únicamente en sus *saldos de
apertura* $NOA_0, \dots, NOA_{T-1}$, y el Término VI utiliza $NOA_T$
como base del cargo de continuación. Cada saldo recibe por tanto
exactamente un cargo, y $NOA_0$ queda determinado por las condiciones
iniciales ([\[eq:inv_inicial\]](#eq:inv_inicial){reference-type="ref"
reference="eq:inv_inicial"}),
([\[eq:wip_ini\]](#eq:wip_ini){reference-type="ref"
reference="eq:wip_ini"})
y ([\[eq:ci_financieras\]](#eq:ci_financieras){reference-type="ref"
reference="eq:ci_financieras"}): es un parámetro compuesto, no una
decisión, de modo que su cargo desplaza el valor del objetivo sin
alterar el *argmax*.

## Ventas netas {#sec:ns}

$$\begin{equation}
    NS_t = \sum_{\substack{(q, (B_T, z)) \in PC \\ z \in L^M}} Y_{q, B_T, z, t} \cdot v_q \qquad \forall\, t = 1, \dots, T
\label{eq:ns}
\tag{4}
\end{equation}$$

**Donde:**

- = Cantidad de presentación $q$ despachada desde $B_T$ hacia la zona de
  mercado $z$ en $t$ (en unidades de presentación).

- = Precio unitario de venta de la presentación $q$ (por unidad de
  presentación).

## Cambio en inventario {#sec:ic}

El cambio en inventario en el período es la diferencia entre el
inventario al final del período actual y el del período anterior,
valorada al costo completo de cada producto. El WIP se valora al grado
de avance $\alpha \cdot e_p$, asegurando que sólo la fracción de costo
correspondiente al avance se capitalice en inventario durante el
proceso, y que la fracción restante $(1 - \alpha)$ se capitalice al
momento de la liberación (cuando el producto transita de WIP a
$B_{wip}$, valorado ya al costo completo $e_p$).

$$\begin{equation}
    IC_t = \sum_{(\pi, l) \in PL^{Op}} \big(I_{\pi l t} - I_{\pi l, t-1}\big) \cdot e_\pi + \sum_{p \in P} \big(W_{pt} - W_{p, t-1}\big) \cdot \alpha \cdot e_p \qquad \forall\, t = 1, \dots, T
\label{eq:ic}
\tag{5}
\end{equation}$$

**Donde:**

- = Cambio en inventario del producto $\pi$ en la localización $l$
  durante el período $t$.

- = Costo unitario completo del producto $\pi$.

- = Cambio en WIP del semiterminado $p$ durante el período $t$.

- = Costo parcial del semiterminado en proceso, proporcional al grado de
  avance $\alpha$.

- = Variable libre de signo: es negativa en todo período de
  desacumulación neta de inventario
  (ec. [\[eq:dominio_libre\]](#eq:dominio_libre){reference-type="ref"
  reference="eq:dominio_libre"}).

## Costos variables de operación {#sec:vco}

Los costos variables integran cinco componentes. El costo de
almacenamiento se calcula sobre el **inventario promedio** del período,
$\tfrac{1}{2}(I_{\pi l, t-1} + I_{\pi l t})$, para aproximar mejor el
costo acumulado durante todo el período. El costo de inmovilización del
WIP también se calcula sobre el promedio
$\tfrac{1}{2}(W_{p, t-1} + W_{pt})$.

$$\begin{equation}
\begin{aligned}
VCO_t = \;& \underbrace{\sum_{\substack{(r, (l^S, l)) \in PC \\ l^S \in L^S}} Y_{r, l^S, l, t} \cdot e_r}_{\text{(i) materias primas}} + \underbrace{\sum_{p \in P} X_{pt} \cdot kx_p \cdot cd_{B_P}}_{\text{(ii) transf.\ primaria}} + \underbrace{\sum_{q \in Q} Z_{qt} \cdot cc_q}_{\text{(iii) transf.\ secundaria}} \\[1em]
& + \underbrace{\sum_{(\pi, l) \in PL^{Op}} \frac{1}{2}\big(I_{\pi l, t-1} + I_{\pi l t}\big) \cdot ki_\pi \cdot ci_l + \sum_{p \in P} \frac{1}{2}\big(W_{p, t-1} + W_{pt}\big) \cdot cw}_{\text{(iv) almacenamiento e inmovilización WIP (inventario promedio)}} \\[1em]
& + \underbrace{\sum_{\substack{(\pi, (l, j)) \in PC \\ l \notin L^S}} Y_{\pi l j t} \cdot ky_\pi \cdot cy_{lj}}_{\text{(v) transporte interno}}
\qquad \forall\, t = 1, \dots, T
\end{aligned}
\label{eq:vco}
\tag{6}
\end{equation}$$

**Donde:**

- $Y_{r, l^S, l, t}$ = cantidad de insumo $r$ comprada al proveedor
  $l^S$ con destino $l$; $e_r$ = costo unitario (incluye flete de
  entrega). Recorre los 16 arcos de aprovisionamiento de
  [2](#tab:auditoria_pc){reference-type="ref+Label"
  reference="tab:auditoria_pc"}.

- $X_{pt}$ = lanzamiento a transformación primaria; $kx_p$ = factor de
  capacidad; $cd_{B_P}$ = costo unitario de destilación.

- $Z_{qt}$ = producción de presentación $q$; $cc_q$ = costo unitario de
  transformación secundaria.

- $\tfrac{1}{2}(I_{\pi l, t-1} + I_{\pi l t})$ = inventario promedio del
  período (inicio + fin entre 2); $ki_\pi$ = factor de capacidad de
  almacenamiento; $ci_l$ = costo unitario de almacenamiento en $l$.
  Análogamente para WIP con $cw$.

- $Y_{\pi ljt}$ = cantidad transportada en arcos operativos internos
  ($l \notin L^S$): los 45 pares de $PC$ correspondientes a
  $B_G \to B_P$ (10), $B_{wip} \to B_S$ (5) y $B_T \to z$ (30). $ky_\pi$
  = factor de capacidad de transporte; $cy_{lj}$ = costo unitario de
  transporte de $l$ a $j$. Los arcos desde proveedores ($l \in L^S$) se
  excluyen porque su costo de flete ya está incluido en $e_r$.

## Utilidad operativa antes de impuestos {#sec:ebit}

$$\begin{equation}
    EBIT_t = TCM_t - fc_t \qquad \forall\, t = 1, \dots, T
\label{eq:ebit}
\tag{7}
\end{equation}$$

**Donde:**

- = Utilidad operativa antes de impuestos e intereses del período $t$
  (variable libre;
  ec. [\[eq:dominio_libre\]](#eq:dominio_libre){reference-type="ref"
  reference="eq:dominio_libre"}).

- = Margen total de contribución del período $t$.

- = Costos fijos **devengados** del período $t$,
  $fc_t = fc^{caja}_t + dep^{sem}$
  (ec. [\[eq:fc_devengado\]](#eq:fc_devengado){reference-type="ref"
  reference="eq:fc_devengado"}). Parámetro exógeno; incluye depreciación
  y excluye ISR e intereses.

**Nota sobre la depreciación:** A diferencia de la versión anterior,
$fc_t$ contiene explícitamente la carga $dep^{sem}$. En consecuencia
$EBIT_t$ está medido **después de depreciación** y la base gravable de
la ec. ([\[eq:tax\]](#eq:tax){reference-type="ref" reference="eq:tax"})
reconoce su deducibilidad. Esta es la contrapartida en el estado de
resultados de la rampa $fa_t = fa_0 - t\cdot dep^{sem}$ que reduce la
base de capital del Término II
([5.1](#sec:depreciacion){reference-type="ref+Label"
reference="sec:depreciacion"}).

## Gasto financiero neto de corto plazo {#sec:nie}

El gasto financiero neto agrupa el costo de la deuda bancaria y el
rendimiento de las inversiones financieras, ambos devengados sobre los
**saldos de apertura** del período, en coherencia con la convención de
la [6.1](#sec:convencion_cargos){reference-type="ref+Label"
reference="sec:convencion_cargos"} y con la ecuación de gestión
financiera ([\[eq:fm\]](#eq:fm){reference-type="ref"
reference="eq:fm"}).

$$\begin{equation}
    NIE_t = i^{DS} \cdot DS_{t-1} - i^{FI} \cdot FI_{t-1} \qquad \forall\, t = 1, \dots, T+1
\label{eq:nie}
\tag{7b}
\end{equation}$$

**Donde:**

- = Gasto financiero neto devengado en $t$ (variable libre;
  ec. [\[eq:dominio_libre\]](#eq:dominio_libre){reference-type="ref"
  reference="eq:dominio_libre"}). Es negativo cuando el rendimiento de
  las inversiones supera el costo de la deuda.

- = Saldos al inicio de $t$, con $DS_0 = DS^0$ y $FI_0 = FI^0$
  (ec. [\[eq:ci_financieras\]](#eq:ci_financieras){reference-type="ref"
  reference="eq:ci_financieras"}).

- = Período de continuación. $NIE_{T+1} = i^{DS} DS_T - i^{FI} FI_T$ no
  corresponde a ningún flujo de efectivo ni entra en $TAX_t$; alimenta
  únicamente el Término VI de la función objetivo como cargo de
  valuación terminal.

**Nota sobre el alcance temporal:** Esta es la única variable del modelo
indexada hasta $T+1$. Los índices $t = 1, \dots, T$ son gasto financiero
real, con contrapartida en efectivo dentro de $FM_t$ y con efecto fiscal
en $TAX_t$; el índice $T+1$ es un dispositivo de valuación sin
contrapartida. La separación debe respetarse en la implementación: sumar
$NIE_{T+1}$ a cualquier restricción de balance introduciría un flujo
inexistente.

## Impuesto sobre la renta (endógeno) {#sec:tax}

El impuesto se modela como variable endógena dependiente de la utilidad
antes de impuestos, garantizando que el flujo de caja de impuestos sea
consistente con los beneficios que la función objetivo reconoce.

$$\begin{equation}
    TAX_t = \tau^{isr} \cdot \big(EBIT_t - NIE_t\big) \qquad \forall\, t = 1, \dots, T
\label{eq:tax}
\tag{8}
\end{equation}$$

**Donde:**

- = Impuesto sobre la renta del período $t$ (variable libre;
  ec. [\[eq:dominio_libre\]](#eq:dominio_libre){reference-type="ref"
  reference="eq:dominio_libre"}).

- = Tasa de impuesto sobre la renta.

- = Base gravable: utilidad operativa (ya neta de depreciación vía
  $fc_t$) menos el gasto financiero neto de corto plazo.

**Por qué $NIE_t$ entra en la base gravable:** El Término V de la
función objetivo valora el costo financiero **después** de impuestos,
$NIE_t(1 - \tau^{isr})$. Para que esa valoración tenga contrapartida en
el flujo de caja, el ISR desembolsado debe reflejar el escudo fiscal
correspondiente. La verificación es inmediata: en la
ec. ([\[eq:caja\]](#eq:caja){reference-type="ref" reference="eq:caja"}),
el interés entra vía $FM_t$ con signo $-NIE_t$ y el escudo vía $-TAX_t$
con signo $+\tau^{isr} NIE_t$, de modo que el impacto neto sobre la caja
es exactamente $-NIE_t(1 - \tau^{isr})$, idéntico al Término V. Si se
mantuviera $TAX_t = \tau^{isr} EBIT_t$, la función objetivo reconocería
un escudo fiscal que el flujo de caja no recibiría.

**Consistencia agregada:** Los Términos I y V suman
$$\big(EBIT_t\big)\big(1 - \tau^{isr}\big) - NIE_t\big(1 - \tau^{isr}\big) = \big(EBIT_t - NIE_t\big)\big(1 - \tau^{isr}\big),$$
que es la utilidad neta del período antes del cargo de capital,
coherente con $TAX_t = \tau^{isr}(EBIT_t - NIE_t)$. La función objetivo
queda así interpretable como *utilidad neta menos cargo de capital sobre
el capital operativo de largo plazo*.

**Supuesto de simetría fiscal:** En esta formulación LP, $TAX_t$ puede
tomar valores negativos cuando la base gravable es negativa, lo que
equivale a un crédito fiscal inmediato. Este supuesto se relajará en la
extensión MILP mediante la restricción
$TAX_t = \tau^{isr} \cdot \max\big(0,\, EBIT_t - NIE_t\big)$,
implementada con variables binarias. Nótese que la declaración
$TAX_t \in \mathbb{R}$ de la
ec. ([\[eq:dominio_libre\]](#eq:dominio_libre){reference-type="ref"
reference="eq:dominio_libre"}) es precisamente lo que hace operativo
este supuesto.

# Restricciones físicas {#sec:fisicas}

## Balance de masa de insumos en $B_P$ {#sec:balance_bp}

$$\begin{equation}
    I_{r, B_P, t-1} + \sum_{l^S \in L^S} Y_{r, l^S, B_P, t} + Y_{r, B_G, B_P, t} - \sum_{(p, r) \in \mathit{BOM}^1} a^1_{pr} \cdot X_{pt} - I_{r, B_P, t} = 0
\label{eq:balance_bp}
\tag{9}
\end{equation}$$
$$\forall\, (r, B_P) \in PL^{Op}, \quad t = 1, \dots, T$$

**Donde:**

- = Inventario de insumo $r$ en $B_P$ al inicio del período (fin del
  período anterior).

- = Recepción de insumo $r$ desde el proveedor $l^S$ en el período $t$.
  Definida para $r \in R^E \cup R^N$ (seis arcos de entrega directa en
  $B_P$); nula para $r \in R^I$.

- = Transferencia interna de $r$ desde $B_G$ a $B_P$. **Definida para
  todo $r \in R^N \cup R^I$** (diez pares en $PC$; ver
  [2](#tab:auditoria_pc){reference-type="ref+Label"
  reference="tab:auditoria_pc"}). Se toma como cero para $r \in R^E$,
  cuyo proveedor entrega directamente en $B_P$ y que por tanto no tiene
  inventario en $B_G$.

- = Coeficiente BOM nivel 1: unidades de $r$ por unidad de semiterminado
  $p$.

- = Lanzamiento a transformación primaria del semiterminado $p$ en $t$.

- = Inventario de insumo $r$ en $B_P$ al final del período $t$.

**Nota sobre el ruteo de insumos importados:** El $\mathit{BOM}^1$
([3](#tab:bom1_detalle){reference-type="ref+Label"
reference="tab:bom1_detalle"}) consume insumos importados $r \in R^I$ en
la producción de $p_3$, $p_4$ y $p_5$, y ese consumo se descarga contra
el inventario de $B_P$. Puesto que los proveedores de $R^I$ entregan
exclusivamente en $B_G$, la **única** ruta de abastecimiento del punto
de consumo es el arco de transferencia interna $B_G \to B_P$. Restringir
dicho arco a $R^N$ dejaría a los importados sin salida de $B_G$
(ec. [\[eq:balance_bg\]](#eq:balance_bg){reference-type="ref"
reference="eq:balance_bg"} no contiene término de consumo) y sin entrada
a $B_P$, volviendo el modelo infactible para cualquier $d_{qzt} > 0$
asociada a $q_5, \dots, q_{10}$. La cardinalidad $|PC| = 61$ confirma
que el arco debe contener los diez insumos de tipo B.

## Balance de masa de insumos en $B_G$ {#sec:balance_bg}

$$\begin{equation}
    I_{r, B_G, t-1} + \sum_{l^S \in L^S} Y_{r, l^S, B_G, t} - Y_{r, B_G, B_P, t} - I_{r, B_G, t} = 0
\label{eq:balance_bg}
\tag{10}
\end{equation}$$
$$\forall\, (r, B_G) \in PL^{Op}, \quad t = 1, \dots, T$$

**Donde:**

- = Inventario de insumo $r$ en $B_G$ al inicio del período.

- = Recepción de $r$ desde el proveedor $l^S$ en $B_G$. Definida para
  $r \in R^N \cup R^I$ (diez arcos de aprovisionamiento).

- = Transferencia interna de $r$ desde $B_G$ hacia $B_P$, definida para
  los mismos diez insumos. Es el **único** término de salida del nodo:
  $B_G$ no tiene consumo productivo propio, de modo que todo insumo
  recibido en $B_G$ debe eventualmente transferirse a $B_P$ o permanecer
  en inventario.

- = Inventario de $r$ en $B_G$ al final de $t$.

**Nota:** El conjunto índice $(r, B_G) \in PL^{Op}$ contiene exactamente
diez pares ($R^N \cup R^I$), no once: el solvente $R^E$ no se almacena
en $B_G$ ([2](#tab:auditoria_pc){reference-type="ref+Label"
reference="tab:auditoria_pc"}).

## Balance del trabajo en proceso (WIP) {#sec:wip}

**Ecuación general** ($t > \delta$):

$$\begin{equation}
    W_{pt} = W_{p, t-1} + X_{pt} - X_{p, t-\delta} \qquad \forall\, p \in P, \quad t = \delta + 1, \dots, T
\label{eq:wip}
\tag{11a}
\end{equation}$$

**Ecuación de frontera** ($t = 1, \dots, \delta$):

$$\begin{equation}
    W_{pt} = W_{p, t-1} + X_{pt} - X^0_{p, t-\delta} \qquad \forall\, p \in P, \quad t = 1, \dots, \delta
\label{eq:wip_frontera}
\tag{11b}
\end{equation}$$

**Donde:**

- = WIP del semiterminado $p$ al final de $t$.

- = WIP al final del período anterior (con $W_{p,0} = W^0_p$).

- = Lanzamiento a transformación primaria en $t$ (entrada al WIP).

- = Lanzamiento realizado $\delta$ períodos antes (liberación del WIP).

- = Lanzamiento histórico previo al horizonte (parámetro de frontera,
  sustituye a $X_{p,t-\delta}$ cuando $t - \delta \leq 0$).

**Nota:** Con $\delta = 1$, la ecuación de frontera aplica sólo para
$t = 1$: $W_{p,1} = W^0_p + X_{p,1} - X^0_{p,0}$. Bajo la condición de
consistencia ([\[eq:cons_w0\]](#eq:cons_w0){reference-type="ref"
reference="eq:cons_w0"}), que con $\delta = 1$ se reduce a
$W^0_p = X^0_{p,0}$, esta expresión se simplifica a $W_{p,1} = X_{p,1}$:
el WIP heredado se libera íntegramente en la primera semana y el trabajo
en proceso al cierre de $t=1$ es únicamente el lanzamiento nuevo. La
igualdad $W_{p,1} = X_{p,1}$ constituye por tanto una prueba directa de
(C3).

## Balance de masa de semiterminados liberados en $B_{wip}$ {#sec:balance_bwip}

**Ecuación general** ($t > \delta$):

$$\begin{equation}
    I_{p, B_{wip}, t} = I_{p, B_{wip}, t-1} + X_{p, t-\delta} - Y_{p, B_{wip}, B_S, t}
\label{eq:balance_bwip}
\tag{12a}
\end{equation}$$ $$\forall\, p \in P, \quad t = \delta + 1, \dots, T$$

**Ecuación de frontera** ($t = 1, \dots, \delta$):

$$\begin{equation}
    I_{p, B_{wip}, t} = I_{p, B_{wip}, t-1} + X^0_{p, t-\delta} - Y_{p, B_{wip}, B_S, t}
\label{eq:balance_bwip_frontera}
\tag{12b}
\end{equation}$$ $$\forall\, p \in P, \quad t = 1, \dots, \delta$$

**Donde:**

- = Inventario de semiterminado liberado $p$ en $B_{wip}$ al final de
  $t$.

- = Liberación del WIP: lanzamiento realizado $\delta$ períodos antes.

- = Liberación correspondiente a un lanzamiento histórico (frontera).

- = Embarque inter-planta de $p$ desde $B_{wip}$ hacia $B_S$ en $t$
  (decisión libre del modelo; transporte instantáneo --- ver
  [5.2](#sec:supuestos_operativos){reference-type="ref+Label"
  reference="sec:supuestos_operativos"}).

## Balance de masa de semiterminados en $B_S$ {#sec:balance_bs}

$$\begin{equation}
    I_{p, B_S, t} = I_{p, B_S, t-1} + Y_{p, B_{wip}, B_S, t} - \sum_{(q, p) \in \mathit{BOM}^2} a^2_{qp} \cdot Z_{qt}
\label{eq:balance_bs}
\tag{13}
\end{equation}$$ $$\forall\, p \in P, \quad t = 1, \dots, T$$

**Donde:**

- = Inventario de semiterminado $p$ en $B_S$ al final de $t$.

- = Recepción desde $B_{wip}$ (arco inter-planta, instantáneo).

- = Coeficiente BOM nivel 2: $0.5$ para presentaciones de 500 ml, $1.0$
  para presentaciones de 1 L.

- = Producción de la presentación $q$ en transformación secundaria (en
  unidades de presentación).

**Nota:** Dado que $a^2_{qp}$ difiere entre presentaciones de 500 ml y
1 L, el consumo de semiterminado $p$ depende del *mix* de producción
entre ambos volúmenes. Por ejemplo, para el semiterminado $p_1$:
producir una unidad de $q_1$ (500 ml) consume $0.5$ unidades de $p_1$,
mientras que producir una unidad de $q_2$ (1 L) consume $1.0$ unidad.

## Balance de masa de presentaciones en $B_T$ {#sec:balance_bt}

$$\begin{equation}
    I_{q, B_T, t} = I_{q, B_T, t-1} + Z_{qt} - \sum_{z \in L^M} Y_{q, B_T, z, t}
\label{eq:balance_bt}
\tag{14}
\end{equation}$$ $$\forall\, q \in Q, \quad t = 1, \dots, T$$

**Donde:**

- = Inventario de presentación $q$ en $B_T$ al final de $t$ (en unidades
  de presentación).

- = Entrada por transformación secundaria.

- = Despacho de $q$ hacia la zona de mercado $z$.

## Inventarios inicial y final {#sec:inv_frontera}

**Condición inicial:** $$\begin{equation}
    I_{\pi l 0} = I^0_{\pi l} \qquad \forall\, (\pi, l) \in PL^{Op}
\label{eq:inv_inicial}
\tag{15a}
\end{equation}$$

**Condición terminal (desigualdad):** $$\begin{equation}
    I_{\pi l T} \geq I^T_{\pi l} \qquad \forall\, (\pi, l) \in PL^{Op}
\label{eq:inv_terminal}
\tag{15b}
\end{equation}$$

**Nota sobre la factibilidad de la meta terminal:** El parámetro
$I^T_{\pi l}$ no es libre: debe ser alcanzable con la capacidad
disponible una vez servida la demanda del horizonte. La condición
necesaria correspondiente
es ([\[eq:fact_horizonte\]](#eq:fact_horizonte){reference-type="ref"
reference="eq:fact_horizonte"}), que incorpora la meta terminal
expresada en equivalente granel.

**Nota:** La condición terminal se formula como desigualdad ($\geq$)
para permitir al optimizador terminar con niveles de inventario iguales
o superiores al objetivo mínimo $I^T_{\pi l}$. Esto proporciona mayor
flexibilidad que una igualdad estricta en la última semana del
horizonte, sin comprometer la continuidad operativa post-horizonte. El
cargo de capital en el Término II de la función objetivo
(ec. [\[eq:objetivo\]](#eq:objetivo){reference-type="ref"
reference="eq:objetivo"}) penaliza naturalmente cualquier exceso de
inventario terminal a través de $NOA_T$, evitando la acumulación
innecesaria.

Adicionalmente, las condiciones iniciales del WIP y los lanzamientos
previos: $$\begin{equation}
    W_{p, 0} = W^0_p \qquad \forall\, p \in P
\label{eq:wip_ini}
\tag{15c}
\end{equation}$$

## Capacidad de transformación primaria {#sec:cap_destilacion}

$$\begin{equation}
    \sum_{p \in P} kx_p \cdot X_{pt} \leq capX_{B_P} \qquad \forall\, t = 1, \dots, T
\label{eq:cap_destilacion}
\tag{16}
\end{equation}$$

**Donde:**

- = Horas de destilación requeridas por unidad del semiterminado $p$.

- = Lanzamiento a transformación primaria.

- = Horas semanales totales de destilación disponibles (operación 24/7).

## Capacidad de almacenamiento {#sec:cap_almacen}

$$\begin{equation}
    \sum_{\pi : (\pi, l) \in PL^{Op}} ki_\pi \cdot I_{\pi l t} \leq capI_l \qquad \forall\, l \in L^{Op}, \quad t = 1, \dots, T
\label{eq:cap_almacen}
\tag{17}
\end{equation}$$

**Donde:**

- = Capacidad de almacenamiento requerida por unidad del producto $\pi$.

- = Inventario al final de $t$ (se usa fin de período para la
  restricción de capacidad *física*, a diferencia del costo que usa
  inventario promedio).

- = Capacidad nominal del almacén $l$.

**Nota sobre el período inicial:** Esta restricción está indexada
$t = 1, \dots, T$ y por tanto **no acota el inventario inicial**. La
condición equivalente sobre $I^0_{\pi l}$ se formula como condición de
consistencia paramétrica
en ([\[eq:cons_capi0\]](#eq:cons_capi0){reference-type="ref"
reference="eq:cons_capi0"}), puesto que $I^0_{\pi l}$ es un dato y no
una decisión.

## Demanda {#sec:demanda}

$$\begin{equation}
    Y_{q, B_T, z, t} + LS_{qzt} = d_{qzt} \qquad \forall\, q \in Q, \quad z \in L^M, \quad t = 1, \dots, T
\label{eq:demanda}
\tag{18}
\end{equation}$$

**Donde:**

- = Demanda servida: cantidad de $q$ despachada a la zona $z$ en $t$
  (unidades de presentación).

- = Demanda insatisfecha (ventas perdidas).

- = Demanda determinista de $q$ en $z$ en $t$ (parámetro; unidades de
  presentación).

## Regularización del plan de producción {#sec:regularizacion}

La regularización descompone el cambio absoluto en producción entre
períodos consecutivos y penaliza ambas direcciones de variación en la
función objetivo.

**Ecuación general** ($t = 2, \dots, T$): $$\begin{equation}
    X_{pt} - X_{p, t-1} = \Delta^+_{pt} - \Delta^-_{pt} \qquad \forall\, p \in P, \quad t = 2, \dots, T
\label{eq:regularizacion}
\tag{19a}
\end{equation}$$

**Ecuación de frontera** ($t = 1$): $$\begin{equation}
    X_{p, 1} - X^{ref}_p = \Delta^+_{p, 1} - \Delta^-_{p, 1} \qquad \forall\, p \in P
\label{eq:regularizacion_frontera}
\tag{19b}
\end{equation}$$

**Donde:**

- = Incremento en producción de $p$ respecto al período anterior
  ($\geq 0$).

- = Decremento en producción de $p$ respecto al período anterior
  ($\geq 0$).

- = Nivel de producción de referencia previo al horizonte (parámetro),
  sujeto a la condición de
  consistencia ([\[eq:cons_xref\]](#eq:cons_xref){reference-type="ref"
  reference="eq:cons_xref"}): debe coincidir con el último lanzamiento
  histórico $X^0_{p,0}$.

**Nota:** Dado que $\rho > 0$ en la función objetivo, el solver nunca
asignará simultáneamente $\Delta^+_{pt} > 0$ y $\Delta^-_{pt} > 0$ en la
solución óptima, pues podría reducir ambas en la misma magnitud sin
violar la igualdad y mejorando el objetivo. Por tanto,
$\Delta^+_{pt} + \Delta^-_{pt} = |X_{pt} - X_{p,t-1}|$ en toda solución
óptima.

## No negatividad de variables físicas {#sec:noneg_fisicas}

$$\begin{equation}
    I_{\pi l t},\; X_{pt},\; W_{pt},\; Z_{qt},\; Y_{\pi l j t},\; LS_{qzt},\; \Delta^+_{pt},\; \Delta^-_{pt} \geq 0
\label{eq:noneg_fisicas}
\tag{20}
\end{equation}$$

# Restricciones financieras {#sec:financieras}

## Operadores de historial {#sec:operadores_historial}

Las ecuaciones de cuentas abiertas involucran términos rezagados que, en
los primeros períodos del horizonte, corresponden a transacciones
anteriores al inicio de la planeación. En lugar de particionar cada
ecuación en casos general y de frontera --- práctica que en la versión
anterior dejó un **hueco de cobertura** en el intervalo
$t \in (\tau^{pay}_N,\, \tau^{pay}_I]$ cuando
$\tau^{pay}_I > \tau^{pay}_N$ --- se definen dos operadores que unifican
variable y parámetro según el signo del índice temporal.

**Operador de cobros (ventas realizadas en el período $s$):**
$$\begin{equation}
    \widehat{S}_{z,s} \;\equiv\;
    \begin{cases}
        \displaystyle\sum_{q \in Q} Y_{q, B_T, z, s} \cdot v_q & \text{si } s \geq 1 \quad \text{(venta decidida dentro del horizonte)} \\[1.2em]
        S^0_{z,s} & \text{si } s \leq 0 \quad \text{(venta histórica, parámetro)}
    \end{cases}
\label{eq:op_cobros}
\tag{H1}
\end{equation}$$

**Operador de pagos (compras realizadas en el período $s$):**
$$\begin{equation}
    \widehat{B}_{r,s} \;\equiv\;
    \begin{cases}
        \displaystyle\sum_{(l^S, l)\,:\,(r,(l^S,l)) \in PC} Y_{r, l^S, l, s} \cdot e_r & \text{si } s \geq 1 \quad \text{(compra decidida dentro del horizonte)} \\[1.2em]
        B^0_{r,s} & \text{si } s \leq 0 \quad \text{(compra histórica, parámetro)}
    \end{cases}
\label{eq:op_pagos}
\tag{H2}
\end{equation}$$

**Propiedades de los operadores:**

- **No son variables nuevas.** $\widehat{S}$ y $\widehat{B}$ son
  notación abreviada: para $s \geq 1$ representan una combinación lineal
  de variables de decisión existentes; para $s \leq 0$ son constantes
  conocidas. El modelo no gana ni pierde grados de libertad, y la
  sustitución preserva la linealidad.

- **Selección resuelta en tiempo de construcción.** La disyunción por
  casos se evalúa sobre el índice $s$, que es un dato estructural
  conocido antes de resolver. No se traduce en ninguna restricción
  disyuntiva ni requiere variables binarias: el generador del modelo
  simplemente elige qué expresión ensamblar en cada celda.

- **Cobertura total del horizonte.** Cada ecuación que los emplea queda
  definida para $t = 1, \dots, T$ sin particiones, lo que elimina por
  construcción cualquier hueco o traslape entre rangos de validez.

- **Consistencia estructural entre ecuaciones.** El término de descarga
  de $AR_{zt}$ en ([\[eq:cxc\]](#eq:cxc){reference-type="ref"
  reference="eq:cxc"}), el de descarga de $AP_t$
  en ([\[eq:cxp\]](#eq:cxp){reference-type="ref" reference="eq:cxp"}) y
  los flujos de $OM_t$ en ([\[eq:om\]](#eq:om){reference-type="ref"
  reference="eq:om"}) quedan expresados con **exactamente los mismos
  operadores**. La coincidencia entre devengo y efectivo deja de ser una
  coincidencia verificable *a posteriori* y pasa a ser una propiedad
  estructural del modelo.

**Implementación:** Se recomienda codificarlos como funciones auxiliares
del generador del modelo, por ejemplo en Pyomo:

> def S_hist(m, z, s):\
> return sum(m.Y\[q, BT, z, s\]\*m.v\[q\] for q in m.Q) if s \>= 1 else
> m.S0\[z, s\]

## Saldo de cuentas por cobrar por zona {#sec:cxc}

$$\begin{equation}
    AR_{zt} = AR_{z, t-1} + \sum_{q \in Q} Y_{q, B_T, z, t} \cdot v_q - \widehat{S}_{z,\, t-\tau_z}
    \qquad \forall\, z \in L^M, \quad t = 1, \dots, T
\label{eq:cxc}
\tag{21}
\end{equation}$$

**Donde:**

- = Saldo de cuentas por cobrar de la zona $z$ al final de $t$.

- = Devengo: ventas generadas en $t$, que se convierten en CxC.

- = Cobro: monto de las ventas realizadas $\tau_z$ períodos antes, cuyo
  plazo vence en $t$. Por la
  definición ([\[eq:op_cobros\]](#eq:op_cobros){reference-type="ref"
  reference="eq:op_cobros"}), corresponde a la variable
  $\sum_q Y_{q,B_T,z,t-\tau_z} v_q$ cuando $t > \tau_z$ y al parámetro
  $S^0_{z,t-\tau_z}$ cuando $t \leq \tau_z$.

- = Condición inicial.

**Nota:** Una única ecuación cubre $t = 1, \dots, T$ para las tres
zonas, con independencia de que $\tau_{z^{loc}} = 2$,
$\tau_{z^{for}} = 4$ y $\tau_{z^{exp}} = 6$ sean distintos entre sí. La
heterogeneidad de plazos queda absorbida por el índice $t - \tau_z$ del
operador.

## Saldo de cuentas por pagar {#sec:cxp}

$$\begin{equation}
    AP_t = AP_{t-1}
    + \underbrace{\sum_{(r,(l^S,l)) \in PC} Y_{r, l^S, l, t} \cdot e_r}_{\text{devengo: compras de } t}
    - \underbrace{\sum_{k \in K} \; \sum_{r \in R^{(k)}} \widehat{B}_{r,\, t - \tau^{pay}_k}}_{\text{pagos que vencen en } t}
    \qquad \forall\, t = 1, \dots, T
\label{eq:cxp}
\tag{22}
\end{equation}$$

**Donde:**

- = Saldo de cuentas por pagar al final de $t$.

- = Clases de pago, con $R^{(N)} = R^N \cup R^E$ (plazo $\tau^{pay}_N$)
  y $R^{(I)} = R^I$ (plazo $\tau^{pay}_I$).

- = Monto de las compras del insumo $r$ realizadas $\tau^{pay}_k$
  períodos antes, cuyo pago vence en $t$; variable si el índice cae
  dentro del horizonte, parámetro $B^0_{r,s}$ si cae antes del inicio
  (ec. [\[eq:op_pagos\]](#eq:op_pagos){reference-type="ref"
  reference="eq:op_pagos"}).

- = Condición inicial.

**Corrección respecto a la versión anterior:** La formulación previa
partía esta ecuación en un caso general válido para
$t > \max(\tau^{pay}_N, \tau^{pay}_I)$ y un caso de frontera válido para
$t \leq \tau^{pay}_N$. Cuando $\tau^{pay}_I > \tau^{pay}_N$ --- el
escenario realista, pues el crédito de proveedores extranjeros suele ser
más largo --- los períodos $t \in (\tau^{pay}_N, \tau^{pay}_I]$ quedaban
**sin ecuación de balance**, dejando $AP_t$ subdeterminado en esa
ventana y permitiendo que el optimizador la fijara arbitrariamente. La
formulación unificada elimina el problema por construcción.

**Propiedad de conservación (pago único):** Puesto que
$\{R^{(N)}, R^{(I)}\}$ es una partición de $R$
([5.2](#sec:supuestos_operativos){reference-type="ref+Label"
reference="sec:supuestos_operativos"}), la doble suma
$\sum_{k \in K}\sum_{r \in R^{(k)}}$ recorre cada insumo exactamente una
vez. En consecuencia, cada peso devengado como cuenta por pagar en el
período $s$ se descarga exactamente una vez, en el período
$s + \tau^{pay}_{k(r)}$. Formalmente,
sumando ([\[eq:cxp\]](#eq:cxp){reference-type="ref" reference="eq:cxp"})
sobre $t = 1, \dots, T$:
$$AP_T - AP^0 = \sum_{t=1}^{T} \sum_{(r,(l^S,l)) \in PC} Y_{r,l^S,l,t}\, e_r \;-\; \sum_{t=1}^{T} \sum_{k \in K} \sum_{r \in R^{(k)}} \widehat{B}_{r,\,t-\tau^{pay}_k}$$
es decir, el saldo terminal es exactamente el monto comprado y aún no
vencido al cierre del horizonte. Esta identidad constituye una prueba de
verificación útil para la instancia miniatura de validación.

## Límite de crédito comercial con proveedores {#sec:ap_max}

Los plazos de pago a proveedores constituyen restricciones duras que
deben respetarse. La dinámica de la
ecuación ([\[eq:cxp\]](#eq:cxp){reference-type="ref"
reference="eq:cxp"}) ya impone que los pagos se realicen exactamente en
la fecha de vencimiento. Adicionalmente, se limita la exposición total
acumulada con proveedores para reflejar el límite de crédito comercial
agregado.

$$\begin{equation}
    AP_t \leq AP^{max} \qquad \forall\, t = 1, \dots, T
\label{eq:ap_max}
\tag{23}
\end{equation}$$

**Donde:**

- = Saldo de cuentas por pagar al final de $t$.

- = Exposición máxima acumulada con proveedores (parámetro).

**Nota:** Esta restricción, en conjunto con la
ecuación ([\[eq:cxp\]](#eq:cxp){reference-type="ref"
reference="eq:cxp"}), asegura que los proveedores reciben su pago
exactamente al vencer el plazo acordado ($\tau^{pay}_N$ o
$\tau^{pay}_I$) y que el volumen total de compras a crédito no excede el
límite de crédito comercial disponible. Con la formulación unificada, la
cota es ahora efectiva en **todos** los períodos del horizonte, incluida
la ventana $t \in (\tau^{pay}_N, \tau^{pay}_I]$ que antes quedaba sin
balance.

## Balance de caja {#sec:caja}

El balance de caja incorpora el impuesto como variable endógena
($TAX_t$), separándolo del flujo exógeno $ec'_t$. Esto garantiza que el
impuesto pagado en efectivo sea consistente con la utilidad operativa
que determina el NOPAT en la función objetivo.

$$\begin{equation}
    C_t = C_{t-1} - OCF_t + OM_t + FM_t - TAX_t - ec'_t \qquad \forall\, t = 1, \dots, T
\label{eq:caja}
\tag{24}
\end{equation}$$

**Donde:**

- = Posición de caja al final de $t$; $C_0 = C^0$.

- = Egresos operativos en efectivo (siempre $\geq 0$ por la
  ec. [\[eq:dominio_noneg\]](#eq:dominio_noneg){reference-type="ref"
  reference="eq:dominio_noneg"}; **resta** caja;
  ec. [\[eq:ocf\]](#eq:ocf){reference-type="ref" reference="eq:ocf"}).

- = Flujo neto de cuentas abiertas: cobros CxC menos pagos CxP (variable
  libre, puede ser $\gtrless 0$;
  ec. [\[eq:om\]](#eq:om){reference-type="ref" reference="eq:om"}).

- = Flujo neto financiero: recuperación de inversiones y nueva deuda,
  menos nuevas inversiones y amortización de deuda (variable libre,
  puede ser $\gtrless 0$; ec. [\[eq:fm\]](#eq:fm){reference-type="ref"
  reference="eq:fm"}).

- = Impuesto endógeno sobre la base $EBIT_t - NIE_t$
  (ec. [\[eq:tax\]](#eq:tax){reference-type="ref" reference="eq:tax"});
  variable libre, puede ser negativo bajo simetría fiscal.

- = Flujo de caja exógeno **sin ISR y sin depreciación**:
  $ec'_t = fc^{caja}_t + int^{LP}_t(1-\tau^{isr}) + div_t$
  (ec. [\[eq:ec_prima\]](#eq:ec_prima){reference-type="ref"
  reference="eq:ec_prima"}).

**Nota sobre la depreciación:** La depreciación $dep^{sem}$ forma parte
de $fc_t$ y por tanto de $EBIT_t$ y de la base gravable, pero **no**
aparece en este balance porque no es desembolsable. Su único efecto
sobre la caja es indirecto y correcto: reduce $TAX_t$ en
$\tau^{isr} \cdot dep^{sem}$ por período.

**Nota sobre el interés de corto plazo:** El interés neto entra dos
veces con signos opuestos y magnitudes distintas: como salida completa
dentro de $FM_t$ ($-NIE_t$) y como escudo fiscal dentro de $-TAX_t$
($+\tau^{isr} NIE_t$). El efecto neto sobre la caja,
$-NIE_t(1-\tau^{isr})$, coincide exactamente con el Término V de la
función objetivo. Esta coincidencia es la prueba de consistencia entre
devengo y efectivo del bloque financiero.

**Nota sobre signos:** La lectura del balance depende críticamente de
las declaraciones de dominio de la
ec. ([\[eq:dominio_libre\]](#eq:dominio_libre){reference-type="ref"
reference="eq:dominio_libre"}). $OCF_t$ entra restando *porque* está
acotado a valores no negativos; $OM_t$, $FM_t$ y $TAX_t$ entran con
signo fijo en la ecuación pero con valor de signo libre, de modo que la
dirección efectiva del flujo la determina la solución.

## Flujo de caja de operaciones {#sec:ocf}

Cubre los pagos en efectivo por transformación primaria, transformación
secundaria, almacenamiento (sobre inventario promedio) y transporte
interno. Las compras de insumos se pagan vía CxP en $OM_t$.

$$\begin{equation}
\begin{aligned}
    OCF_t = \;& \sum_{p \in P} X_{pt} \cdot kx_p \cdot cd_{B_P} + \sum_{q \in Q} Z_{qt} \cdot cc_q \\
    & + \sum_{\substack{(\pi, (l, j)) \in PC \\ l \notin L^S}} Y_{\pi l j t} \cdot ky_\pi \cdot cy_{lj} \\
    & + \sum_{(\pi, l) \in PL^{Op}} \frac{1}{2}\big(I_{\pi l, t-1} + I_{\pi l t}\big) \cdot ki_\pi \cdot ci_l + \sum_{p \in P} \frac{1}{2}\big(W_{p, t-1} + W_{pt}\big) \cdot cw
\end{aligned}
\label{eq:ocf}
\tag{25}
\end{equation}$$ $$\forall\, t = 1, \dots, T$$

**Donde:** Cada término corresponde a la misma definición que en la
ecuación de $VCO_t$ (ec. [\[eq:vco\]](#eq:vco){reference-type="ref"
reference="eq:vco"}), excluyendo el componente (i) de materias primas
(que se paga vía CxP con plazo diferido). El término de transporte
recorre los 45 pares de arcos internos, incluido el arco $B_G \to B_P$
para los diez insumos de tipo B. Los arcos desde proveedores
($l \in L^S$) se excluyen porque su costo de flete está incluido en
$e_r$.

## Gestión de cuentas abiertas {#sec:om}

$$\begin{equation}
    OM_t = \underbrace{\sum_{z \in L^M} \widehat{S}_{z,\, t-\tau_z}}_{\text{cobros que vencen en } t}
    \;-\; \underbrace{\sum_{k \in K} \; \sum_{r \in R^{(k)}} \widehat{B}_{r,\, t-\tau^{pay}_k}}_{\text{pagos que vencen en } t}
    \qquad \forall\, t = 1, \dots, T
\label{eq:om}
\tag{26}
\end{equation}$$

**Donde:**

- = Cobros: ventas de la zona $z$ cuyo plazo vence en $t$; variable si
  $t > \tau_z$, parámetro $S^0_{z,t-\tau_z}$ en caso contrario
  (ec. [\[eq:op_cobros\]](#eq:op_cobros){reference-type="ref"
  reference="eq:op_cobros"}).

- = Pagos: compras del insumo $r$ cuyo plazo vence en $t$; variable si
  $t > \tau^{pay}_k$, parámetro $B^0_{r,t-\tau^{pay}_k}$ en caso
  contrario (ec. [\[eq:op_pagos\]](#eq:op_pagos){reference-type="ref"
  reference="eq:op_pagos"}).

- = Variable libre de signo
  (ec. [\[eq:dominio_libre\]](#eq:dominio_libre){reference-type="ref"
  reference="eq:dominio_libre"}): negativa en semanas de pago
  concentrado.

**Nota sobre la articulación devengado--efectivo:**
Comparando ([\[eq:om\]](#eq:om){reference-type="ref" reference="eq:om"})
con ([\[eq:cxc\]](#eq:cxc){reference-type="ref" reference="eq:cxc"})
y ([\[eq:cxp\]](#eq:cxp){reference-type="ref" reference="eq:cxp"}), el
término de cobros de $OM_t$ es **idénticamente** el término de descarga
de $AR_{zt}$ sumado sobre las zonas, y el término de pagos es
**idénticamente** el término de descarga de $AP_t$. Esta identidad
garantiza, por construcción, que no existe fuga ni doble conteo entre el
devengo de las cuentas abiertas y su realización en efectivo.
Formalmente:
$$OM_t \;=\; -\Big[\textstyle\sum_{z} \big(AR_{zt} - AR_{z,t-1}\big) - \textstyle\sum_z \textstyle\sum_q Y_{q,B_T,z,t} v_q\Big] \;-\; \Big[\big(AP_t - AP_{t-1}\big) - \textstyle\sum_{(r,(l^S,l))} Y_{r,l^S,l,t}\, e_r\Big]$$
que es la relación estándar entre variación de capital de trabajo
operativo y flujo de efectivo.

## Gestión financiera {#sec:fm}

$$\begin{equation}
    FM_t = FI_{t-1} \cdot (1 + i^{FI}) - FI_t - DS_{t-1} \cdot (1 + i^{DS}) + DS_t \qquad \forall\, t = 1, \dots, T
\label{eq:fm}
\tag{27}
\end{equation}$$

**Donde:**

- = Recuperación de inversiones con intereses (**entrada** de caja).

- = Nueva colocación de inversiones (**salida** de caja).

- = Amortización de deuda con intereses (**salida** de caja).

- = Contratación de nueva deuda (**entrada** de caja).

- = Variable libre de signo
  (ec. [\[eq:dominio_libre\]](#eq:dominio_libre){reference-type="ref"
  reference="eq:dominio_libre"}).

**Nota sobre la separación del costo de deuda:** Conforme a la base de
capital de la ec. ([\[eq:noa\]](#eq:noa){reference-type="ref"
reference="eq:noa"}), $DS_t$ no forma parte de $NOA_t$ y el WACC ---
calibrado sobre la estructura de largo plazo
([6.2](#sec:consistencia_wacc){reference-type="ref+Label"
reference="sec:consistencia_wacc"}) --- no cobra cargo de capital sobre
la deuda bancaria. El costo financiero de $DS_t$ se captura en dos
lugares complementarios y no redundantes: en **efectivo** dentro de
$FM_t$, y en **valor** dentro del Término V de la función objetivo vía
$NIE_t$. Obsérvese que $FM_t$ y $NIE_t$ comparten los mismos saldos de
apertura $DS_{t-1}$ y $FI_{t-1}$: la componente de intereses de $FM_t$
es exactamente $-NIE_t$, y el resto de $FM_t$ corresponde a movimientos
de principal.

**Descomposición explícita:** Reescribiendo la
ec. ([\[eq:fm\]](#eq:fm){reference-type="ref" reference="eq:fm"}) para
separar principal e interés,
$$FM_t = \underbrace{\big(FI_{t-1} - FI_t\big) - \big(DS_{t-1} - DS_t\big)}_{\text{movimientos de principal}} \;-\; \underbrace{\big(i^{DS} DS_{t-1} - i^{FI} FI_{t-1}\big)}_{= \; NIE_t},$$
lo que hace evidente que el Término V de la función objetivo penaliza
precisamente la componente de interés de este flujo, después de su
escudo fiscal.

## Caja mínima {#sec:caja_min}

$$\begin{equation}
    C_t \geq C^{min} \qquad \forall\, t = 1, \dots, T
\label{eq:caja_min}
\tag{28}
\end{equation}$$

## Línea de crédito máxima {#sec:credito}

$$\begin{equation}
    DS_t \leq DS^{max} \qquad \forall\, t = 1, \dots, T
\label{eq:credito}
\tag{29}
\end{equation}$$

## Condiciones iniciales financieras {#sec:ci_financieras}

$$\begin{equation}
    FI_0 = FI^0, \quad AR_{z, 0} = AR^0_z \;\; \forall\, z \in L^M, \quad C_0 = C^0, \quad DS_0 = DS^0, \quad AP_0 = AP^0
\label{eq:ci_financieras}
\tag{30}
\end{equation}$$

## No negatividad de variables financieras {#sec:noneg_financieras}

$$\begin{equation}
    FI_t,\; AR_{zt},\; C_t,\; DS_t,\; AP_t \geq 0 \qquad \forall\, z \in L^M, \quad t = 1, \dots, T
\label{eq:noneg_financieras}
\tag{31}
\end{equation}$$

**Nota:** Esta cota aplica a las **variables de decisión** financieras.
Las variables auxiliares $OM_t$, $FM_t$, $TAX_t$ y $NOA_t$ quedan
explícitamente **fuera** de este conjunto y se declaran libres de signo
en la
ec. ([\[eq:dominio_libre\]](#eq:dominio_libre){reference-type="ref"
reference="eq:dominio_libre"}).

# Enfoque secuencial {#sec:secuencial}

Se define un esquema secuencial de dos pasos como punto de comparación
con el modelo integrado.

## SEQ-1: Optimización del flujo físico {#sec:seq1}

$$\begin{equation}
    \max \sum_{t=1}^{T} TCM_t
\label{eq:seq1}
\tag{32}
\end{equation}$$

Las restricciones activas en SEQ-1 son:
([\[eq:dominio_libre\]](#eq:dominio_libre){reference-type="ref"
reference="eq:dominio_libre"}),
([\[eq:dominio_noneg\]](#eq:dominio_noneg){reference-type="ref"
reference="eq:dominio_noneg"}),
([\[eq:tcm\]](#eq:tcm){reference-type="ref" reference="eq:tcm"}),
([\[eq:ns\]](#eq:ns){reference-type="ref" reference="eq:ns"}),
([\[eq:ic\]](#eq:ic){reference-type="ref" reference="eq:ic"}),
([\[eq:vco\]](#eq:vco){reference-type="ref" reference="eq:vco"}) y
([\[eq:balance_bp\]](#eq:balance_bp){reference-type="ref"
reference="eq:balance_bp"}) a
([\[eq:noneg_fisicas\]](#eq:noneg_fisicas){reference-type="ref"
reference="eq:noneg_fisicas"}). No intervienen $EBIT_t$, $NIE_t$ ni
$TAX_t$, puesto que SEQ-1 ignora por construcción los costos fijos, la
carga fiscal y el bloque financiero.

## SEQ-2: Minimización del costo financiero neto {#sec:seq2}

El segundo modelo minimiza el costo financiero neto **después de
impuestos** a lo largo del horizonte, incluido el período de
continuación. Los intereses se devengan sobre los saldos de apertura, en
consistencia con la convención de la
[6.1](#sec:convencion_cargos){reference-type="ref+Label"
reference="sec:convencion_cargos"} y con la ecuación de gestión
financiera (ec. [\[eq:fm\]](#eq:fm){reference-type="ref"
reference="eq:fm"}). Las variables físicas se fijan a los valores
óptimos de SEQ-1.

$$\begin{equation}
    \min \sum_{t=1}^{T+1} NIE_t \cdot \big(1 - \tau^{isr}\big)
    \qquad \text{con} \qquad NIE_t = i^{DS} \cdot DS_{t-1} - i^{FI} \cdot FI_{t-1}
\label{eq:seq2_obj}
\tag{33}
\end{equation}$$

$$\begin{equation}
    I_{\pi l t} = I^*_{\pi l t}, \quad X_{pt} = X^*_{pt}, \quad W_{pt} = W^*_{pt}, \quad Z_{qt} = Z^*_{qt}, \quad Y_{\pi l j t} = Y^*_{\pi l j t}
\label{eq:seq2_fix}
\tag{34}
\end{equation}$$

**Donde:**

- = Gasto financiero neto devengado en $t$ sobre los saldos de apertura
  (ec. [\[eq:nie\]](#eq:nie){reference-type="ref" reference="eq:nie"}).

- = Período de continuación, que penaliza las posiciones $DS_T$ y $FI_T$
  dejadas abiertas al cierre y elimina la degeneración terminal también
  en el modelo secuencial.

- = Valores óptimos obtenidos en SEQ-1.

**Nota sobre la convención de saldos:** El uso de $DS_{t-1}$ y
$FI_{t-1}$ asegura que los intereses se calculan sobre el saldo que
efectivamente estuvo vigente durante el período $t$, en coherencia con
la ecuación de gestión financiera
(ec. [\[eq:fm\]](#eq:fm){reference-type="ref" reference="eq:fm"}), donde
el flujo de caja refleja la recuperación $FI_{t-1}(1 + i^{FI})$ y la
amortización $DS_{t-1}(1 + i^{DS})$. Para $t = 1$, los saldos son
$DS_0 = DS^0$ y $FI_0 = FI^0$.

**Nota sobre la conmensurabilidad con el modelo integrado:** Con la
incorporación del Término V, el objetivo de SEQ-2 deja de medir una
magnitud ajena al modelo integrado y pasa a ser **literalmente el
negativo de los Términos V y de la componente financiera del
Término VI** de la
ec. ([\[eq:objetivo\]](#eq:objetivo){reference-type="ref"
reference="eq:objetivo"}). En la versión anterior el modelo integrado no
contenía ningún término de costo financiero, de modo que SEQ-2
minimizaba una magnitud que el modelo con el que se le comparaba ni
siquiera medía, y la brecha entre ambos enfoques no era interpretable.
La comparación correcta consiste en evaluar la solución secuencial ---
flujo físico de SEQ-1 más plan financiero de SEQ-2 --- **con la función
objetivo completa del modelo integrado**, y reportar
$$\text{Brecha} = \text{EVA}^{\text{INT}} - \text{EVA}\big(\text{SEQ-1} \cup \text{SEQ-2}\big) \;\geq\; 0.$$
La desigualdad es débil por construcción, puesto que la solución
secuencial es factible para el modelo integrado siempre que SEQ-2
resulte factible. La magnitud de la brecha es el resultado que justifica
la integración.

Las restricciones activas en SEQ-2 son:
([\[eq:dominio_libre\]](#eq:dominio_libre){reference-type="ref"
reference="eq:dominio_libre"}),
([\[eq:dominio_noneg\]](#eq:dominio_noneg){reference-type="ref"
reference="eq:dominio_noneg"}),
([\[eq:op_cobros\]](#eq:op_cobros){reference-type="ref"
reference="eq:op_cobros"}),
([\[eq:op_pagos\]](#eq:op_pagos){reference-type="ref"
reference="eq:op_pagos"}), ([\[eq:noa\]](#eq:noa){reference-type="ref"
reference="eq:noa"}), ([\[eq:nie\]](#eq:nie){reference-type="ref"
reference="eq:nie"}), ([\[eq:tax\]](#eq:tax){reference-type="ref"
reference="eq:tax"}), ([\[eq:cxc\]](#eq:cxc){reference-type="ref"
reference="eq:cxc"}) a
([\[eq:noneg_financieras\]](#eq:noneg_financieras){reference-type="ref"
reference="eq:noneg_financieras"}), y la restricción de fijación
([\[eq:seq2_fix\]](#eq:seq2_fix){reference-type="ref"
reference="eq:seq2_fix"}).

# Condiciones de factibilidad agregada {#sec:factibilidad_capacidad}

Antes de resolver la instancia completa conviene establecer si el
servicio total de la demanda es siquiera posible. Estas condiciones son
**necesarias**: si alguna falla, el óptimo tendrá forzosamente
$LS_{qzt} > 0$ y la verificación de $p^{lost}_{qz}$ de la
[6.3](#sec:consistencia_plost){reference-type="ref+Label"
reference="sec:consistencia_plost"} arrojaría un falso positivo --- se
observaría demanda insatisfecha y se atribuiría a una mala calibración
de la penalización cuando en realidad se trata de una restricción
física. Ninguna de ellas se añade al LP: todas son implicadas por las
restricciones ya formuladas y se emplean como diagnóstico previo.

## Requerimiento agregado en equivalente granel {#sec:req_granel}

La demanda está expresada en unidades de presentación, mientras que la
capacidad de transformación primaria se consume en unidades de granel.
La conversión es el coeficiente $a^2_{qp}$, que difiere entre las
presentaciones de 500 ml y de 1 L. Se define el requerimiento de granel
del semiterminado $p$ en el período $t$:

$$\begin{equation}
    h_{pt} \;=\; \sum_{q\,:\,(q,p) \in \mathit{BOM}^2} \; \sum_{z \in L^M} a^2_{qp} \cdot d_{qzt}
    \qquad \forall\, p \in P, \quad t = 1, \dots, T
\label{eq:req_granel}
\tag{V1}
\end{equation}$$

El *pipeline* inicial disponible para servir demanda, también en
equivalente granel, agrega todas las posiciones que ya han absorbido la
transformación primaria o están a punto de hacerlo:

$$\begin{equation}
    G^0_p \;=\; \underbrace{W^0_p}_{\text{en proceso}} \;+\; \underbrace{I^0_{p, B_{wip}} + I^0_{p, B_S}}_{\text{granel liberado}} \;+\; \underbrace{\sum_{q\,:\,(q,p) \in \mathit{BOM}^2} a^2_{qp} \cdot I^0_{q, B_T}}_{\text{terminado, en equivalente granel}}
    \qquad \forall\, p \in P
\label{eq:pipeline_inicial}
\tag{V2}
\end{equation}$$

Análogamente, la meta terminal exigida
por ([\[eq:inv_terminal\]](#eq:inv_terminal){reference-type="ref"
reference="eq:inv_terminal"}) expresada en equivalente granel:
$$g^T_p \;=\; I^T_{p, B_{wip}} + I^T_{p, B_S} + \sum_{q\,:\,(q,p) \in \mathit{BOM}^2} a^2_{qp} \cdot I^T_{q, B_T}
    \qquad \forall\, p \in P$$

**Nota sobre $W^0_p$
en ([\[eq:pipeline_inicial\]](#eq:pipeline_inicial){reference-type="ref"
reference="eq:pipeline_inicial"}):** El WIP inicial cuenta como
disponible porque, por la
condición ([\[eq:cons_w0\]](#eq:cons_w0){reference-type="ref"
reference="eq:cons_w0"}), corresponde a lanzamientos ya realizados que
se liberarán dentro de los primeros $\delta$ períodos sin consumir
capacidad futura. Incluirlo es lo que evita que la prueba resulte
innecesariamente conservadora en las primeras semanas.

## Condición de arranque {#sec:cond_arranque}

Durante los primeros $\delta$ períodos ningún lanzamiento decidido
dentro del horizonte ha llegado todavía a liberarse. La demanda de esas
semanas sólo puede servirse desde el *pipeline* inicial:

$$\begin{equation}
    \sum_{s=1}^{t} h_{ps} \;\leq\; G^0_p \qquad \forall\, p \in P, \quad t = 1, \dots, \delta
\label{eq:fact_arranque}
\tag{V3}
\end{equation}$$

Esta condición es **por producto** y no admite compensación entre
semiterminados: el granel de $p_1$ no puede servir demanda de
presentaciones derivadas de $p_2$. Con $\delta = 1$ se reduce a
$h_{p,1} \leq G^0_p$.

## Condición acumulada de capacidad {#sec:cond_acumulada}

Para $t > \delta$, el material servido hasta $t$ proviene del *pipeline*
inicial más los lanzamientos realizados hasta $t - \delta$. Como la
capacidad de destilación es **compartida** entre semiterminados, la
condición debe agregarse en horas y no en unidades:

$$\begin{equation}
    \sum_{p \in P} kx_p \cdot \max\!\Big(0,\; \sum_{s=1}^{t} h_{ps} - G^0_p\Big)
    \;\leq\; (t - \delta) \cdot capX_{B_P}
    \qquad \forall\, t = \delta+1, \dots, T
\label{eq:fact_acumulada}
\tag{V4}
\end{equation}$$

El operador $\max(0,\cdot)$ es esencial: un semiterminado con *pipeline*
inicial excedente no libera capacidad para los demás, de modo que su
aportación al requerimiento de horas se trunca en cero en lugar de
volverse negativa.

La formulación **acumulada** es la correcta, no la períodica. Una
condición del tipo $\sum_p kx_p h_{pt} \leq capX_{B_P}$ para cada $t$
sería innecesariamente restrictiva: el modelo puede anticipar producción
y absorber picos de demanda con inventario, precisamente el mecanismo
que la política *make-to-stock* habilita. La condición acumulada respeta
esa flexibilidad y sigue siendo necesaria.

## Condición de horizonte completo {#sec:cond_horizonte}

La condición anterior evaluada en $t = T$ ignora que el plan debe además
dejar el inventario terminal exigido. Incorporándolo:

$$\begin{equation}
    \sum_{p \in P} kx_p \cdot \max\!\Big(0,\; \sum_{s=1}^{T} h_{ps} + g^T_p - G^0_p\Big)
    \;\leq\; (T - \delta) \cdot capX_{B_P}
\label{eq:fact_horizonte}
\tag{V5}
\end{equation}$$

## Condición de dimensionamiento del crédito comercial {#sec:cond_credito}

La restricción ([\[eq:ap_max\]](#eq:ap_max){reference-type="ref"
reference="eq:ap_max"}) puede volverse la restricción activa del
problema sin que ello sea evidente en los datos. El saldo de cuentas por
pagar en régimen estacionario equivale al valor de las compras de las
últimas $\tau^{pay}_k$ semanas de cada clase. Empleando el requerimiento
medio de granel $\bar h_p = \tfrac{1}{T}\sum_t h_{pt}$ como estimador
del nivel de lanzamientos, la condición de dimensionamiento es:

$$\begin{equation}
    \sum_{k \in K} \; \tau^{pay}_k \cdot \sum_{r \in R^{(k)}} \; \sum_{p \in P} a^1_{pr} \cdot e_r \cdot \bar h_p
    \;\lesssim\; AP^{max}
\label{eq:fact_credito}
\tag{V6}
\end{equation}$$

Se escribe con $\lesssim$ porque es una condición de **orden de
magnitud** y no una cota exacta: las compras son decisiones del modelo y
pueden desviarse del nivel medio. Su utilidad es diagnóstica. Si el lado
izquierdo se aproxima a $AP^{max}$, el plan estará limitado por el
crédito comercial antes que por la capacidad física, lo que conviene
saber **antes** de interpretar los resultados. Un razonamiento análogo
aplica a $capI_l$ frente al inventario de ciclo implícito y a $DS^{max}$
frente al déficit de caja acumulado durante las primeras $\max_z \tau_z$
semanas, cuando aún no se cobran las ventas del horizonte.

## Nota sobre la declaración de $capX_{B_P}$ {#sec:nota_capx}

El documento asume operación continua. Con una sola línea de
destilación, $capX_{B_P} \leq 168$ horas semanales, y con
$kx_p \in [1.20,\, 2.50]$ el techo de producción del portafolio completo
queda entre $67$ y $140$ unidades de granel por semana. Es un rango
estrecho que debe contrastarse explícitamente con $\sum_p h_{pt}$
mediante ([\[eq:fact_acumulada\]](#eq:fact_acumulada){reference-type="ref"
reference="eq:fact_acumulada"}). Si el contraste falla, la instancia
requiere $n^{lin} > 1$ y el parámetro debe declararse como
$capX_{B_P} = 168 \cdot n^{lin} \cdot \eta^{ef}$, documentando el número
de líneas y el factor de eficiencia adoptado. Reportar $capX_{B_P}$ como
una cifra única sin esa descomposición deja la instancia sin
trazabilidad.

# Identidad de articulación contable {#sec:articulacion}

Esta sección establece la identidad que debe satisfacer toda solución
del modelo. Su valor es doble: constituye la prueba de verificación más
potente disponible --- si cierra período a período, la contabilidad del
modelo es correcta --- y su demostración **verifica retroactivamente**
las correcciones relativas a la depreciación y al costo financiero de
corto plazo.

## Enunciado {#sec:articulacion_enunciado}

Defínase el capital contable implícito del sistema al final del período
$t$ como el activo total menos el pasivo total:
$$E_t \;=\; \underbrace{fa_t + NOA_t + C_t + FI_t}_{\text{activo neto de } AP_t} \;-\; \underbrace{DS_t - D^{LP}}_{\text{pasivo financiero}}
    \;=\; fa_t + NOA_t + C_t + FI_t - DS_t - D^{LP}$$

**Proposición (identidad de articulación).** Toda solución factible del
modelo satisface, para todo $t = 1, \dots, T$:

$$\begin{equation}
\boxed{
    E_t - E_{t-1} \;=\; \underbrace{\big(EBIT_t - NIE_t - int^{LP}_t\big)\big(1 - \tau^{isr}\big)}_{\text{utilidad neta del período}} \;-\; div_t
}
\label{eq:articulacion}
\tag{A1}
\end{equation}$$

Es decir, la variación del capital contable iguala la utilidad neta
menos los dividendos: la condición de *clean surplus* que la teoría del
ingreso residual exige (Preinreich 1937; Lücke 1955; Ohlson 1995).

## Demostración {#sec:articulacion_demostracion}

Se parte de la definición de $E_t$ y se sustituyen las ecuaciones de
balance del modelo.

**Paso 1 --- variación de los activos fijos.** Por la rampa de
depreciación, $fa_t - fa_{t-1} = -dep^{sem}$.

**Paso 2 --- variación del capital operativo.** Por la
ec. ([\[eq:noa\]](#eq:noa){reference-type="ref" reference="eq:noa"}),
$$\Delta NOA_t = IC_t + \sum_{z} \Delta AR_{zt} - \Delta AP_t,$$ donde
$IC_t$ es exactamente el cambio de inventarios y WIP valorado al costo,
ec. ([\[eq:ic\]](#eq:ic){reference-type="ref" reference="eq:ic"}). De
las ecuaciones de cuentas
abiertas ([\[eq:cxc\]](#eq:cxc){reference-type="ref"
reference="eq:cxc"}) y ([\[eq:cxp\]](#eq:cxp){reference-type="ref"
reference="eq:cxp"}), y usando la definición de $OM_t$
en ([\[eq:om\]](#eq:om){reference-type="ref" reference="eq:om"}),
$$\sum_z \Delta AR_{zt} - \Delta AP_t = NS_t - \text{compras}_t - OM_t.$$
Puesto que $OCF_t = VCO_t - \text{compras}_t$ por construcción de las
ecs. ([\[eq:vco\]](#eq:vco){reference-type="ref" reference="eq:vco"})
y ([\[eq:ocf\]](#eq:ocf){reference-type="ref" reference="eq:ocf"}), se
obtiene $\text{compras}_t = VCO_t - OCF_t$ y por tanto
$$\Delta NOA_t = IC_t + NS_t - VCO_t + OCF_t - OM_t = TCM_t + OCF_t - OM_t,$$
donde el último paso emplea la definición de $TCM_t$
en ([\[eq:tcm\]](#eq:tcm){reference-type="ref" reference="eq:tcm"}).

**Paso 3 --- variación de la caja.** Directamente
de ([\[eq:caja\]](#eq:caja){reference-type="ref" reference="eq:caja"}):
$$\Delta C_t = -OCF_t + OM_t + FM_t - TAX_t - ec'_t.$$

**Paso 4 --- variación de las posiciones financieras.**
Reagrupando ([\[eq:fm\]](#eq:fm){reference-type="ref"
reference="eq:fm"}) en principal e interés,
$$FM_t = -\Delta FI_t + \Delta DS_t - NIE_t
    \qquad \Longrightarrow \qquad
    \Delta FI_t - \Delta DS_t = -FM_t - NIE_t.$$

**Paso 5 --- agregación.** Sumando los cuatro pasos, los términos
$OCF_t$, $OM_t$ y $FM_t$ se cancelan por pares:
$$\Delta E_t = -dep^{sem} + \big(TCM_t + OCF_t - OM_t\big) + \big(-OCF_t + OM_t + FM_t - TAX_t - ec'_t\big) + \big(-FM_t - NIE_t\big)$$
$$= -dep^{sem} + TCM_t - TAX_t - ec'_t - NIE_t.$$

**Paso 6 --- sustitución de los parámetros de costo fijo.**
Por ([\[eq:ec_prima\]](#eq:ec_prima){reference-type="ref"
reference="eq:ec_prima"}),
$ec'_t = fc^{caja}_t + int^{LP}_t(1-\tau^{isr}) + div_t$; y
por ([\[eq:fc_devengado\]](#eq:fc_devengado){reference-type="ref"
reference="eq:fc_devengado"}), $fc^{caja}_t = fc_t - dep^{sem}$.
Entonces
$$\Delta E_t = -dep^{sem} + TCM_t - TAX_t - \big(fc_t - dep^{sem}\big) - int^{LP}_t\big(1-\tau^{isr}\big) - div_t - NIE_t.$$
**La depreciación se cancela de forma exacta**, y usando
$EBIT_t = TCM_t - fc_t$ de ([\[eq:ebit\]](#eq:ebit){reference-type="ref"
reference="eq:ebit"}):
$$\Delta E_t = EBIT_t - NIE_t - TAX_t - int^{LP}_t\big(1-\tau^{isr}\big) - div_t.$$

**Paso 7 --- sustitución del impuesto.**
Por ([\[eq:tax\]](#eq:tax){reference-type="ref" reference="eq:tax"}),
$TAX_t = \tau^{isr}(EBIT_t - NIE_t)$, de donde
$EBIT_t - NIE_t - TAX_t = (EBIT_t - NIE_t)(1 - \tau^{isr})$, y se
obtiene ([\[eq:articulacion\]](#eq:articulacion){reference-type="ref"
reference="eq:articulacion"}). $\blacksquare$

## Lo que la demostración verifica {#sec:articulacion_implicaciones}

La cancelación exacta de $dep^{sem}$ en el Paso 6 no es una casualidad
algebraica: es la **condición necesaria y suficiente** para que el
tratamiento de la depreciación sea consistente. Ocurre únicamente porque
$dep^{sem}$ aparece simultáneamente en tres lugares con los signos
correctos: restando en la rampa $fa_t$, sumando en $fc_t$ (y por tanto
restando en $EBIT_t$), y **ausente** de $ec'_t$. Si la depreciación no
estuviera en $fc_t$ --- el estado del modelo antes de la corrección
correspondiente --- la identidad arrojaría un residuo de exactamente
$-dep^{sem}$ por período, acumulando $-T \cdot dep^{sem}$ al cierre del
horizonte.

Del mismo modo, el Paso 7 cierra únicamente si la base gravable es
$EBIT_t - NIE_t$. Con $TAX_t = \tau^{isr} EBIT_t$ quedaría un residuo de
$-\tau^{isr} NIE_t$ por período: el escudo fiscal que la función
objetivo reconoce en su Término V pero que el flujo de caja no
recibiría.

La
identidad ([\[eq:articulacion\]](#eq:articulacion){reference-type="ref"
reference="eq:articulacion"}) es por tanto el **test unificado** de las
correcciones de depreciación e impuestos. Un residuo no nulo localiza el
error de inmediato:

::: {#tab:residuos}
  **Residuo $\varepsilon_t$**                     **Causa probable**
  ----------------------------------------------- -----------------------------------------------------------------------------------------------------------
  $-dep^{sem}$ constante                          La depreciación no está incluida en $fc_t$, o está duplicada en $ec'_t$.
  $-\tau^{isr} NIE_t$                             $TAX_t$ grava $EBIT_t$ en lugar de $EBIT_t - NIE_t$.
  $+\tau^{isr} int^{LP}_t$                        Los intereses de largo plazo entran en $ec'_t$ en bruto en lugar de netos del escudo.
  Proporcional a $\Delta W_{pt}$                  Inconsistencia del factor $\alpha$ entre $NOA_t$ e $IC_t$.
  Proporcional a las compras de $t$               $OCF_t$ no excluye correctamente las materias primas, o el flete de proveedor se está contando dos veces.
  Salto en $t \in (\tau^{pay}_N, \tau^{pay}_I]$   Hueco de cobertura en la ecuación de cuentas por pagar.
  Salto en $t \leq \max_z \tau_z$                 Violación de las condiciones de consistencia inicial (C1) o (C2).

  : Diagnóstico por firma del residuo de la identidad de articulación,
  $\varepsilon_t = \Delta E_t - \big[(EBIT_t - NIE_t - int^{LP}_t)(1-\tau^{isr}) - div_t\big]$.
:::

## Relación con la función objetivo {#sec:articulacion_eva}

Sumando ([\[eq:articulacion\]](#eq:articulacion){reference-type="ref"
reference="eq:articulacion"}) sobre el horizonte y sustituyendo en la
función objetivo ([\[eq:objetivo\]](#eq:objetivo){reference-type="ref"
reference="eq:objetivo"}), el EVA admite la lectura
$$\text{EVA} = \underbrace{\big(E_T - E_0\big) + \sum_{t=1}^{T} div_t + \sum_{t=1}^{T} int^{LP}_t\big(1-\tau^{isr}\big)}_{\text{retorno total generado para los proveedores de capital}}
    \;-\; \underbrace{i^{wacc}\sum_{t=0}^{T}\big(fa_t + NOA_t\big) \;+\; \dots}_{\text{retorno exigido}}$$
donde los puntos suspensivos recogen los Términos III, IV y la
componente financiera del VI. La interpretación es la estándar del
ingreso residual: **valor creado por encima del costo del capital
empleado**. Esta lectura es la que permite reportar el resultado del
modelo ante un comité financiero sin traducción adicional.

# Instancia miniatura de validación {#sec:instancia_mini}

Resolver directamente la instancia de 52 semanas es la forma más costosa
de encontrar un error. Esta sección define una instancia reducida,
completamente parametrizada, diseñada para que cada corrección
introducida en el documento tenga una prueba ejecutable asociada. Su
propósito no es producir un plan de interés gerencial sino **fallar de
forma diagnóstica** cuando la implementación se desvía de la
formulación.

## Criterio de diseño {#sec:mini_criterio}

La instancia es deliberadamente pequeña pero **no degenerada**: conserva
al menos un representante de cada estructura que el modelo pretende
capturar. En particular:

- Conserva un insumo de cada clase, **incluido un importado**, de modo
  que el arco de transferencia interna $B_G \to B_P$ sea indispensable.
  Si ese arco está mal definido, la instancia es infactible.

- Fija $\tau^{pay}_N = 1 < \tau^{pay}_I = 2$, de forma que el período
  $t = 2$ cae **exactamente** en la ventana
  $(\tau^{pay}_N, \tau^{pay}_I]$ que quedaba sin ecuación de balance en
  la formulación particionada.

- Conserva dos zonas con plazos de cobro distintos ($\tau_{z_1} = 1$,
  $\tau_{z_2} = 2$) y dos presentaciones con volúmenes distintos
  ($a^2 = 0.5$ y $1.0$), de modo que la heterogeneidad de plazos y la
  conversión granel--presentación queden ejercitadas.

- Fija $\alpha = 0.8 \neq 1$ para que el factor de avance del WIP
  intervenga de forma no trivial en $NOA_t$ y en $IC_t$ simultáneamente.

- Incluye un pico de demanda en $t = 4$ (el doble del nivel base) que
  obliga al modelo a anticipar producción, ejercitando el mecanismo
  *make-to-stock* y el término de regularización.

- Deja holgura de capacidad suficiente para que el óptimo sirva la
  demanda completa, de modo que **cualquier** $LS_{qzt} > 0$ observado
  sea señal de error y no consecuencia de una restricción física.

## Conjuntos {#sec:mini_conjuntos}

  **Conjunto**       **Contenido**                                                                               **Cardinalidad derivada**
  ------------------ ------------------------------------------------------------------------------------------- ----------------------------------------------------------------
  $T$                $6$ semanas                                                                                 $\delta = 1$
  $R^E, R^N, R^I$    $\{r^E_1\}$, $\{r^N_1\}$, $\{r^I_1\}$                                                       $|R| = 3$; $R^{(N)} = \{r^E_1, r^N_1\}$, $R^{(I)} = \{r^I_1\}$
  $P$                $\{p_1\}$                                                                                   $|P| = 1$
  $Q$                $\{q_1$ (500 ml)$,\; q_2$ (1 L)$\}$                                                         $|Q| = 2$
  $L^M$              $\{z_1, z_2\}$ con $\tau_{z_1} = 1$, $\tau_{z_2} = 2$                                       $|L^M| = 2$
  $L^{Op}$           $\{B_P, B_G, B_{wip}, B_S, B_T\}$                                                           sin cambio respecto al modelo completo
  $PL^S$             $r^E_1 \to B_P$; $r^N_1 \to B_P$; $r^N_1 \to B_G$; $r^I_1 \to B_G$                          $|PL^S| = 4$
  $\mathit{TC}$      los 4 anteriores $+\; B_G\!\to\!B_P \;+\; B_{wip}\!\to\!B_S \;+\; 2 \times (B_T\!\to\!z)$   $|\mathit{TC}| = 8$
  $PC$               $4 + \mathbf{2} + 1 + 4$                                                                    $|PC| = 11$
  $PL^{Op}$          $B_P\!:3$, $B_G\!:2$, $B_{wip}\!:1$, $B_S\!:1$, $B_T\!:2$                                   $|PL^{Op}| = 9$
  $\mathit{BOM}^1$   $(p_1, r^E_1), (p_1, r^N_1), (p_1, r^I_1)$                                                  $|\mathit{BOM}^1| = 3$
  $\mathit{BOM}^2$   $(q_1, p_1), (q_2, p_1)$                                                                    $|\mathit{BOM}^2| = 2$

**Nota crítica sobre $|PC| = 11$:** los **dos** pares del arco
$B_G \to B_P$ corresponden a $r^N_1$ y $r^I_1$. Un generador que
restrinja ese arco a insumos nacionales produciría $|PC| = 10$ y una
instancia infactible, pues el $\mathit{BOM}^1$ consume $r^I_1$ contra el
inventario de $B_P$. Verificar la cardinalidad es la prueba más rápida
de la corrección del ruteo.

## Parámetros técnicos y de costo {#sec:mini_tecnicos}

  **Parámetro**        **Valor**                                                                  **Nota**
  -------------------- -------------------------------------------------------------------------- -------------------------------------------------------------------------------------------------------------------------------------
  $a^1_{p_1 r}$        $r^E_1\!: 1.00$; $r^N_1\!: 0.20$; $r^I_1\!: 0.10$                          receta de un solo semiterminado
  $a^2_{qp}$           $q_1\!: 0.50$; $q_2\!: 1.00$                                               500 ml y 1 L
  $e_r$                $10$; $20$; $50$                                                           incluye flete DAP
  $kx_{p_1}$           $1.00$ h/unidad                                                            
  $cd_{B_P}$           $5.00$                                                                     de donde $e_{p_1} = 1(10) + 0.2(20) + 0.1(50) + 1(5) = 24$
  $cc_q$               $q_1\!: 3$; $q_2\!: 5$                                                     de donde $e_{q_1} = 15$, $e_{q_2} = 29$
  $v_q$                $q_1\!: 25$; $q_2\!: 45$                                                   márgenes $v_q - e_q$: $10$ y $16$
  $ki_\pi,\; ky_\pi$   $1.00$                                                                     unitarios para simplificar la lectura
  $ci_l$               $0.10$                                                                     igual en todos los almacenes
  $cw$                 $0.20$                                                                     
  $cy_{lj}$            $B_G\!\to\!B_P\!: 0.5$; $B_{wip}\!\to\!B_S\!: 1.0$; $B_T\!\to\!z\!: 1.5$   arcos internos únicamente
  $capX_{B_P}$         $100$ h/semana                                                             $n^{lin}=1$, $\eta^{ef} \approx 0.60$
  $capI_l$             $500$                                                                      no restrictiva por diseño
  $\alpha$             $0.80$                                                                     ejercita la valoración al grado de avance
  $\rho$               $0.10$                                                                     cumple $\rho \ll \min_q (v_q - e_q) = 10$
  $p^{lost}_{qz}$      $\beta = 1.5$: $q_1\!: 15$; $q_2\!: 24$                                    satisface ([\[eq:consistencia_plost\]](#eq:consistencia_plost){reference-type="ref" reference="eq:consistencia_plost"}) con holgura

**Demanda $d_{qzt}$:** nivel base constante $d_{q_1 z_1} = 20$,
$d_{q_1 z_2} = 10$, $d_{q_2 z_1} = 10$, $d_{q_2 z_2} = 5$, duplicado en
$t = 4$. De aquí,
por ([\[eq:req_granel\]](#eq:req_granel){reference-type="ref"
reference="eq:req_granel"}), $h_{p_1, t} = 30$ para $t \neq 4$ y
$h_{p_1, 4} = 60$ unidades de granel.

## Parámetros financieros {#sec:mini_financieros}

  **Parámetro**        **Valor**        **Nota**
  -------------------- ---------------- -----------------------------------------------------------------------------------------------------------------------
  $\tau^{isr}$         $0.30$           
  $i^{wacc}$           $0.002182$       $(1.12)^{1/52} - 1$, WACC anual $12\%$ de largo plazo
  $i^{DS}$             $0.002691$       $(1.15)^{1/52} - 1$
  $i^{FI}$             $0.001121$       $(1.06)^{1/52} - 1$
  $fa_0$               $5{,}000$        
  $dep^{sem}$          $50$             cumple (P3): $fa_6 = 4{,}700 \geq 0$
  $fc^{caja}_t$        $300$            de donde $fc_t = 350$ por ([\[eq:fc_devengado\]](#eq:fc_devengado){reference-type="ref" reference="eq:fc_devengado"})
  $int^{LP}_t$         $20$             de donde $ec'_t = 300 + 20(0.7) + 0 = 314$
  $div_t$              $0$              nulo para aislar la identidad de articulación
  $D^{LP}$             $2{,}000$        constante en el horizonte
  $C^0,\; C^{min}$     $500$; $200$     
  $DS^0,\; DS^{max}$   $0$; $2{,}000$   
  $FI^0$               $0$              
  $AP^{max}$           $3{,}000$        uso estimado por ([\[eq:fact_credito\]](#eq:fact_credito){reference-type="ref" reference="eq:fact_credito"}): $28\%$

**Verificación
de ([\[eq:consistencia_tasas\]](#eq:consistencia_tasas){reference-type="ref"
reference="eq:consistencia_tasas"}):**
$i^{DS} = 0.002691 > i^{FI} = 0.001121$.

## Condiciones de frontera {#sec:mini_frontera}

  **Rubro**         **Valores**                                                                                                                                                                                      **Condición**
  ----------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ -----------------------------------------
  $I^0_{\pi l}$     $(r^E_1, B_P)\!: 50$; $(r^N_1, B_P)\!: 20$; $(r^I_1, B_P)\!: 10$; $(r^N_1, B_G)\!: 10$; $(r^I_1, B_G)\!: 20$; $(p_1, B_{wip})\!: 5$; $(p_1, B_S)\!: 10$; $(q_1, B_T)\!: 10$; $(q_2, B_T)\!: 5$   (C4), (C5)
  $I^T_{\pi l}$     $(p_1, B_S)\!: 5$; $(q_1, B_T)\!: 5$; $(q_2, B_T)\!: 3$; resto $0$                                                                                                                               (C4)
  $X^0_{p_1, 0}$    $30$                                                                                                                                                                                             (C7)
  $W^0_{p_1}$       $30$                                                                                                                                                                                             **(C3)**: $= X^0_{p_1,0}$
  $X^{ref}_{p_1}$   $30$                                                                                                                                                                                             **(C8)**: $= X^0_{p_1,0}$
  $S^0_{z,s}$       $(z_1, 0)\!: 600$; $(z_2, -1)\!: 250$; $(z_2, 0)\!: 250$                                                                                                                                         soporte correcto
  $AR^0_z$          $z_1\!: 600$; $z_2\!: 500$                                                                                                                                                                       **(C1)**
  $B^0_{r,s}$       $(r^E_1, 0)\!: 300$; $(r^N_1, 0)\!: 200$; $(r^I_1, -1)\!: 150$; $(r^I_1, 0)\!: 250$                                                                                                              soporte correcto
  $AP^0$            $900$                                                                                                                                                                                            **(C2)**: $300\!+\!200\!+\!150\!+\!250$

**Estado inicial derivado:** de la
ec. ([\[eq:noa\]](#eq:noa){reference-type="ref" reference="eq:noa"}),
$NOA_0 = 4{,}031.00$, y por tanto el capital contable inicial de la
[12](#sec:articulacion){reference-type="ref+Label"
reference="sec:articulacion"} es
$E_0 = fa_0 + NOA_0 + C^0 + FI^0 - DS^0 - D^{LP} = 7{,}531.00$.

## Verificación previa de las condiciones agregadas {#sec:mini_verificacion_previa}

Con $G^0_{p_1} = 30 + 5 + 10 + [0.5(10) + 1.0(5)] = 55$ y
$g^T_{p_1} = 0 + 5 + [0.5(5) + 1.0(3)] = 10.5$:

  **Condición**   **Lado izquierdo**   **Lado derecho**       **Holgura**
  --------------- -------------------- ---------------------- ----------------
  (V3), $t = 1$   $30$ unidades        $G^0 = 55$             $25$ unidades
  (V4), $t = 2$   $5.0$ h              $100$ h                $95.0$ h
  (V4), $t = 3$   $35.0$ h             $200$ h                $165.0$ h
  (V4), $t = 4$   $95.0$ h             $300$ h                $205.0$ h
  (V4), $t = 5$   $125.0$ h            $400$ h                $275.0$ h
  (V4), $t = 6$   $155.0$ h            $500$ h                $345.0$ h
  (V5)            $165.5$ h            $500$ h                $334.5$ h
  (V6)            $840.0$              $AP^{max} = 3{,}000$   uso del $28\%$

Las ocho condiciones de consistencia (C1)--(C8) y las seis de
factibilidad (V1)--(V6) se satisfacen. La instancia admite por
construcción una solución con $LS_{qzt} = 0$ para todo $(q,z,t)$.

## Matriz de verificación {#sec:mini_matriz}

Cada fila asocia una corrección del documento a una prueba concreta
sobre la instancia miniatura. Se recomienda implementarlas como pruebas
automatizadas: son la red de seguridad que permitirá extender el modelo
a MILP y al gemelo digital sin arrastrar regresiones silenciosas.

::: {#tab:matriz_verificacion}
  **Objeto**                   **Prueba**                                                                                                               **Resultado esperado**
  ---------------------------- ------------------------------------------------------------------------------------------------------------------------ ----------------------------------------------------------------------------------------------------------------------------------------------------------------
  Ruteo de importados          Contar $|PC|$; resolver                                                                                                  $|PC| = 11$ y problema factible. Restringir el arco $B_G\!\to\!B_P$ a $R^N$ debe producir infactibilidad
  Dominios (D1)                Declarar $IC_t \geq 0$ deliberadamente                                                                                   Infactibilidad: el modelo no puede desacumular inventario frente a ([\[eq:inv_terminal\]](#eq:inv_terminal){reference-type="ref" reference="eq:inv_terminal"})
  Dominios (D1)                Declarar $TAX_t \geq 0$ y forzar $EBIT_1 < 0$                                                                            Inconsistencia entre el Término I y el ISR desembolsado
  Cuentas por pagar (22)       Inspeccionar $AP_2$                                                                                                      Determinado de forma única. Con la formulación particionada, $t = 2 \in (\tau^{pay}_N, \tau^{pay}_I]$ queda sin ecuación y $AP_2$ resulta arbitrario
  Operadores (H1), (H2)        $\sum_{t} \widehat{S}_{z,t-\tau_z}$ frente a ventas                                                                      Todo cobro aparece una sola vez; $AR_{z,T}$ igual a las ventas de las últimas $\tau_z$ semanas
  Pago único (22)              $AP_T - AP^0$ frente a compras no vencidas                                                                               Identidad exacta
  Cargo de capital (II)+(VI)   Contar cargos                                                                                                            $7$ cargos, uno por cada $NOA_0, \dots, NOA_6$; total igual a $i^{wacc}\sum_{t=0}^{6}(fa_t + NOA_t)$
  Saldo de apertura (II)       Comparar con la variante de cierre                                                                                       El plan debe diferir; con saldo de cierre el modelo subsidia la acumulación intraperíodo
  Depreciación (P1), (P2)      Residuo $\varepsilon_t$ de ([\[eq:articulacion\]](#eq:articulacion){reference-type="ref" reference="eq:articulacion"})   $\varepsilon_t = 0$. Omitir $dep^{sem}$ de $fc_t$ debe arrojar $\varepsilon_t = -50$ constante
  Escudo fiscal (8)            Residuo $\varepsilon_t$ con $TAX_t = \tau^{isr}EBIT_t$                                                                   $\varepsilon_t = -\tau^{isr} NIE_t$, no nulo en cuanto $DS_t > 0$
  Costo financiero (V)         Efecto neto del interés sobre la caja                                                                                    Igual a $-NIE_t(1-\tau^{isr})$, idéntico al Término V
  Arbitraje (1c)               $DS_t \cdot FI_t$ para todo $t$                                                                                          Nulo en el óptimo. Un valor positivo indica error de signo en $FM_t$ o en $NIE_t$
  Valor terminal (VI)          Perturbar $DS_6$                                                                                                         El objetivo debe empeorar. Sin el Término VI, $DS_6$ tiene coeficiente nulo y el óptimo es degenerado
  Consistencia inicial (C1)    Fijar $AR^0_{z_1} = 500 \neq 600$                                                                                        Infactibilidad en $t = 1$ por $AR_{z_1, 1} \geq 0$
  Consistencia inicial (C2)    Fijar $AP^0 = 1{,}200 \neq 900$                                                                                          $AP_t$ con piso positivo irreductible; EVA inflado sin contrapartida de caja
  Consistencia inicial (C3)    Fijar $W^0_{p_1} = 20 \neq 30$                                                                                           Violación del balance de masa; con $\delta = 1$ el síntoma es $W_{p_1,1} \neq X_{p_1,1}$
  Factibilidad (V4)            Reducir $capX_{B_P}$ a $30$ h                                                                                            (V4) falla en $t = 6$ y el óptimo presenta $LS_{qzt} > 0$ pese a $p^{lost}$ correctamente calibrado
  Regularización (19b)         Fijar $X^{ref}_{p_1} = 0$ violando (C8)                                                                                  Penalización espuria de $\rho \cdot X_{p_1,1}$ en la primera semana
  BOM nivel 2                  Mezcla $q_1$/$q_2$ frente a consumo de $p_1$                                                                             Consumo igual a $0.5 Z_{q_1 t} + 1.0 Z_{q_2 t}$, no a $Z_{q_1 t} + Z_{q_2 t}$

  : Matriz de verificación de la instancia miniatura.
:::

## Protocolo de ejecución {#sec:mini_protocolo}

1.  **Admisión de datos:** verificar (C1)--(C8). El fallo bloquea la
    ejecución.

2.  **Factibilidad agregada:** verificar (V3)--(V6). El fallo no
    bloquea, pero debe registrarse: condiciona la interpretación de
    $LS_{qzt}$.

3.  **Resolver** la instancia miniatura y confirmar $LS_{qzt} = 0$ para
    todo $(q,z,t)$.

4.  **Articulación:** calcular $\varepsilon_t$
    de ([\[eq:articulacion\]](#eq:articulacion){reference-type="ref"
    reference="eq:articulacion"}) para $t = 1, \dots, 6$ y exigir
    $|\varepsilon_t| < \epsilon_{tol}$, con $\epsilon_{tol}$ del orden
    de la tolerancia de factibilidad del solver escalada por la magnitud
    de $E_t$.

5.  **Pruebas de mutación:** ejecutar la
    [11](#tab:matriz_verificacion){reference-type="ref+Label"
    reference="tab:matriz_verificacion"} introduciendo cada error de
    forma deliberada y confirmar que la prueba correspondiente lo
    detecta. Una prueba que no falla cuando debe no es una prueba.

6.  **Escalamiento:** sólo entonces resolver la instancia de $T = 52$,
    repitiendo los pasos 1, 2 y 4.

**Nota sobre el paso 5.** Es el que suele omitirse y el único que
garantiza que la batería sirve. Las siete firmas de residuo de la
[10](#tab:residuos){reference-type="ref+Label" reference="tab:residuos"}
son precisamente el catálogo de mutaciones a inyectar; si alguna no se
detecta, la implementación de la prueba está mal, no el modelo.

# Resumen de la estructura del modelo {#sec:resumen}

::: xltabular
l l X

\
**Componente** & **Ec.** & **Descripción**\

\
**Componente** & **Ec.** & **Descripción**\

Función objetivo & (1) & $\max$ EVA $=$ I. NOPAT $-$ II. Cargo de
capital (apertura, $t=1..T$) $-$ III. Penalización $-$
IV. Regularización $-$ V. Costo financiero neto $-$ VI. Valor terminal
(continuación $T+1$)\
Consistencia $p^{lost}$ & (1b) &
$p^{lost}_{qz} > \max\{0,\; i^{wacc} v_q \tau_z / (1 - \tau^{isr}) - (v_q - e_q)\}$;
condición suficiente y conservadora\
Consistencia de tasas & (1c) & $i^{DS} > i^{FI}$: impide el arbitraje
deuda--inversión y la degeneración asociada\
\
Variables libres & (D1) &
$TCM, IC, EBIT, NIE, TAX, OM, FM, NOA \in \mathbb{R}$\
Variables no negativas & (D2) & $NS_t, VCO_t, OCF_t \geq 0$ (cotas
implicadas, declaradas por higiene)\
Costos fijos devengados & (P1) & $fc_t = fc^{caja}_t + dep^{sem}$: la
depreciación entra en $EBIT_t$ y en la base gravable\
Flujo exógeno de caja & (P2) &
$ec'_t = fc^{caja}_t + int^{LP}_t(1-\tau^{isr}) + div_t$: sin
depreciación, con escudo de LP\
Dominio de $fa_t$ & (P3) & $dep^{sem} \leq fa_0 / T$, de modo que
$fa_T \geq 0$\
\
Agregación de CxC & (C1) & $AR^0_z = \sum_{s=-\tau_z+1}^{0} S^0_{z,s}$\
Agregación de CxP & (C2) &
$AP^0 = \sum_{k}\sum_{r \in R^{(k)}}\sum_{s=-\tau^{pay}_k+1}^{0} B^0_{r,s}$\
Agregación de WIP & (C3) & $W^0_p = \sum_{s=-\delta+1}^{0} X^0_{p,s}$;
con $\delta=1$ implica $W_{p,1} = X_{p,1}$\
No negatividad & (C4) & Historiales e inventarios de frontera $\geq 0$\
Almacén en $t=0$ & (C5) & $\sum_\pi ki_\pi I^0_{\pi l} \leq capI_l$: la
ec. (17) no acota $t=0$\
Cotas iniciales & (C6) & $C^0 \geq C^{min}$, $DS^0 \leq DS^{max}$,
$AP^0 \leq AP^{max}$, $FI^0 \geq 0$\
Capacidad histórica & (C7) & $\sum_p kx_p X^0_{p,s} \leq capX_{B_P}$\
Referencia de regularización & (C8) & $X^{ref}_p = X^0_{p,0}$\
\
Margen de contribución & (2) & $TCM_t = NS_t - VCO_t + IC_t$\
Activos operativos netos & (3) & $NOA_t$ = inventarios $+$ WIP($\alpha$)
$+$ CxC $-$ CxP (sin $FI$, $C$, $DS$)\
Ventas netas & (4) & $NS_t$ = ingresos por despachos\
Cambio en inventario & (5) & $IC_t$ = $\Delta$ inventarios $+$ $\Delta$
WIP al grado de avance $\alpha$\
Costos variables & (6) & $VCO_t$: almacenamiento sobre **inventario
promedio**; transporte sólo arcos internos (45 pares)\
Utilidad operativa & (7) & $EBIT_t = TCM_t - fc_t$, con $fc_t$ ya neto
de depreciación\
Gasto financiero neto & (7b) &
$NIE_t = i^{DS} DS_{t-1} - i^{FI} FI_{t-1}$, $t = 1,\dots,T+1$ (única
variable indexada a $T+1$)\
Impuesto endógeno & (8) & $TAX_t = \tau^{isr}(EBIT_t - NIE_t)$ (simetría
fiscal, extensible a MILP)\
\
Balances de masa & (9)--(14) & Un balance por nodo: $B_P$, $B_G$, WIP,
$B_{wip}$, $B_S$, $B_T$. Arco $B_G \to B_P$ sobre $R^N \cup R^I$\
Inv. inicial / terminal & (15a--c) & Inicial ($=$); terminal
($\geq I^T$); WIP inicial\
Capacidades & (16)--(17) & Transformación primaria y almacenamiento\
Demanda & (18) & $Y + LS = d$ (demanda determinista)\
Regularización & (19a--b) & $X_{pt} - X_{p,t-1} = \Delta^+ - \Delta^-$;
frontera explícita\
No negatividad & (20) & Variables físicas $\geq 0$ (incluye $\Delta^+$,
$\Delta^-$)\
\
Operador de cobros & (H1) & $\widehat{S}_{z,s}$: variable si $s \geq 1$,
parámetro $S^0_{z,s}$ si $s \leq 0$\
Operador de pagos & (H2) & $\widehat{B}_{r,s}$: variable si $s \geq 1$,
parámetro $B^0_{r,s}$ si $s \leq 0$\
Cuentas por cobrar & (21) & Balance dinámico por zona, **ecuación
única** $\forall t = 1,\dots,T$\
Cuentas por pagar & (22) & Balance dinámico con partición de clases $K$,
**ecuación única** sin hueco temporal\
Límite crédito comercial & (23) & $AP_t \leq AP^{max}$\
Balance de caja & (24) & $C_t = C_{t-1} - OCF + OM + FM - TAX - ec'$\
Flujos de caja & (25)--(27) & Operaciones, cuentas abiertas (ecuación
única), gestión financiera\
Cotas financieras & (28)--(29) & Caja mínima, línea de crédito\
Condiciones iniciales & (30) & Posiciones financieras en $t = 0$\
No negatividad & (31) & Variables de decisión financieras $\geq 0$\
\
Requerimiento granel & (V1) & $h_{pt} = \sum_q \sum_z a^2_{qp} d_{qzt}$\
*Pipeline* inicial & (V2) &
$G^0_p = W^0_p + I^0_{p,B_{wip}} + I^0_{p,B_S} + \sum_q a^2_{qp} I^0_{q,B_T}$\
Arranque & (V3) & $\sum_{s\leq t} h_{ps} \leq G^0_p$ para
$t \leq \delta$, por producto\
Capacidad acumulada & (V4) &
$\sum_p kx_p \max(0, \sum_{s\leq t} h_{ps} - G^0_p) \leq (t-\delta) capX_{B_P}$\
Horizonte completo & (V5) & Ídem incluyendo la meta terminal $g^T_p$\
Crédito comercial & (V6) & Dimensionamiento de $AP^{max}$ frente a las
compras de régimen\
\
Articulación contable & (A1) &
$\Delta E_t = (EBIT_t - NIE_t - int^{LP}_t)(1-\tau^{isr}) - div_t$;
demostrada en [12](#sec:articulacion){reference-type="ref+Label"
reference="sec:articulacion"}\
Instancia miniatura & --- & $T=6$, $|P|=1$, $|Q|=2$, $|R|=3$, $|L^M|=2$;
matriz de 19 pruebas en
[11](#tab:matriz_verificacion){reference-type="ref+Label"
reference="tab:matriz_verificacion"}\
\
SEQ-1 & (32) & $\max \sum TCM_t$ con restricciones físicas\
SEQ-2 & (33)--(34) & $\min \sum_{t=1}^{T+1} NIE_t (1-\tau^{isr})$ con
flujo físico fijado; conmensurable con el Término V del modelo
integrado\
:::

# Supuestos de simplificación declarados {#sec:supuestos_declarados}

Esta sección documenta las cinco simplificaciones que permanecen en la
formulación. Todas fueron identificadas, evaluadas en cuanto a su efecto
sobre el resultado y **retenidas de forma deliberada**. Su propósito es
que ninguna de ellas quede como omisión tácita: un revisor que detecte
cualquiera de estos puntos debe poder verificar aquí que fue
considerado, con qué criterio se decidió conservarlo y bajo qué
condición la decisión deja de ser sostenible.

La distinción respecto a los supuestos de la
[5.2](#sec:supuestos_operativos){reference-type="ref+Label"
reference="sec:supuestos_operativos"} es de naturaleza. Aquéllos
describen el sistema modelado --- son afirmaciones sobre la realidad de
la cadena. Los de esta sección describen el **modelo** --- son
afirmaciones sobre lo que la formulación deja fuera y con qué
consecuencia. Cada uno se presenta con cuatro elementos: enunciado,
justificación, sesgo y condición de validez.

::: {#tab:supuestos_declarados}
  **\#**   **Simplificación**                                                                                                  **Sesgo**                    **Condición de validez**
  -------- ------------------------------------------------------------------------------------------------ ----------------------------------------------- --------------------------------------------------------------------------------------
  (S1)     Factor de avance $\alpha$ no conciliado con la imputación temporal del costo de transformación    Neutro salvo en los últimos $\delta$ períodos  $\alpha = 1$, o bien aceptar el sesgo de borde en $t > T - \delta$
  (S2)     Ausencia de *lead time* de abastecimiento y de cotas de proveedor                                                 **Optimista**                  Entrega dentro del período semanal y capacidad de proveedor no restrictiva
  (S3)     SEQ-2 sin variables elásticas en las cotas financieras                                                    No aplica al modelo integrado          El flujo físico de SEQ-1 satisface (23), (28) y (29)
  (S4)     El WIP no consume capacidad de almacenamiento                                                                        Neutro                      $\delta = 1$ y tanques de retención dimensionados a la capacidad horaria de la línea
  (S5)     Flete interno no capitalizado en $e_\pi$                                                                          **Optimista**                  Flete interno acumulado por debajo del 2% de $e_\pi$

  : Resumen de los supuestos de simplificación retenidos. La dirección
  del sesgo indica el efecto sobre el EVA reportado.
:::

## (S1) Coherencia del factor de avance del WIP {#sec:s1_alpha}

**Enunciado.** El costo de transformación primaria $kx_p \cdot cd_{B_P}$
se imputa **íntegramente** en el período de lanzamiento, por el término
(ii) de la ec. ([\[eq:vco\]](#eq:vco){reference-type="ref"
reference="eq:vco"}), mientras que el material en proceso se valora al
grado de avance $\alpha \cdot e_p$ en las
ecs. ([\[eq:noa\]](#eq:noa){reference-type="ref" reference="eq:noa"})
y ([\[eq:ic\]](#eq:ic){reference-type="ref" reference="eq:ic"}). Ambas
convenciones son internamente consistentes entre sí --- el mismo
$\alpha$ aparece en la base de capital y en el cambio de inventario ---
pero no están conciliadas con la imputación del costo de conversión.

**Consecuencia algebraica.** Sea una unidad de $p$ lanzada en $t$. Su
aportación a $TCM_t$ es
$$\underbrace{-\,kx_p\, cd_{B_P}}_{\text{conversión en } VCO_t} \;+\; \underbrace{\alpha\, e_p - \textstyle\sum_{(p,r)} a^1_{pr} e_r}_{\text{cambio de inventario en } IC_t}
    \;=\; \alpha\, e_p - e_p \;=\; -\,(1-\alpha)\, e_p,$$ empleando
$e_p = \sum a^1_{pr} e_r + kx_p cd_{B_P}$. Al liberarse en $t+\delta$,
el material transita de WIP (valorado $\alpha e_p$) a $B_{wip}$
(valorado $e_p$), de modo que $IC_{t+\delta}$ recupera exactamente
$+(1-\alpha)e_p$. **El efecto neto en dos períodos es nulo**: se trata
de un desplazamiento temporal, no de una pérdida.

**Justificación de retenerlo.** El modelo describe el nodo WIP como un
período de **retención de calidad**, no como una transformación
incompleta. Bajo esa lectura, lo correcto sería $\alpha = 1$: el
material ya absorbió el 100% de los materiales y el 100% del costo de
destilación. Se conserva $\alpha$ como parámetro libre en $(0, 1]$ por
dos razones: permite representar instalaciones donde la retención sí
implique proceso residual, y mantiene la trazabilidad con la valoración
al grado de avance que exigen la NIF C-4 y la IAS 2 cuando ése es el
caso. **Para el caso base se recomienda $\alpha = 1$**, con lo cual la
simplificación desaparece por completo.

**Sesgo y magnitud.** Con $\alpha < 1$ subsisten dos efectos, de
materialidad muy distinta:

1.  *Subvaluación del capital inmovilizado.* El WIP figura en $NOA_t$
    por $\alpha e_p$ en lugar de $e_p$, lo que omite un cargo de capital
    de $(1-\alpha)\, i^{wacc} \sum_p e_p W_{pt}$ por período. Sobre la
    instancia miniatura ($\alpha = 0.8$, $e_p = 24$, $W = 30$) la
    subvaluación es de $144$ unidades monetarias, esto es el **1.6%** de
    la base de capital $fa_0 + NOA_0$. Es inmaterial.

2.  *Sesgo de borde terminal.* Las unidades lanzadas en los últimos
    $\delta$ períodos no alcanzan a liberarse antes de $T$ y su
    $(1-\alpha)e_p$ nunca se recupera. Sobre la misma instancia el
    efecto es de $(1-\alpha)e_p X_{p,T}(1-\tau^{isr}) = 100.8$,
    equivalente al **18.7% del margen de una semana**. Esto **sí** es
    material: el modelo desincentiva artificialmente el lanzamiento en
    la última semana del horizonte.

**Condición de validez.** La simplificación es admisible si se cumple
alguna de estas tres: (i) $\alpha = 1$; (ii) el horizonte es lo bastante
largo para que la distorsión de los últimos $\delta$ períodos sea
irrelevante para la decisión que se implementa --- lo cual es el caso en
un esquema de horizonte rodante, donde sólo se ejecuta la primera
semana; (iii) se reporta explícitamente que $X_{p,t}$ para
$t > T - \delta$ está sesgado a la baja.

**Extensión disponible.** Diferir el costo de conversión en la misma
proporción que el avance, sustituyendo el término (ii) de la
ec. ([\[eq:vco\]](#eq:vco){reference-type="ref" reference="eq:vco"}) por
$$\sum_{p \in P} \Big[\alpha \cdot X_{pt} + (1-\alpha) \cdot X_{p, t-\delta}\Big] \cdot kx_p \cdot cd_{B_P},$$
con la frontera $X^0_{p,t-\delta}$ correspondiente. Permanece lineal y
elimina ambos efectos sin necesidad de fijar $\alpha = 1$.

## (S2) Ausencia de *lead time* de abastecimiento {#sec:s2_leadtime}

**Enunciado.** La variable $Y_{r, l^S, l, t}$ no tiene rezago ni cota
superior: el modelo puede ordenar una cantidad arbitraria de cualquier
insumo y recibirla en el mismo período $t$, mientras que su pago se
diferirá $\tau^{pay}_{k(r)}$ períodos por la
ec. ([\[eq:cxp\]](#eq:cxp){reference-type="ref" reference="eq:cxp"}).

**Reconocimiento explícito de la asimetría.** Es la simplificación más
difícil de defender del modelo y conviene enunciarla sin atenuantes: el
retraso **financiero** de los insumos importados se modela con precisión
--- clase de pago propia, plazo diferenciado $\tau^{pay}_I$, operadores
de historial, límite de crédito comercial --- mientras que su retraso
**físico**, que en la práctica es del mismo orden de magnitud, se modela
como nulo. La formulación es asimétrica respecto al mismo fenómeno.

**Justificación de retenerlo.** El supuesto operativo que la sostiene es
que el abastecimiento opera bajo un acuerdo de suministro vigente con
ventanas de entrega semanales, de modo que la decisión de colocación de
órdenes es **anterior** y **externa** al horizonte táctico: lo que el
modelo decide es la cantidad a recibir contra un programa ya negociado,
no el momento de ordenar. Bajo esa lectura el rezago físico está
absorbido por el proceso de planeación de abastecimiento y no pertenece
a este nivel de decisión. El supuesto es sostenible para insumos
nacionales y para importados con inventario en consignación o en almacén
fiscal local; **no** lo es para importación directa con tránsito
marítimo.

**Sesgo.** **Optimista**, en tres canales simultáneos: (i) subestima el
inventario de materia prima que la operación requiere realmente, y con
ello $NOA_t$ y el cargo de capital del Término II; (ii) sobrestima el
nivel de servicio alcanzable, porque permite reaccionar a un pico de
demanda dentro del mismo período; (iii) elimina un caso que en la
realidad es relevante y que el modelo no puede representar: cuando
$\tau^{lead}_r > \tau^{pay}_{k(r)}$, la empresa paga el insumo **antes**
de recibirlo, situación que invierte el signo de la contribución del
crédito comercial al capital de trabajo.

**Condición de validez.** El supuesto se sostiene mientras
$\tau^{lead}_r$ sea despreciable frente al período de discretización
semanal, criterio análogo al aplicado al arco inter-planta en la
[5.2](#sec:supuestos_operativos){reference-type="ref+Label"
reference="sec:supuestos_operativos"}. Se rompe en cuanto
$\tau^{lead}_r \geq 1$ semana para cualquier insumo con $a^1_{pr} > 0$,
lo que en la práctica ocurre para todo el subconjunto $R^I$ bajo
importación directa.

**Extensión disponible.** Ligar la recepción en $t$ a una orden colocada
en $t - \tau^{lead}_r$, con parámetro de frontera $O^0_{r,s}$ para las
órdenes en tránsito al inicio del horizonte --- sujeto a una condición
de consistencia análoga a (C1)--(C3) --- y añadir la cota de capacidad
de proveedor $Y_{r, l^S, l, t} \leq \overline{Y}_r$. Permanece lineal.
El lote mínimo de compra, en cambio, exige variables binarias y
pertenece a la extensión MILP.

## (S3) SEQ-2 sin variables elásticas {#sec:s3_seq2}

**Enunciado.** La restricción de
fijación ([\[eq:seq2_fix\]](#eq:seq2_fix){reference-type="ref"
reference="eq:seq2_fix"}) impone a SEQ-2 el flujo físico óptimo de
SEQ-1. Puesto que SEQ-1
optimiza ([\[eq:seq1\]](#eq:seq1){reference-type="ref"
reference="eq:seq1"}) ignorando por completo el bloque financiero, el
flujo resultante puede violar el límite de crédito
comercial ([\[eq:ap_max\]](#eq:ap_max){reference-type="ref"
reference="eq:ap_max"}), la caja
mínima ([\[eq:caja_min\]](#eq:caja_min){reference-type="ref"
reference="eq:caja_min"}) o la línea de
crédito ([\[eq:credito\]](#eq:credito){reference-type="ref"
reference="eq:credito"}). En ese caso **SEQ-2 es infactible** y la
comparación entre enfoques no está definida.

**Justificación de retenerlo.** La formulación elástica requiere
introducir variables de holgura con penalización de tipo *big-M* en las
tres cotas, lo que modifica el objetivo de SEQ-2 y obliga a decidir un
valor de $M$ que es arbitrario y que condiciona el resultado de la
comparación. Se prefiere mantener SEQ-2 en su forma estricta y **tratar
la infactibilidad como un resultado** en lugar de enmascararla con
holguras penalizadas: si el plan físicamente óptimo no es financiable,
ése es precisamente el hallazgo que justifica la integración, y una
holgura con $M$ mal calibrado lo convertiría en un número difícil de
interpretar.

**Sesgo.** Ninguno sobre el modelo integrado, que es independiente de
SEQ-1 y SEQ-2. El efecto es sobre la **interpretabilidad de la
comparación**: en el caso factible, la brecha
$\text{EVA}^{\text{INT}} - \text{EVA}(\text{SEQ-1} \cup \text{SEQ-2}) \geq 0$
es una medida válida del valor de la integración; en el caso infactible,
la brecha no es un número y el resultado debe reportarse de forma
cualitativa.

**Condición de validez.** Verificable de forma directa y **obligatoria
antes de reportar la comparación**: sustituir el flujo físico óptimo de
SEQ-1 en las ecs. ([\[eq:cxp\]](#eq:cxp){reference-type="ref"
reference="eq:cxp"}), ([\[eq:caja\]](#eq:caja){reference-type="ref"
reference="eq:caja"}) y ([\[eq:fm\]](#eq:fm){reference-type="ref"
reference="eq:fm"}) y comprobar que existe una asignación de
$\{C_t, DS_t, FI_t\}$ que
satisface ([\[eq:ap_max\]](#eq:ap_max){reference-type="ref"
reference="eq:ap_max"}),
([\[eq:caja_min\]](#eq:caja_min){reference-type="ref"
reference="eq:caja_min"})
y ([\[eq:credito\]](#eq:credito){reference-type="ref"
reference="eq:credito"}). La condición de
dimensionamiento ([\[eq:fact_credito\]](#eq:fact_credito){reference-type="ref"
reference="eq:fact_credito"}) es un indicador previo útil: sobre la
instancia miniatura el uso estimado del crédito comercial es del 28%,
holgura suficiente para anticipar factibilidad.

**Extensión disponible.** Añadir variables
$\sigma^{AP}_t, \sigma^{C}_t, \sigma^{DS}_t \geq 0$ a las tres cotas y
minimizar
$\sum_t NIE_t(1-\tau^{isr}) + M\sum_t(\sigma^{AP}_t + \sigma^{C}_t + \sigma^{DS}_t)$,
reportando la violación agregada como medida del déficit de
financiabilidad del plan secuencial. La calibración de $M$ debe
documentarse y acompañarse de un análisis de sensibilidad.

## (S4) El WIP no consume capacidad de almacenamiento {#sec:s4_wip}

**Enunciado.** La variable $W_{pt}$ no aparece en la restricción de
capacidad de
almacenamiento ([\[eq:cap_almacen\]](#eq:cap_almacen){reference-type="ref"
reference="eq:cap_almacen"}), aunque el material en retención ocupa
espacio físico en la Instalación A.

**Justificación de retenerlo.** A diferencia de los cuatro supuestos
restantes, éste **no es una tolerancia sino un resultado**. La retención
de calidad ocurre en los tanques del propio proceso de destilación, no
en el almacén $B_P$, de modo que su capacidad está gobernada por el
activo de transformación y no por el de almacenamiento. Y esa capacidad
ya está acotada de forma implícita, según la siguiente proposición.

**Proposición.** Con $\delta = 1$ y bajo la condición de
consistencia ([\[eq:cons_w0\]](#eq:cons_w0){reference-type="ref"
reference="eq:cons_w0"}), se cumple $W_{pt} = X_{pt}$ para todo
$p \in P$ y todo $t = 1, \dots, T$.

*Demostración.* Para $t = 1$, la
ec. ([\[eq:wip_frontera\]](#eq:wip_frontera){reference-type="ref"
reference="eq:wip_frontera"}) da
$W_{p,1} = W^0_p + X_{p,1} - X^0_{p,0}$, y por (C3) con $\delta = 1$ se
tiene $W^0_p = X^0_{p,0}$, de donde $W_{p,1} = X_{p,1}$. Supóngase
$W_{p,t-1} = X_{p,t-1}$. La
ec. ([\[eq:wip\]](#eq:wip){reference-type="ref" reference="eq:wip"}) da
$W_{pt} = W_{p,t-1} + X_{pt} - X_{p,t-1} = X_{pt}$. $\blacksquare$

**Corolario.** Sustituyendo en la restricción de capacidad de
destilación ([\[eq:cap_destilacion\]](#eq:cap_destilacion){reference-type="ref"
reference="eq:cap_destilacion"}),
$$\sum_{p \in P} kx_p \cdot W_{pt} \;=\; \sum_{p \in P} kx_p \cdot X_{pt} \;\leq\; capX_{B_P} \qquad \forall\, t = 1, \dots, T,$$
es decir, **el WIP está acotado de forma implícita en horas de capacidad
de línea**. Añadir $ki_p W_{pt}$ a la
ec. ([\[eq:cap_almacen\]](#eq:cap_almacen){reference-type="ref"
reference="eq:cap_almacen"}) sería redundante siempre que el volumen de
los tanques de retención esté dimensionado de manera consistente con la
capacidad horaria de la línea, lo cual es práctica estándar de diseño de
proceso.

**Sesgo.** Neutro bajo la condición de validez. El supuesto no altera ni
el valor del objetivo ni la región factible.

**Condición de validez.** Se requiere $\delta = 1$. Para $\delta > 1$ la
proposición se generaliza a $W_{pt} = \sum_{j=0}^{\delta-1} X_{p, t-j}$,
de donde $\sum_p kx_p W_{pt} \leq \delta \cdot capX_{B_P}$: la cota
implícita se relaja por un factor $\delta$ y el volumen de retención
**debe verificarse de forma explícita** contra el equivalente de
$\delta$ semanas de producción. Si esa verificación falla, hay que
introducir un nodo de capacidad dedicado en lugar de agregar $W_{pt}$ a
un almacén existente, porque los tanques de retención y el almacén $B_P$
no comparten espacio.

## (S5) Flete interno no capitalizado {#sec:s5_flete}

**Enunciado.** El costo de transporte interno $ky_\pi \cdot cy_{lj}$ se
reconoce como gasto del período en el término (v) de la
ec. ([\[eq:vco\]](#eq:vco){reference-type="ref" reference="eq:vco"}) y
en la ec. ([\[eq:ocf\]](#eq:ocf){reference-type="ref"
reference="eq:ocf"}), en lugar de capitalizarse en el costo unitario
$e_\pi$ del inventario situado aguas abajo del arco. Esto aplica a los
tres arcos internos: la transferencia $B_G \to B_P$, el arco
inter-planta $B_{wip} \to B_S$ y el despacho $B_T \to z$.

**Discrepancia normativa.** La NIF C-4 y la IAS 2 establecen que el
costo de los inventarios incluye los costos incurridos para llevarlos a
su ubicación actual. Bajo esa norma, una unidad de $p$ situada en $B_S$
debería valorarse en $e_p + ky_p \cdot cy_{B_{wip}, B_S}$, superior a la
unidad equivalente en $B_{wip}$. El modelo emplea un único $e_\pi$
independiente de la localización.

**Justificación de retenerlo.** El motivo es estructural, no de
comodidad. Capitalizar el flete interno obliga a sustituir el parámetro
$e_\pi$ por $e_{\pi l}$ --- dependiente de la localización --- y esa
sustitución se propaga a la base de
capital ([\[eq:noa\]](#eq:noa){reference-type="ref"
reference="eq:noa"}), al cambio de
inventario ([\[eq:ic\]](#eq:ic){reference-type="ref"
reference="eq:ic"}), al costo unitario de las presentaciones $e_q$ y a
la condición de
consistencia ([\[eq:consistencia_plost\]](#eq:consistencia_plost){reference-type="ref"
reference="eq:consistencia_plost"}). El parámetro deja de ser un vector
sobre $\Pi$ y pasa a estar definido sobre $PL^{Op}$, con una relación
recursiva a lo largo de los arcos de $PC$. La formulación permanece
lineal, pero el costo en trazabilidad y en verificabilidad de la
instancia es alto para una corrección que, bajo la condición de validez,
es inmaterial.

**Sesgo.** **Optimista**. Subvalúa el inventario situado en $B_P$, $B_S$
y $B_T$, y con ello $NOA_t$ y el cargo de capital del Término II. La
magnitud por unidad es el flete acumulado a lo largo de los arcos
recorridos hasta la localización en cuestión.

**Condición de validez.** El flete interno acumulado debe representar
una fracción inmaterial del costo unitario:
$$\frac{\sum_{\text{arcos recorridos}} ky_\pi \cdot cy_{lj}}{e_\pi} \;<\; 2\% \qquad \forall\, \pi \in \Pi.$$

**Advertencia sobre la instancia miniatura.** Con los parámetros
ilustrativos de la [13.3](#sec:mini_tecnicos){reference-type="ref+Label"
reference="sec:mini_tecnicos"}, el flete acumulado de una unidad de
$q_2$ hasta $B_T$ es $ky(cy_{B_{wip},B_S} + cy_{B_T,z}) = 2.50$ sobre un
costo $e_{q_2} = 29$, esto es el **8.6%**: la instancia miniatura **no**
satisface la condición de validez. Esto es deliberado y no invalida su
uso. La instancia miniatura existe para verificar la mecánica del modelo
--- balances, articulación contable, cobertura temporal --- y sus
parámetros de costo se eligieron por legibilidad aritmética, no por
representatividad económica. La condición debe verificarse sobre la
**instancia real** de 52 semanas con los valores instanciados de
$cy_{lj}$, y su resultado reportarse junto con los del modelo. Si el
cociente excediera el 2%, la extensión deja de ser opcional.

**Extensión disponible.** Definir el costo unitario sobre $PL^{Op}$
mediante la recursión
$$e_{\pi j} \;=\; e_{\pi l} + ky_\pi \cdot cy_{lj} \qquad \forall\, (\pi, (l,j)) \in PC \;\text{ con } l, j \notin L^S,$$
inicializada en el punto de entrada de cada producto a la red, y
sustituir $e_\pi$ por $e_{\pi l}$ en las
ecs. ([\[eq:noa\]](#eq:noa){reference-type="ref" reference="eq:noa"})
y ([\[eq:ic\]](#eq:ic){reference-type="ref" reference="eq:ic"}).
Requiere verificar que la recursión esté bien definida, es decir que el
subgrafo de arcos internos sea acíclico --- lo cual se cumple en la
topología de la [1](#fig:red_cadena){reference-type="ref+Label"
reference="fig:red_cadena"}.

## Protocolo de vigencia de los supuestos {#sec:vigencia_supuestos}

Los cinco supuestos no son permanentes: cada uno tiene una condición
verificable cuya falla obliga a implementar la extensión
correspondiente. La verificación debe ejecutarse **con los datos de la
instancia real**, no con los de la instancia miniatura, y su resultado
debe acompañar al reporte de resultados del modelo.

::: {#tab:vigencia_supuestos}
  **\#**   **Qué verificar con la instancia instanciada**                                                                                                      **Acción si falla**
  -------- --------------------------------------------------------------------------------------------------------------------------------------------------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  (S1)     Si $\alpha < 1$: cuantificar $(1-\alpha) e_p X_{p,T}(1-\tau^{isr})$ y compararlo con el margen semanal medio                                        Fijar $\alpha = 1$ o diferir el costo de conversión ([15.1](#sec:s1_alpha){reference-type="ref+Label" reference="sec:s1_alpha"})
  (S2)     $\tau^{lead}_r$ efectivo de cada insumo con $a^1_{pr} > 0$, en particular de $R^I$; y si existe algún $r$ con $\tau^{lead}_r > \tau^{pay}_{k(r)}$   Introducir el rezago de abastecimiento y las cotas de proveedor ([15.2](#sec:s2_leadtime){reference-type="ref+Label" reference="sec:s2_leadtime"})
  (S3)     Factibilidad financiera del flujo óptimo de SEQ-1 frente a (23), (28) y (29)                                                                        Reportar la infactibilidad como resultado, o adoptar la formulación elástica ([15.3](#sec:s3_seq2){reference-type="ref+Label" reference="sec:s3_seq2"})
  (S4)     Que $\delta = 1$; y volumen de los tanques de retención frente a una semana de producción a $capX_{B_P}$                                            Si $\delta > 1$, verificar contra $\delta$ semanas e introducir un nodo de capacidad dedicado ([15.4](#sec:s4_wip){reference-type="ref+Label" reference="sec:s4_wip"})
  (S5)     Cociente de flete interno acumulado sobre $e_\pi$, para cada producto y localización                                                                Capitalizar mediante la recursión $e_{\pi j} = e_{\pi l} + ky_\pi cy_{lj}$ ([15.5](#sec:s5_flete){reference-type="ref+Label" reference="sec:s5_flete"})

  : Verificación de vigencia de los supuestos de simplificación. La
  columna de acción indica qué hacer si la condición falla.
:::

**Interacción con el esquema de horizonte rodante.** Tres de los cinco
supuestos se **debilitan** al pasar a reoptimización periódica y dos se
**agravan**. Conviene tenerlo presente al diseñar el gemelo digital:

- **(S1) se debilita.** El sesgo de borde afecta únicamente a los
  últimos $\delta$ períodos de cada ventana, que nunca se implementan:
  sólo se ejecuta la primera semana de cada reoptimización.

- **(S4) se debilita.** La proposición $W_{pt} = X_{pt}$ es
  independiente de la longitud de la ventana y sigue siendo exacta.

- **(S5) se mantiene.** El sesgo es proporcional al inventario en
  tránsito y no depende del horizonte.

- **(S2) se agrava.** Una ventana corta reduce la anticipación
  disponible justo cuando el rezago físico de abastecimiento se vuelve
  determinante. El piso de ventana
  $T^{window} \geq \max\{\tau_{z^{exp}}, \tau^{pay}_I, \tau^{lead}_r + \delta\}$
  depende directamente de un parámetro que esta versión no modela, de
  modo que relajar (S2) es prerrequisito del gemelo digital, no una
  mejora opcional.

- **(S3) se agrava.** En reoptimización, una infactibilidad de SEQ-2 en
  cualquier ventana interrumpe la serie de comparación completa. Si el
  objeto de estudio es la brecha entre enfoques a lo largo del tiempo,
  la formulación elástica deja de ser opcional.

# Registro de cambios de la versión 2.5 {#registro-de-cambios-de-la-versión-2.5 .unnumbered}

  **Cambio**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  **Ubicación**
  ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  Sección de supuestos de simplificación declarados (S1)--(S5). Documenta las cinco simplificaciones identificadas, evaluadas y deliberadamente retenidas, cada una con enunciado formal, justificación, dirección y magnitud del sesgo, condición de validez verificable y extensión disponible. Se añade un protocolo de vigencia y el análisis de cómo interactúa cada supuesto con el esquema de horizonte rodante.                                                                                                                                                                                                                       [15](#sec:supuestos_declarados){reference-type="ref+Label" reference="sec:supuestos_declarados"}, [12](#tab:supuestos_declarados){reference-type="ref+Label" reference="tab:supuestos_declarados"}, [13](#tab:vigencia_supuestos){reference-type="ref+Label" reference="tab:vigencia_supuestos"}
  Hallazgo incorporado en (S4): se demuestra que con $\delta = 1$ y la condición ([\[eq:cons_w0\]](#eq:cons_w0){reference-type="ref" reference="eq:cons_w0"}) se cumple $W_{pt} = X_{pt}$, de donde la restricción de capacidad de destilación ([\[eq:cap_destilacion\]](#eq:cap_destilacion){reference-type="ref" reference="eq:cap_destilacion"}) acota implícitamente el WIP en horas de línea. El supuesto pasa de tolerado a justificado y la adición de $ki_p W_{pt}$ a la ec. ([\[eq:cap_almacen\]](#eq:cap_almacen){reference-type="ref" reference="eq:cap_almacen"}) resulta redundante bajo dimensionamiento estándar de tanques.   [15.4](#sec:s4_wip){reference-type="ref+Label" reference="sec:s4_wip"}
  Advertencia sobre la instancia miniatura en (S5): con sus parámetros ilustrativos el flete interno acumulado representa el 8.6% de $e_{q_2}$ y por tanto **no** satisface la condición de validez del 2%. Se declara que la elección es deliberada --- la instancia verifica mecánica, no representatividad económica --- y que la condición debe evaluarse sobre la instancia real.                                                                                                                                                                                                                                                        [15.5](#sec:s5_flete){reference-type="ref+Label" reference="sec:s5_flete"}

**Estado del documento.** Con esta versión quedan cerrados los tres
bloques de corrección y declarado el alcance de la formulación. Las
veinte áreas de oportunidad identificadas en la revisión inicial están
resueltas (quince) o documentadas como simplificación deliberada con su
condición de validez y su extensión (cinco). No subsisten omisiones
tácitas.

# Registro de cambios de la versión 2.4 {#registro-de-cambios-de-la-versión-2.4 .unnumbered}

::: xltabular
c X \>

p3.8cm

**\#** & **Cambio** & **Ubicación**\

**\#** & **Cambio** & **Ubicación**\

9 & Condiciones de consistencia de los datos de frontera (C1)--(C8). Los
saldos $AR^0_z$, $AP^0$ y $W^0_p$ deben reproducir exactamente sus
historiales pendientes; se declaran los soportes de $S^0$, $B^0$ y
$X^0$; se acotan el estado inicial de almacén, caja, deuda y crédito
comercial, que las restricciones indexadas $t = 1,\dots,T$ no cubrían; y
se exige $X^{ref}_p = X^0_{p,0}$. Se documenta el síntoma observable de
cada violación y se formaliza la implicación para el horizonte rodante:
la verificación debe ejecutarse como rutina de admisión de datos en
**cada** ciclo de reoptimización. &
[5.3](#sec:consistencia_iniciales){reference-type="ref+Label"
reference="sec:consistencia_iniciales"},
[8](#tab:consecuencias_c){reference-type="ref+Label"
reference="tab:consecuencias_c"},
[8.7](#sec:inv_frontera){reference-type="ref+Label"
reference="sec:inv_frontera"},
[8.9](#sec:cap_almacen){reference-type="ref+Label"
reference="sec:cap_almacen"},
[8.11](#sec:regularizacion){reference-type="ref+Label"
reference="sec:regularizacion"},
[8.3](#sec:wip){reference-type="ref+Label" reference="sec:wip"}\
19 & Condiciones de factibilidad agregada (V1)--(V6). La prueba se
formula en **equivalente granel** vía $a^2_{qp}$, de forma **acumulada**
en lugar de períodica --- para no penalizar la anticipación de
producción que la política *make-to-stock* habilita --- e incorpora el
rezago $\delta$, el *pipeline* inicial $G^0_p$ y la meta terminal
$g^T_p$. Se añade la condición de dimensionamiento del crédito comercial
y la descomposición obligatoria
$capX_{B_P} = 168 \cdot n^{lin} \cdot \eta^{ef}$. Ninguna se añade al
LP: son condiciones necesarias implicadas, empleadas como diagnóstico
previo para no confundir una restricción física con una mala calibración
de $p^{lost}_{qz}$. &
[11](#sec:factibilidad_capacidad){reference-type="ref+Label"
reference="sec:factibilidad_capacidad"}, ecs. (V1)--(V6)\
20 & Identidad de articulación contable (A1), **demostrada** en siete
pasos, e instancia miniatura completamente parametrizada ($T=6$) con
matriz de 19 pruebas ejecutables. La demostración verifica
retroactivamente las correcciones del bloque anterior: la depreciación
se cancela de forma exacta en el Paso 6 --- lo que sólo ocurre si
$dep^{sem}$ está en $fc_t$ y ausente de $ec'_t$ --- y el Paso 7 cierra
únicamente si la base gravable es $EBIT_t - NIE_t$. Se añade un catálogo
de firmas de residuo que localiza el error a partir del patrón de
$\varepsilon_t$. & [12](#sec:articulacion){reference-type="ref+Label"
reference="sec:articulacion"},
[10](#tab:residuos){reference-type="ref+Label"
reference="tab:residuos"},
[13](#sec:instancia_mini){reference-type="ref+Label"
reference="sec:instancia_mini"},
[11](#tab:matriz_verificacion){reference-type="ref+Label"
reference="tab:matriz_verificacion"}\
:::

**Relación entre los tres cambios.** No son independientes. Las
condiciones (C1)--(C8) determinan $NOA_0$ y $E_0$, que son el punto de
partida de la identidad (A1); sin ellas el residuo $\varepsilon_t$ sería
no nulo por una causa de datos y no de formulación, y el diagnóstico de
la [10](#tab:residuos){reference-type="ref+Label"
reference="tab:residuos"} resultaría engañoso. A su vez, las condiciones
(V1)--(V6) dependen de $W^0_p$ e $I^0_{\pi l}$ a través de $G^0_p$, de
modo que una violación de (C3) invalida simultáneamente la prueba de
factibilidad. El orden de ejecución del protocolo de la
[13.8](#sec:mini_protocolo){reference-type="ref+Label"
reference="sec:mini_protocolo"} --- consistencia, factibilidad,
resolución, articulación, mutación --- no es arbitrario: cada paso
presupone el anterior.

# Registro de cambios de la versión 2.3 {#registro-de-cambios-de-la-versión-2.3 .unnumbered}

::: xltabular
c X \>

p3.8cm

**\#** & **Cambio** & **Ubicación**\

**\#** & **Cambio** & **Ubicación**\

3, 6, 13 & Convención de devengo unificada. El cargo de capital pasa a
calcularse sobre el **saldo de apertura** $(fa_{t-1}+NOA_{t-1})$ con
rango $t=1,\dots,T$, por razones de causalidad contable; el valor
terminal se incorpora como **cargo de continuación** en $T+1$
(Término VI) y no como crédito al valor en libros, formulación esta
última que duplicaría el margen ya reconocido en el NOPAT. Se documenta
la demostración del doble conteo y se hace explícito que la suma II$+$VI
coincide numéricamente con el rango de la v2.2, cuyo conjunto de
términos era correcto pero cuya justificación era *ad hoc*. &
Ec. ([\[eq:objetivo\]](#eq:objetivo){reference-type="ref"
reference="eq:objetivo"}),
[6.1](#sec:convencion_cargos){reference-type="ref+Label"
reference="sec:convencion_cargos"},
[7.2](#sec:noa){reference-type="ref+Label" reference="sec:noa"},
[6.3](#sec:consistencia_plost){reference-type="ref+Label"
reference="sec:consistencia_plost"}\
8 & Depreciación conciliada entre base de capital y utilidad. Se
introduce $fc^{caja}_t$ y se declara $fc_t = fc^{caja}_t + dep^{sem}$,
con lo que $EBIT_t$ y la base gravable quedan medidos después de
depreciación mientras $ec'_t$ la excluye por no ser desembolsable. Se
separa el escudo fiscal de la deuda de largo plazo (incorporado en
$i^{wacc}$, reconocido en $ec'_t$) del de la deuda de corto plazo
(reconocido en $TAX_t$). Se añade la condición de dominio $fa_T \geq 0$.
& [5.1](#sec:depreciacion){reference-type="ref+Label"
reference="sec:depreciacion"}, ecs. (P1)--(P3),
[7](#tab:conciliacion_dep){reference-type="ref+Label"
reference="tab:conciliacion_dep"},
[7.6](#sec:ebit){reference-type="ref+Label" reference="sec:ebit"},
[9.5](#sec:caja){reference-type="ref+Label" reference="sec:caja"}\
12 & Costo financiero neto de corto plazo incorporado a la función
objetivo (Término V) mediante la variable $NIE_t$. Para mantener la
consistencia entre devengo y efectivo, $TAX_t$ pasa a gravar
$EBIT_t - NIE_t$. Se añaden la regla de calibración de $i^{wacc}$ sobre
la estructura de capital de largo plazo, el análisis de traslape con la
identidad de balance y la formulación exacta alternativa, así como la
condición paramétrica $i^{DS} > i^{FI}$. SEQ-2 se reformula para ser
conmensurable con el modelo integrado. &
[7.7](#sec:nie){reference-type="ref+Label" reference="sec:nie"},
ec. (7b), [7.8](#sec:tax){reference-type="ref+Label"
reference="sec:tax"},
[6.2](#sec:consistencia_wacc){reference-type="ref+Label"
reference="sec:consistencia_wacc"},
[6.4](#sec:consistencia_tasas){reference-type="ref+Label"
reference="sec:consistencia_tasas"},
[10.2](#sec:seq2){reference-type="ref+Label" reference="sec:seq2"}\
:::

**Verificaciones de consistencia introducidas en esta versión.** Tres
identidades deben cumplirse en cualquier solución y conviene
automatizarlas como pruebas de regresión:

1.  *Escudo fiscal del crédito de corto plazo:* el efecto neto del
    interés sobre la caja, $-NIE_t + \tau^{isr} NIE_t$, debe igualar el
    Término V período a período.

2.  *Unicidad del cargo de capital:* cada saldo $NOA_0, \dots, NOA_T$
    debe recibir exactamente un cargo; la suma de los Términos II y VI
    debe igualar $i^{wacc}\sum_{t=0}^{T}(fa_t + NOA_t)$.

3.  *Ausencia de arbitraje financiero:*
    bajo ([\[eq:consistencia_tasas\]](#eq:consistencia_tasas){reference-type="ref"
    reference="eq:consistencia_tasas"}), en el óptimo debe cumplirse
    $DS_t \cdot FI_t = 0$ para todo $t$; su violación indica un error de
    signo en $FM_t$ o en $NIE_t$.

# Registro de cambios de la versión 2.2 {#registro-de-cambios-de-la-versión-2.2 .unnumbered}

  **\#**   **Cambio**                                                                                                                                                                                                                                                                                                                                                                           **Ubicación**
  -------- ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  1        Arco $B_G \to B_P$ habilitado para $R^N \cup R^I$. Sin esta corrección el $\mathit{BOM}^1$ no puede consumir insumos importados y el modelo es infactible. Se añade tabla de auditoría de $\mathit{TC}$, $PC$ y $PL^{Op}$ que hace verificable la cardinalidad $|PC| = 61$.                                                                                                          [1](#fig:red_cadena){reference-type="ref+Label" reference="fig:red_cadena"}, [2](#tab:auditoria_pc){reference-type="ref+Label" reference="tab:auditoria_pc"}, ecs. ([\[eq:balance_bp\]](#eq:balance_bp){reference-type="ref" reference="eq:balance_bp"}) y ([\[eq:balance_bg\]](#eq:balance_bg){reference-type="ref" reference="eq:balance_bg"}), [5.2](#sec:supuestos_operativos){reference-type="ref+Label" reference="sec:supuestos_operativos"}
  2        Declaración explícita del dominio de las variables auxiliares. Siete variables se declaran libres de signo y tres no negativas, con justificación individual. Previene el error de construcción inducido por el dominio no negativo por omisión de Pyomo, GAMS, JuMP y AMPL.                                                                                                         [4.1](#sec:dominio_aux){reference-type="ref+Label" reference="sec:dominio_aux"}, ecs. (D1) y (D2)
  7        Unificación de las ecuaciones de cuentas abiertas mediante los operadores de historial $\widehat{S}$ y $\widehat{B}$. Se eliminan las particiones (21a/b), (22a/b) y (26a/b), y se cierra el hueco de cobertura en $t \in (\tau^{pay}_N, \tau^{pay}_I]$ que dejaba $AP_t$ subdeterminado. Se añaden la propiedad de pago único y la identidad de articulación devengado--efectivo.   [9.1](#sec:operadores_historial){reference-type="ref+Label" reference="sec:operadores_historial"}, ecs. (H1), (H2), ([\[eq:cxc\]](#eq:cxc){reference-type="ref" reference="eq:cxc"}), ([\[eq:cxp\]](#eq:cxp){reference-type="ref" reference="eq:cxp"}) y ([\[eq:om\]](#eq:om){reference-type="ref" reference="eq:om"})

**Nota:** Los cinco puntos que este registro listaba originalmente como
pendientes --- coherencia del factor de avance $\alpha$, *lead time* de
abastecimiento, elasticidad de las cotas financieras en SEQ-2, consumo
de capacidad de almacenamiento por el WIP y capitalización del flete
interno --- fueron evaluados y se retienen como simplificaciones
deliberadas. Su alcance, sesgo, condición de validez y extensión
disponible se documentan en la
[15](#sec:supuestos_declarados){reference-type="ref+Label"
reference="sec:supuestos_declarados"}; dejan por tanto de ser pendientes
y pasan a ser supuestos declarados.
