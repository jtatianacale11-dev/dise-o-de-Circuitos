# Semana 3 · Mapas de Karnaugh, indiferentes y NAND/NOR

[⬅ Semana 2](semana-02.md) · [Índice](../README.md) · [Semana 4 ➡](semana-04.md)

## 🎯 Objetivos

- **Sesión 1:** obtener la expresión mínima en SOP y POS de funciones de 3 y 4 variables con mapas de Karnaugh, justificando cada agrupación.
- **Sesión 2:** minimizar una función de 4 variables con condiciones indiferentes e implementarla solo con NAND, cuantificando el ahorro en compuertas y CI.

---

# Sesión 1 · Mapas de Karnaugh

## 1. La clave: el teorema de adyacencia

`X·Y + X·Y' = X`. Si dos minitérminos difieren en **una sola variable**, esa variable desaparece.

Ejemplo: `m4 = AB'C'` y `m5 = AB'C` se unen en `AB'`.

## 2. ¿Por qué el orden 00, 01, 11, 10?

Es el **código Gray**: cada celda cambia un solo bit respecto a la siguiente, y la última es vecina de la primera. En binario normal (00, 01, 10, 11) hay saltos de 2 bits y se pierden adyacencias.

## 3. Mapas

**3 variables** (filas = A, columnas = BC en Gray):

| A \ BC | 00 | 01 | 11 | 10 |
|:-:|:-:|:-:|:-:|:-:|
| **0** | 0 | 1 | 3 | 2 |
| **1** | 4 | 5 | 7 | 6 |

**4 variables** (filas = AB, columnas = CD, ambas en Gray):

| AB \ CD | 00 | 01 | 11 | 10 |
|:-:|:-:|:-:|:-:|:-:|
| **00** | 0 | 1 | 3 | 2 |
| **01** | 4 | 5 | 7 | 6 |
| **11** | 12 | 13 | 15 | 14 |
| **10** | 8 | 9 | 11 | 10 |

Atención: m3 va antes que m2, y m15 antes que m14.

## 4. Reglas para agrupar

1. **Tamaño 2ᵏ:** 1, 2, 4, 8 o 16 celdas. Un grupo de 2ᵏ elimina k variables.
2. **Rectangular:** sin formas en L ni diagonales.
3. **Lo más grande posible:** grupos grandes dan términos más cortos.
4. **El mapa se enrolla:** bordes opuestos y las 4 esquinas son vecinos (como un toroide).

**Cómo leer un grupo:** se conservan solo las variables que **no cambian**. Constante en 1 → `A`; constante en 0 → `A'`; si cambia → desaparece.

## 5. Implicantes

| Concepto | Significado |
|---|---|
| Implicante | Cualquier grupo válido de 1 (o X) |
| Implicante primo | Un grupo que ya no se puede agrandar |
| Primo esencial | El único primo que cubre algún 1; debe estar en la solución |

**Solución mínima = primos esenciales + los primos mínimos necesarios para cubrir el resto.**

**Procedimiento SOP mínima:** colocar 1, 0 y X → agrupar lo más grande → elegir primos esenciales → cubrir los 1 restantes con pocos grupos → leer un término por grupo.

## 6. Ejemplos resueltos

### Reto de 3 variables: `F(A,B,C) = Σm(0, 2, 4, 5, 6)`

| A \ BC | 00 | 01 | 11 | 10 |
|:-:|:-:|:-:|:-:|:-:|
| **0** | 1 | 0 | 0 | 1 |
| **1** | 1 | 1 | 0 | 1 |

- Grupo de 4 (columnas 00 y 10): `C'`
- Grupo de 2 (m4 y m5): `AB'`

**`F = C' + AB'`**

### Ejemplo guiado de 4 variables: `F = Σm(0,1,2,5,8,9,10)`

| Grupo | Celdas | Término |
|---|---|---|
| Bordes superior e inferior | m0, m1, m8, m9 | `B'C'` |
| Las 4 esquinas | m0, m2, m8, m10 | `B'D'` |
| Dos celdas | m1, m5 | `A'C'D` |

**SOP: `F = B'C' + B'D' + A'C'D`** → 3 términos, 7 literales.

### La misma función en POS (se agrupan los 0)

| Grupo | Término |
|---|---|
| Fila AB = 11 | `(A' + B')` |
| Columna CD = 11 | `(C' + D')` |
| B = 1, D = 0 (bordes izquierdo y derecho) | `(B' + D)` |

**POS: `F = (A'+B')(B'+D)(C'+D')`** → 6 literales. En POS la lectura es invertida: 0 → `A`, 1 → `A'`.

**Conclusión:** se construyen ambas formas y se elige la de menor costo (aquí POS con 6 literales frente a 7 de SOP).

### Ticket de salida: `F = Σm(0, 2, 5, 7, 8, 10, 13, 15)`

- Las 4 esquinas dan `B'D'`.
- El bloque central (m5, m7, m13, m15) da `BD`.

**`F = B'D' + BD`**, que es una **XNOR** de B y D.

## ✍️ Taller de la sesión

| Ejercicio | Función |
|---|---|
| T1 | `F(A,B,C) = Σm(1, 3, 4, 6)` |
| T2 | `F(A,B,C,D) = Σm(0, 1, 4, 5, 6, 7, 14, 15)` (¿algún primo sobra?) |
| T3 | `F(A,B,C,D) = Σm(0, 2, 5, 8, 10, 13)` (atención a las esquinas) |
| T4 | Encontrar los 3 errores de un mapa mal agrupado. El mapa usaba el orden 00, 01, 10, 11 en las columnas (no es Gray) y la expresión no era mínima |

---

# Sesión 2 · Indiferentes y NAND/NOR

## 1. Combinaciones que nunca ocurren

Un dígito BCD usa 4 bits pero solo 10 de las 16 combinaciones. Las combinaciones 1010 a 1111 nunca ocurren, así que el circuito puede hacer con ellas lo que más convenga. Se marcan con **X** (indiferentes).

## 2. Cómo usar las X en el mapa

| Regla | Qué significa |
|---|---|
| **Como 1** | Si agranda un grupo, se incluye: el término queda más corto |
| **Como 0** | Si no ayuda, se ignora. Ninguna X está obligada a quedar cubierta |
| **Nunca sola** | Un grupo formado solo por X agrega un término inútil |

Notación: `F = Σm(minitérminos) + d(indiferentes)`.

## 3. Ejemplo guiado: dígito BCD ≥ 5

`F = Σm(5, 6, 7, 8, 9) + d(10, 11, 12, 13, 14, 15)`

| | Expresión | Literales |
|---|---|:-:|
| Sin usar las X | `AB'C' + A'BD + A'BC` | 9 |
| **Con las X** | **`A + BD + BC`** | **5** |

## 4. Universalidad de NAND y NOR

| Función | Solo NAND | Solo NOR |
|---|---|---|
| NOT | `(A·A)'` | `(A + A)'` |
| AND | `((AB)')'` | `(A' + B')'` |
| OR | `(A'·B')'` | `((A + B)')'` |

- **SOP mínima → NAND-NAND.**
- **POS mínima → NOR-NOR.**
- Si NOT, AND y OR se construyen con NAND, cualquier función también.

## 5. De SOP a NAND-NAND en cuatro pasos

| Paso | Expresión | Justificación |
|:-:|---|---|
| 1 | `F = A + BD + BC` | SOP mínima |
| 2 | `F = ((A + BD + BC)')'` | Doble negación |
| 3 | `F = (A' · (BD)' · (BC)')'` | De Morgan en la negación interna |
| 4 | `F = NAND( A', NAND(B,D), NAND(B,C) )` | Cada paréntesis es una NAND |

El literal que queda solo (A) entra **complementado**: la NAND se usa como inversor. Son 2 niveles, igual retardo que AND-OR.

## 6. Costo en circuitos integrados

| Versión | Chips | CI |
|---|---|:-:|
| Sin usar las X | 7404 + 7411 + 7432 | 3 |
| Con X, AND-OR | 7408 + 7432 | 2 |
| Con X, solo NAND | 7400 + 7410 | 2 |

**El ahorro vino de las X;** la NAND no agrega chips y deja una sola familia en el inventario.


## ✅ Resumen de la semana

- **Gray:** el orden 00-01-11-10 hace visibles las adyacencias.
- **Grupos:** 2ᵏ celdas, rectangulares, lo más grandes posible y con bordes enrollados.
- **SOP y POS:** SOP agrupa los 1; POS agrupa los 0 y se lee invertido.
- **Indiferentes:** valen 1 si agrandan un grupo y 0 si no; nunca se agrupan solas.
- **NAND/NOR:** SOP → NAND-NAND y POS → NOR-NOR.
- **Costo:** se compara en literales y chips; el mínimo algebraico no siempre es el más barato.

