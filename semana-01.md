# Semana 1 · Sistemas numéricos, códigos y compuertas lógicas

[⬅ Volver al índice](../README.md) · [Semana 2 ➡](semana-02.md)

## 🎯 Objetivo

Convertir números entre decimal, binario, octal y hexadecimal. Representar negativos en complemento a dos. Reconocer los códigos BCD, Gray y ASCII. Leer las 7 compuertas lógicas y ubicarlas en un circuito integrado 74LS.

## 1. Por qué el computador usa solo 0 y 1

- Un transistor funciona como un **interruptor controlado por voltaje**: abierto es 0 (voltaje bajo) y cerrado es 1 (voltaje alto).
- Con solo dos estados es fácil distinguirlos aunque la señal llegue con ruido.
- Un «1» no es un voltaje exacto, es un **rango**. En la familia 74LS:

| Nivel | Voltaje |
|---|---|
| Salida en 1 | ≥ 2,7 V |
| Entrada lee 1 | ≥ 2,0 V |
| Zona indefinida | entre 0,8 V y 2,0 V (no se garantiza nada) |
| Entrada lee 0 | ≤ 0,8 V |
| Salida en 0 | ≤ 0,5 V |

- **Margen de ruido alto:** 2,7 − 2,0 = **0,7 V**.
- **Margen de ruido bajo:** 0,8 − 0,5 = **0,3 V**.
- Una causa típica de lecturas erróneas es una **entrada sin conectar**.

## 2. Sistemas numéricos

En un sistema posicional cada posición vale una potencia de la base. En binario las posiciones valen 128, 64, 32, 16, 8, 4, 2, 1.

- **Bit** = un dígito binario. **Byte** = 8 bits.
- Ejemplo: `1011₂ = 8 + 0 + 2 + 1 = 11`.

### Conversiones

| Conversión | Método | Ejemplo |
|---|---|---|
| Decimal → binario | Dividir entre 2 y leer los restos de abajo hacia arriba | `25 = 11001₂` (16 + 8 + 1) |
| Binario → hexadecimal | Grupos de 4 bits desde la derecha | `157 = 10011101₂ = 9D₁₆` |
| Binario → octal | Grupos de 3 bits desde la derecha | `157 = 235₈` |

Hexadecimal: 10 = A, 11 = B, 12 = C, 13 = D, 14 = E, 15 = F.

## 3. Aritmética binaria

Reglas: `0+0=0`, `0+1=1`, `1+1=10` (escribo 0 y llevo 1), `1+1+1=11`.

```
acarreo   1 1 1
            1 0 1 1   (11)
          + 0 1 1 0   ( 6)
          ---------
          1 0 0 0 1   (17)
```

## 4. Números con signo: complemento a dos

El circuito no tiene signo «−». Se acuerda que `0000 − 1 = 1111` significa −1, y así sucesivamente.

| Binario (4 bits) | 0000 … 0111 | 1000 … 1111 |
|---|---|---|
| Valor | 0 … +7 | −8 … −1 |

- Si el bit izquierdo es 0, el número es positivo. Si es 1, es negativo.
- Rango con 4 bits: de −8 a +7. Con 8 bits: de −128 a +127.
- **Obtener el negativo: invertir los bits y sumar 1.** Ejemplo: 3 = `0011` → `1100` → `1101` = −3.
- **Restar es sumar el negativo**, así que no hace falta un circuito restador. Ejemplo: `5 − 3 = 0101 + 1101 = 1 0010`. Se descarta el bit que sobra y queda `0010 = 2` ✓.
- **Desbordamiento (overflow):** si el resultado no cabe en los bits, dos positivos pueden dar un negativo. Ejemplo: `0111 + 0001 = 1000 = −8` ✗. Lo mismo ocurre con un `int` de Java de 32 bits.

## 5. Códigos

| Código | Idea | Uso |
|---|---|---|
| **BCD** | Cada dígito decimal en 4 bits (157 → `0001 0101 0111`). Las combinaciones 1010 a 1111 no se usan. | Displays, relojes, calculadoras |
| **Gray** | Entre vecinos cambia un solo bit. Se construye con el método del espejo. | Perillas y encoders rotativos; orden de los mapas de Karnaugh |
| **ASCII** | Tabla acordada de caracteres a números, 7 bits = 128 caracteres. Ejemplo: «A» = 65 = `01000001`. | Texto (la ñ y las tildes requieren UTF-8) |

## 6. Compuertas lógicas

| Compuerta | Expresión | Salida en 1 cuando… |
|---|---|---|
| NOT | `Y = A'` | la entrada es 0 |
| AND | `Y = A·B` | **todas** las entradas son 1 |
| OR | `Y = A+B` | **al menos una** entrada es 1 |
| NAND | `Y = (A·B)'` | no todas las entradas son 1 |
| NOR | `Y = (A+B)'` | ninguna entrada es 1 |
| XOR | `Y = A⊕B` | las entradas son **distintas** |
| XNOR | `Y = (A⊕B)'` | las entradas son **iguales** |

- El círculo en la salida significa «negado».
- Ejemplo cotidiano de XOR: la luz de una escalera con dos interruptores.
- Con solo NAND (o solo NOR) se puede construir cualquier circuito.
- Existen dos normas de dibujo, ANSI e IEC. En el curso se usa **ANSI**.

## 7. Circuitos integrados y hoja de datos

Ejemplo: el **74LS08** tiene 4 compuertas AND en un encapsulado **DIP-14**.

- Pin 14 = VCC (+5 V). Pin 7 = GND (tierra).
- Los pines se cuentan en sentido antihorario desde la muesca (pin 1 arriba a la izquierda).
- Lectura del código `SN 74 LS 08 N`:

| SN | 74 | LS | 08 | N |
|---|---|---|---|---|
| Fabricante (Texas Instruments) | Rango comercial (54 = militar) | Familia (LS = TTL, HC = CMOS) | Función (4 AND de 2 entradas) | Encapsulado DIP de plástico |

- Un 74HC08 tiene la misma función y los mismos pines, pero es CMOS.

## ✍️ Ejercicios de la clase

- **Reto de conversión:** 45 a binario y hexadecimal; A7₁₆ a decimal; 11100101₂ a hexadecimal y octal.
- **Del enunciado a la compuerta:** identificar la compuerta de cada frase y construir la tabla de verdad de `Y = A·B + C'`.

## ✅ Resumen de la semana

- 0 y 1 son rangos de voltaje con una zona indefinida entre ellos.
- Binario = potencias de 2; hexadecimal = 4 bits; octal = 3 bits.
- Negativo = invertir + 1; restar = sumar el negativo.
- BCD para displays, Gray para sensores, ASCII para texto.
- 7 compuertas: NOT, AND, OR, NAND, NOR, XOR, XNOR.

**Tarea:** lectura de sistemas numéricos y códigos, Cuestionario 01, instalar Logisim-evolution y práctica APE 01.
