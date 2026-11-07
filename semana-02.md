# Semana 2 · Álgebra de Boole y formas canónicas

[⬅ Semana 1](semana-01.md) · [Índice](../README.md) · [Semana 3 ➡](semana-03.md)

## 🎯 Objetivo

Pasar de un enunciado a la **tabla de verdad**, a la **forma canónica** y a un **circuito simplificado**.

## 1. Un circuito y una expresión son lo mismo

| Circuito | Expresión |
|---|---|
| Cable de entrada | Variable (A, B, C) |
| AND | Producto `·` |
| OR | Suma `+` |
| NOT | Complemento `'` |
| Salida | Valor de la expresión |

Precedencia: **NOT → AND → OR**. Ejemplo: `Y = A·B + C'`.

## 2. Del enunciado a la especificación

«La sirena suena cuando se activan dos sensores» admite dos lecturas:

| Lectura | Filas | Función |
|---|---|---|
| Exactamente dos | 011, 101, 110 | `F = Σm(3, 5, 6)` |
| **Al menos dos** (la que pide el cliente) | 011, 101, 110, 111 | `F = Σm(3, 5, 6, 7)` |

Moraleja: hay que interpretar bien el enunciado antes de diseñar.

## 3. Postulados de Huntington

Conjunto {0, 1} con tres operaciones: `+` (OR), `·` (AND) y `'` (NOT).

| Postulado | Forma OR | Forma AND (dual) |
|---|---|---|
| Identidad | X + 0 = X | X · 1 = X |
| Complemento | X + X' = 1 | X · X' = 0 |
| Conmutativa | X + Y = Y + X | XY = YX |
| Distributiva | X(Y + Z) = XY + XZ | **X + YZ = (X + Y)(X + Z)** |

**Dualidad:** si se intercambia `+` ↔ `·` y `0` ↔ `1` (sin complementar variables), la identidad sigue siendo válida.

## 4. Teoremas (tabla de consulta)

| Teorema | Forma OR | Forma AND |
|---|---|---|
| Elemento nulo | X + 1 = 1 | X · 0 = 0 |
| Idempotencia | X + X = X | X · X = X |
| Involución | (X')' = X | |
| Absorción | X + XY = X | X(X + Y) = X |
| Absorción del complemento | X + X'Y = X + Y | X(X' + Y) = XY |
| Adyacencia | XY + XY' = X | (X + Y)(X + Y') = X |
| Consenso | XY + X'Z + YZ = XY + X'Z | (X+Y)(X'+Z)(Y+Z) = (X+Y)(X'+Z) |

Los teoremas no se memorizan, se **consultan y se aplican**.

### La distributiva que sorprende: `A + BC = (A + B)(A + C)`

```
(A + B)(A + C)
= AA + AC + AB + BC        distributiva
= A + AC + AB + BC         idempotencia (AA = A)
= A(1 + C + B) + BC        factor común A
= A + BC                   elemento nulo (1 + … = 1)
```

## 5. Leyes de De Morgan

> **Rompe la barra, cambia el operador.**

- `(AB)' = A' + B'`
- `(A + B)' = A'B'`
- Se generaliza a n variables: `(A + B + C)' = A'B'C'`.

## 6. Minitérminos y maxitérminos

| i | A B C | Minitérmino mᵢ (vale 1) | Maxitérmino Mᵢ (vale 0) |
|:-:|:-:|---|---|
| 0 | 000 | A'B'C' | A + B + C |
| 1 | 001 | A'B'C | A + B + C' |
| 2 | 010 | A'BC' | A + B' + C |
| 3 | 011 | A'BC | A + B' + C' |
| 4 | 100 | AB'C' | A' + B + C |
| 5 | 101 | AB'C | A' + B + C' |
| 6 | 110 | ABC' | A' + B' + C |
| 7 | 111 | ABC | A' + B' + C' |

- **Minitérmino:** producto. Variable en 1 → sin complemento; en 0 → complementada.
- **Maxitérmino:** suma. Variable en 0 → sin complemento; en 1 → complementada.
- Se cumple `Mᵢ = (mᵢ)'`.

## 7. SOP y POS: una función, dos descripciones

| | SOP (suma de productos) | POS (producto de sumas) |
|---|---|---|
| Se leen las filas donde | F = 1 | F = 0 |
| Se unen con | OR (+) | AND (·) |
| Alarma | `F = Σm(3,5,6,7) = A'BC + AB'C + ABC' + ABC` | `F = ΠM(0,1,2,4) = (A+B+C)(A+B+C')(A+B'+C)(A'+B+C)` |

Los índices de ΠM son los que faltan en Σm.

**¿Cuál elegir?**

| Situación | Conviene |
|---|---|
| Pocos 1 en la tabla | SOP |
| Pocos 0 en la tabla | POS |
| Solo hay NAND | SOP → NAND-NAND (7400) |
| Solo hay NOR | POS → NOR-NOR (7402) |

El criterio final es el **costo**: compuertas, circuitos integrados y niveles lógicos.

## 8. Resolución guiada: alarma de 3 sensores

**Enunciado:** una bodega tiene puerta (A), ventana (B) y movimiento (C). La sirena F suena si **al menos dos** sensores se activan.

| i | A | B | C | F |
|:-:|:-:|:-:|:-:|:-:|
| 0 | 0 | 0 | 0 | 0 |
| 1 | 0 | 0 | 1 | 0 |
| 2 | 0 | 1 | 0 | 0 |
| 3 | 0 | 1 | 1 | **1** |
| 4 | 1 | 0 | 0 | 0 |
| 5 | 1 | 0 | 1 | **1** |
| 6 | 1 | 1 | 0 | **1** |
| 7 | 1 | 1 | 1 | **1** |

**Simplificación** (ABC se usa tres veces por idempotencia):

```
A'BC + ABC = BC(A' + A) = BC
AB'C + ABC = AC(B' + B) = AC
ABC' + ABC = AB(C' + C) = AB

F = AB + AC + BC          (POS: (A+B)(A+C)(B+C))
```

| Versión | Circuitos integrados | Cantidad |
|---|---|---|
| Canónica | 7404 + 2×7411 + 7432 | **4 CI** |
| Simplificada | 7408 + 7432 | **2 CI** |

## 9. Simplificación algebraica

Procedimiento (no garantiza el mínimo):

1. Pares adyacentes: `XY + XY' = X`
2. Duplicar si hace falta: `X = X + X`
3. Absorción: `X + XY = X`
4. Absorción del complemento: `X + X'Y = X + Y`
5. Consenso: `XY + X'Z + YZ = XY + X'Z`

**Ejemplo:** `F = A + A'B + AB'C`

```
F = A + A'B + AB'C
  = (A + B) + AB'C     absorción del complemento: A + A'B = A + B
  = A + AB'C + B       conmutativa
  = A + B              absorción: A + A(B'C) = A
```

De 3 términos (5 literales) a una OR de 2 entradas.

## ✍️ Taller por grupos

| Tema | Descripción |
|---|---|
| Luz de pasillo | Dos interruptores A y B conmutan la luz; el maestro M (= 1) la apaga siempre |
| Detector de primos | Entrada de 3 bits ABC (0–7); F = 1 si el número es primo |
| Bomba de agua | Enciende si hay agua en la cisterna (B) y el tanque está bajo (A) o se pide en manual (C) |
| Aviso de cinturón | Suena si el motor está encendido (E) y el cinturón no está puesto (C) o la puerta está abierta (P) |

Cada grupo entrega: tabla, Σm y ΠM, SOP canónica, versión simplificada y recuento de compuertas y CI.

## ✅ Resumen de la semana

- **Circuito = expresión:** lo que se simplifica en papel se ahorra en hardware.
- **La tabla manda:** dos expresiones son equivalentes si y solo si tienen la misma tabla de verdad.
- **Canónica:** siempre existe (Σm y ΠM) y es única.
- **Simplificar = elegir por costo:** el álgebra no garantiza el mínimo, por eso llegan los mapas de Karnaugh.
