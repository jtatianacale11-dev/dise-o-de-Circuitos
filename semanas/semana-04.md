# Semana 4 · Circuitos aritméticos

[⬅ Semana 3](semana-03.md) · [Índice](../README.md)

## 🎯 Objetivo

Diseñar con el procedimiento clásico un circuito que suma números binarios de 4 bits, usarlo también para restar y estimar cuánto tarda en dar el resultado. Es el corazón de la **ALU** de un procesador.

## 1. Sumar un solo bit

| Operación | Resultado | S (escribo) | C (me llevo) |
|:-:|:-:|:-:|:-:|
| 0 + 0 | 0 | 0 | 0 |
| 0 + 1 | 1 | 1 | 0 |
| 1 + 0 | 1 | 1 | 0 |
| 1 + 1 | 2 = 10₂ | 0 | 1 |

Igual que en decimal: `7 + 5 = 12` → escribo 2, llevo 1.

**Ejemplo:** `0110 + 0111`

```
acarreo   1 1 0 0
            0 1 1 0   ( 6)
          + 0 1 1 1   ( 7)
          ---------
            1 1 0 1   (13)
```

Un acarreo puede provocar otro: los acarreos se **encadenan**.

## 2. Dos tipos de columna, dos circuitos

| Columna | Suma | Circuito |
|---|---|---|
| Columna 0 | 2 bits (A y B) | **Semisumador** |
| Columnas 1, 2, 3… | 3 bits (A, B y el acarreo Cin) | **Sumador completo** |

## 3. Semisumador

| A | B | C | S |
|:-:|:-:|:-:|:-:|
| 0 | 0 | 0 | 0 |
| 0 | 1 | 0 | 1 |
| 1 | 0 | 0 | 1 |
| 1 | 1 | 1 | 0 |

- `S = A ⊕ B` (XOR: 1 cuando A y B son distintos)
- `C = A · B` (AND: 1 solo cuando ambos son 1)
- «Semi» porque no puede recibir acarreo: solo sirve para la columna 0.

## 4. Sumador completo: el truco de contar unos

Se cuentan los unos de la fila y se escribe el número en binario como `Cout S`.

| A | B | Cin | # de 1 | Cout | S |
|:-:|:-:|:-:|:-:|:-:|:-:|
| 0 | 0 | 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | 1 | 0 | 1 |
| 0 | 1 | 0 | 1 | 0 | 1 |
| 0 | 1 | 1 | 2 | 1 | 0 |
| 1 | 0 | 0 | 1 | 0 | 1 |
| 1 | 0 | 1 | 2 | 1 | 0 |
| 1 | 1 | 0 | 2 | 1 | 0 |
| 1 | 1 | 1 | 3 | 1 | 1 |

**En palabras:**

- **S = 1** cuando hay un número **impar** de unos (1 o 3).
- **Cout = 1** cuando hay **al menos dos** unos (la mayoría).

### Mapas de Karnaugh

**Mapa de S** (A en filas, B·Cin en columnas 00, 01, 11, 10): los unos están alternados como tablero de ajedrez, ninguno tiene vecino y no hay grupos. Eso es una XOR.

| A \ B·Cin | 00 | 01 | 11 | 10 |
|:-:|:-:|:-:|:-:|:-:|
| **0** | 0 | 1 | 0 | 1 |
| **1** | 1 | 0 | 1 | 0 |

**`S = A ⊕ B ⊕ Cin`**

**Mapa de Cout:** tres grupos de dos.

| A \ B·Cin | 00 | 01 | 11 | 10 |
|:-:|:-:|:-:|:-:|:-:|
| **0** | 0 | 0 | 1 | 0 |
| **1** | 0 | 1 | 1 | 1 |

- Columna 11 → `B·Cin`
- Fila A = 1, columnas 11-10 → `A·B`
- Fila A = 1, columnas 01-11 → `A·Cin`

**`Cout = AB + A·Cin + B·Cin`**

> No hay que memorizar las fórmulas. Se hace: tabla de verdad contando unos → mapa de Karnaugh (si sale ajedrez es XOR) → expresión y su lectura en palabras.

## 5. Sumador de 4 bits en cadena (ripple carry)

Cuatro sumadores completos conectados con una regla única: **el Cout de un sumador entra al Cin del siguiente.**

```
  A3 B3     A2 B2     A1 B1     A0 B0
   │  │      │  │      │  │      │  │
 ┌─┴──┴─┐  ┌─┴──┴─┐  ┌─┴──┴─┐  ┌─┴──┴─┐
 │ Sum 3│◄─│ Sum 2│◄─│ Sum 1│◄─│ Sum 0│◄─ Cin
 └──┬───┘  └──┬───┘  └──┬───┘  └──┬───┘
    S3        S2        S1        S0
```

El acarreo avanza como una ola, de derecha a izquierda. Cada sumador espera el acarreo del anterior.

### Estimación del retardo

Cada compuerta tarda ≈ 10 ns y el acarreo cruza ≈ 2 compuertas por etapa, entonces **retardo ≈ 2 · n · 10 ns**.

| Bits | Compuertas | Tiempo aprox. |
|:-:|:-:|:-:|
| 4 | 8 | 80 ns |
| 16 | 32 | 320 ns |
| 32 | 64 | 640 ns |

Más bits implican un sumador más lento.

## 6. Restar es sumar el negativo

`A − B = A + (−B)`, donde `−B` = invertir los bits de B y sumar 1.

```
B   = 0011 (3)          0110 (6)
inv = 1100            + 1101 (−3)
+1  = 1101 (−3)       ───────────
                      1 0011 → 3 ✔   (el 1 que sobra se descarta)
```

### El XOR como inversor controlado

| M | B | B ⊕ M |
|:-:|:-:|:-:|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

- **M = 0:** B pasa igual → **SUMA**.
- **M = 1:** B se invierte → **RESTA**.
- M también se conecta al Cin del sumador 0, que aporta el +1.

Un solo circuito suma o resta según M.

## 7. Overflow (desbordamiento)

Con 4 bits con signo el rango es de −8 a +7.

```
  0101 (+5)
+ 0100 (+4)
─────────
  1001 → −7  ✗
```

- Hay overflow si el acarreo que **entra** a la última etapa es distinto del que **sale**: **`V = C3 ⊕ C4`**.
- Que `Cout = 1` **no** significa error por sí solo.

## 8. Comparador de magnitud

- **¿A = B?** XNOR en cada bit (1 si son iguales) y AND de todas.
- **¿A > B?** Se recorre desde la izquierda: el primer bit en que difieren decide.
- Para 2 bits: A > B si A gana en el bit alto, o empatan ahí y A gana en el bajo. El integrado comercial es el **7485**.

## 9. De aquí sale la ALU

Sumador/restador + comparador + AND/OR/XOR + multiplexor (semana 5) = resultado de la instrucción.

## 🧪 Demostración: `0101 + 0011`

| Etapa | A | B | Cin | # de 1 | S | Cout |
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| 0 | 1 | 1 | 0 | 2 | 0 | 1 |
| 1 | 0 | 1 | 1 | 2 | 0 | 1 |
| 2 | 1 | 0 | 1 | 2 | 0 | 1 |
| 3 | 0 | 0 | 1 | 1 | 1 | 0 |

Resultado `1000 = 8`. Sin signo es correcto; con signo hay overflow (5 + 3 = 8 no cabe en −8…+7).

## ✍️ Taller dirigido

1. Sumar `1011 + 0110` escribiendo el acarreo de cada columna.
2. Restar `0110 − 0011` con el sumador (M = 1).
3. Estimar el retardo de un sumador de 16 bits (≈ 320 ns).
4. Comparar A = 10 y B = 01: ¿A > B? ¿A = B?

## ✅ Resumen de la semana

| Concepto | Idea clave |
|---|---|
| S y C | S se escribe, C «me llevo» |
| Semisumador | S = XOR, C = AND; solo columna 0 |
| Sumador completo | S = impar de unos; Cout = al menos dos |
| Ripple | El acarreo avanza etapa por etapa |
| Resta | M = 1: el XOR invierte B y Cin = 1 |
| Overflow | V = C3 ⊕ C4, no Cout |

**Tarea:** lectura de circuitos aritméticos combinacionales, Cuestionario 04 y diseño previo de la APE 04 (tabla y ecuaciones del sumador completo en papel).

