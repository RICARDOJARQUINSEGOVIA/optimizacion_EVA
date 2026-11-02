# Balance del proyecto y protocolo de validación para tu modelo
**26 de octubre de 2026**

---

# PARTE 1 — Qué se ha logrado

## 1.1 Lo que existe y está auditado

**Un corpus caracterizado.** 73 registros evaluados a texto completo, 65 incluidos, codificados en 51 variables de los bloques B, C, D, E, F, G, H, I y J. Los conteos de todos los bloques están en `Tablas_bloques_Janova_20261026.xlsx` con fórmulas vivas sobre la hoja `Datos`.

**Un instrumento con confiabilidad medida, no supuesta.** Dos rondas ciegas con muestreo aleatorio, semilla fija y archivo sellado. Estadísticos preinscritos: acuerdo observado, kappa de Cohen y AC1 de Gwet como principal, con criterio de éxito fijado antes de medir.

**Nueve reglas de operacionalización escritas**, cada una con su pregunta, su criterio, los artículos afectados, la decisión del investigador y su justificación: R13, R13a, R13b, R13c para D2; R10, R10a, R10b, R10c, R10d para G12. En `Reglas v1.1` del libro maestro.

**Un hallazgo metodológico cuantificado.** Las reglas de un marco publicado no eran reproducibles tal como están enunciadas: κ = −0.194 en D2 y +0.02 en G12. Tras operacionalizarlas, D2 cumple el criterio (90 %, AC1 = 0.861) y G12 no (50 %, AC1 = 0.336). La proporción de artículos que cumplen la convención de Janová pasa de 12.3 % a 26.2 % según cómo se lea una sola regla.

**Trazabilidad completa.** 113 entradas fechadas en `Registro de cambios`, incluidos mis dos errores de codificación y los dos intentos de fase 4 descartados hoy. Cualquier número del artículo se rastrea a su archivo y su hoja.

**La propuesta de validación del instrumento, redactada.** `Propuesta_validacion_v1.0_20261026.md`, lista para pegar.

## 1.2 Lo que esto vale

El trabajo de confiabilidad es lo que convierte los conteos del corpus en números citables. Sin él, la tabla de porcentajes sería una opinión bien ordenada. Con él, es una medición con su error declarado. Eso ya está hecho y no hay que volver a tocarlo.

---

# PARTE 2 — Distancia respecto del propósito original

Aquí va lo incómodo, y conviene decirlo entero.

## 2.1 El propósito era otro

Lo que importaba era **tener un protocolo de validación aplicable a tu modelo**. El estudio del corpus era el medio: averiguar qué hace y qué no hace la literatura para fundamentar un protocolo que no fuera una lista de buenas intenciones.

**Estado del protocolo hoy: no existe.** Ni una línea escrita. Lo que existe es la base empírica para escribirlo.

## 2.2 Qué se acercó al propósito

- La caracterización del corpus **es exactamente** la base que hacía falta. Sabes, con números, dónde falla la literatura. Eso es lo que permite un protocolo con justificación empírica en lugar de uno copiado de un marco.
- Los bloques G y H dan el diagnóstico más útil: **la validación operativa del campo es, casi siempre, el propio autor comparando su modelo contra un benchmark que él eligió.** G11 «solo autores» = 59 de 65 (90.8 %).
- El trabajo de confiabilidad enseñó algo transferible a tu propio modelo: **las afirmaciones de validación son ambiguas si no se operacionalizan.** Eso vale igual para juzgar 65 artículos que para redactar el tuyo.

## 2.3 Qué se alejó

- **Dos días en la confiabilidad del instrumento.** La primera mitad era necesaria. La segunda —la cascada R10a, R10b, R10c, R10d— se desbordó: su rendimiento es un análisis de sensibilidad interesante, no un protocolo mejor.
- **Esa cascada consumió el corpus.** 53 de 65 cierres leídos durante el desarrollo de las reglas, lo que impide medir la confiabilidad de las reglas finales. Es un costo real y ya está declarado como limitación.
- **El objeto se desplazó.** Durante dos días el proyecto fue «medir la confiabilidad de un libro de códigos», no «validar un modelo de optimización con objetivos financieros». Son cosas distintas y la segunda es la tuya.

## 2.4 La buena noticia

**El corpus ya no necesita nada más.** No hay que codificar otra variable, ni medir otra confiabilidad, ni conseguir otro codificador para que el protocolo exista. Todo lo que el protocolo necesita está en `Tablas_bloques_Janova_20261026.xlsx`. La Parte 4 de este documento lo escribe.

---

# PARTE 3 — Qué falta para un paper de calidad

En orden de importancia, no de esfuerzo.

**1. Una pregunta de investigación, escrita en una frase.** Hoy el proyecto tiene un método impecable y un objeto implícito. Candidata, a partir de lo que tienes: *¿en qué medida las prácticas de validación reportadas en la literatura de optimización de cadenas de suministro con objetivos financieros permiten juzgar si un modelo es apto para su uso declarado, y qué protocolo mínimo lo permitiría?* Esa pregunta une las dos mitades: el diagnóstico y el protocolo.

**2. El protocolo como contribución prescriptiva.** Un paper que solo dice «el 12 % cumple la convención» es descriptivo y se cita poco. Uno que dice «el 12 % cumple, estas son las cinco omisiones que lo explican, y este es el protocolo que las cierra» es prescriptivo y se usa. La Parte 4 es ese protocolo.

**3. Comparación explícita con Janová et al. (2024).** Tú aplicas su convención a otro corpus y además mides la confiabilidad de sus variables, que ellos no reportan. Decir eso de frente es tu aportación más defendible: *no replicamos su resultado, auditamos su instrumento*.

**4. El hallazgo del umbral, en el abstract.** 12.3 % frente a 26.2 % sobre los mismos 65 artículos es el número que hace que alguien lea el resto. Hoy está en el análisis de sensibilidad, que nadie lee.

**5. Las cifras de vacío absoluto, bien contadas.** Tres celdas en cero sobre 65 artículos: **G5 (prueba experimental o piloto) = 0**, **H5 (revisión por un experto financiero o de la empresa) = 0**, **H2 «fuente citada» para los parámetros financieros = 0**. Un cero sobre 65 es un resultado, y es el más fuerte que tienes.

**6. El codificador humano, si aparece.** Mejora la sección de confiabilidad. No bloquea nada. Es lo último de la lista, no lo primero.

**7. Las seis observaciones pendientes** de la hoja `Otras variables (no ejecutado)`. Ninguna mueve J1–J5. Revísalas al final.

---

# PARTE 4 — Protocolo de validación para tu modelo

## 4.1 El principio

Cada elemento del protocolo lleva **la frecuencia con que el corpus lo cumple**. Eso hace dos cosas: justifica el protocolo con evidencia en lugar de autoridad, y te permite decir en tu propio artículo *«aplicamos una comprobación que 1 de 65 estudios comparables reporta»*, que es una afirmación verificable y fuerte.

Y define la estrategia: **no intentes cumplirlo todo. Cumple lo que casi nadie cumple**, porque ahí está el diferencial y, además, suele ser lo más barato.

## 4.2 Nivel 1 — Lo que debes cumplir porque todos lo cumplen

Si fallas aquí, el modelo queda por debajo de la media del campo.

| # | Qué hacer | Evidencia que produces | Corpus |
|---|---|---|---|
| 1.1 | Enunciar el propósito del modelo en una frase, con su métrica | Una frase en la introducción y la misma en el cierre | — |
| 1.2 | Validación conceptual: justificar la forma del modelo | Párrafo con la revisión de alternativas de modelado | E2: 64/65 (98.5 %) |
| 1.3 | Comparar contra al menos un comparador | Tabla de resultados contra el comparador | G4: 54/65 (83.1 %) |
| 1.4 | **Cerrar contrastando resultados con el propósito declarado** | Un párrafo de conclusión que reenuncie el objetivo y diga si se alcanzó | G12 = Sí: 49/65 (75.4 %) |

Sobre 1.4: es la variable que nos costó dos días. Hazlo explícito y sin ambigüedad: reenuncia el objetivo con sus palabras, da el número sobre su métrica, y di si se alcanzó o en qué no. Tres oraciones.

## 4.3 Nivel 2 — Lo que te separa de la mayoría

Entre el 35 % y el 55 % del corpus lo hace. Cumplirlo te pone en la mitad buena.

| # | Qué hacer | Evidencia | Corpus | Esfuerzo |
|---|---|---|---|---|
| 2.1 | **Verificación computacional**: comprobar que el código resuelve lo que dices | Pruebas con instancias de solución conocida, casos degenerados, réplicas | E1: 32/65 (49.2 %) | 1-2 días |
| 2.2 | **Análisis de sensibilidad** sobre los parámetros operativos | Tabla o tornado de los parámetros clave | G7: 34/65 (52.3 %) | 1 día |
| 2.3 | **Prueba de concepto con datos reales** de una empresa | Un caso con datos de una organización identificada | D1: 24/65 (36.9 %) · F1 «empresa real»: 18/65 (27.7 %) | depende del acceso |
| 2.4 | **Declarar limitaciones con su efecto**, no como trabajo futuro | Un párrafo que diga qué no captura el modelo y **cómo afecta** a lo reportado | D2 = Sí: 25/65 (38.5 %) | 1 hora |

Sobre 2.4: aquí te sirve literalmente tu propia R13. El 61.5 % del corpus nombra carencias sin decir su efecto, o no las nombra. Escribir «el modelo no considera X, de modo que los costos reportados son una cota inferior en el escenario Y» te pone en el 38.5 %, y cuesta una hora.

## 4.4 Nivel 3 — Lo que casi nadie hace, y por eso vale

Menos del 20 % del corpus. Aquí está tu ventaja real.

| # | Qué hacer | Evidencia | Corpus | Esfuerzo |
|---|---|---|---|---|
| 3.1 | **Validación de cara con externos**: que alguien ajeno revise si el modelo representa el problema | Un párrafo nombrando el perfil del revisor y qué objetó | **G2: 1/65 (1.5 %)** | 2 reuniones |
| 3.2 | **Validación cruzada**: comprobar el modelo contra un conjunto de datos distinto del de calibración | Tabla con el desempeño fuera de muestra | **G3: 1/65 (1.5 %)** | 2-3 días |
| 3.3 | **Validación por expertos del dominio** | Protocolo de la consulta y resultado | G6: 3/65 (4.6 %) | 2 reuniones |
| 3.4 | **Condiciones extremas y estrés**: demanda cero, capacidad infinita, disrupción total | Tabla de comportamiento en los límites | G8: 6/65 (9.2 %) | 1 día |
| 3.5 | **Co-desarrollo con el usuario** del modelo | Descripción del proceso y de qué cambió por su intervención | G9: 5/65 (7.7 %) | según el proyecto |
| 3.6 | **Validar los datos de entrada**, no solo el modelo | Procedencia, rango, consistencia y tratamiento de faltantes | F2 = Sí: 5/65 (7.7 %) | 1 día |
| 3.7 | **Comparar contra la práctica actual**, no contra un benchmark ideal | Tabla contra la política vigente de la organización | G4b «práctica actual»: 10/65 (15.4 %) | depende del acceso |

Sobre 3.7: es el más subestimado. El 21.5 % del corpus compara contra un «benchmark ideal» que el propio autor construye, lo que casi garantiza ganar. Comparar contra lo que la empresa hace hoy es más difícil y mucho más creíble.

## 4.5 Nivel 4 — La capa financiera, que es donde el campo está vacío

Tu modelo tiene objetivos financieros. **Es aquí donde puedes ser el primero**, y no es una exageración retórica: son ceros sobre 65.

| # | Qué hacer | Evidencia | Corpus | Esfuerzo |
|---|---|---|---|---|
| 4.1 | **Consistencia de la métrica financiera**: que EVA, CCC, VPN o lo que uses esté definido como lo define la disciplina financiera, con su fórmula | La fórmula y su fuente contable | H1 = Sí: 17/21 de los que aplican (81 %) | 2 horas |
| 4.2 | **Citar la fuente de cada parámetro financiero**: tasa de descuento, costo de capital, plazos de crédito | Tabla de parámetros con su fuente | **H2 «fuente citada»: 0/65.** 20 de 24 usan supuestos sin fuente (83.3 %) | 1 día |
| 4.3 | **Conciliar con datos contables reales** de una empresa | Comparación de tu métrica contra el estado financiero | H3: 5/65 (7.7 %) | según el acceso |
| 4.4 | **Sensibilidad a los parámetros financieros**, separada de la operativa | Tabla propia: ¿cuánto se mueve la decisión si la tasa cambia 200 pb? | H4 = Sí: 7/65 (10.8 %). El 66.2 % no la hace | 1 día |
| 4.5 | **Revisión por un experto financiero o del área de finanzas de la empresa** | Párrafo con el perfil, qué revisó y qué objetó | **H5: 0/65** | 2 reuniones |

**Lee la tabla anterior como una oportunidad, no como una carga.** 4.2 y 4.5 son ceros absolutos sobre 65 artículos de optimización con objetivos financieros. Citar la fuente de tu tasa de descuento cuesta un día. Que un contador o un director financiero revise tu formulación y escribas qué objetó cuesta dos reuniones. Con esas dos cosas, tu artículo reporta **dos comprobaciones que ningún estudio comparable reporta**, y eso es una frase que puedes escribir en el abstract.

## 4.6 El protocolo mínimo que yo te propondría

Si tuvieras que elegir, y vas con el tiempo encima, estos siete. Cubren el Nivel 1 completo, lo barato del 2 y el 3, y los dos ceros del 4:

1. **1.4** — cierre que contrasta resultados con el propósito (3 oraciones)
2. **2.4** — limitaciones con su efecto declarado (1 hora)
3. **2.1** — verificación computacional (1-2 días)
4. **2.2** — sensibilidad operativa (1 día)
5. **4.1 + 4.2** — métrica financiera definida y parámetros con fuente (1 día)
6. **4.4** — sensibilidad financiera separada (1 día)
7. **4.5** — un revisor financiero externo (2 reuniones)

**Unos 5 días de trabajo más dos reuniones.** Y con eso tu modelo queda validado por encima del percentil 90 del corpus que acabas de caracterizar, con la frecuencia empírica de cada comprobación citable desde tu propia tabla.

## 4.7 Cómo encaja esto con el paper

Lo natural es que el protocolo **cierre el artículo del corpus** como su contribución prescriptiva: diagnóstico primero, protocolo después, derivado del diagnóstico. Así el trabajo de estos días paga dos veces y el artículo deja de ser descriptivo.

Y la aplicación del protocolo a tu modelo es el **capítulo siguiente**, donde el protocolo ya no se propone sino que se ejecuta. Ese capítulo tiene algo que casi ningún trabajo de modelado tiene: un protocolo de validación con justificación empírica y una tabla que dice, comprobación por comprobación, cuántos estudios comparables la reportan.

---

# PARTE 5 — Qué hago yo ahora, si me lo pides

- Convertir la Parte 4 en una **tabla de verificación** que marques conforme la ejecutes sobre tu modelo, con una columna de evidencia y otra de estado.
- Redactar el **apartado prescriptivo** del artículo a partir de la Parte 4, en registro de paper y con las frecuencias citadas.
- Escribir la **pregunta de investigación y el abstract** con el hallazgo del umbral delante.
- Montar el **plan de verificación computacional** (2.1) para tu modelo concreto, en cuanto me digas en qué está implementado.

Dime por dónde y lo tienes hoy.
