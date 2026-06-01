# Documentación — Parcial 4: Simulación de Probabilidad

> MM3014 Teoría de Probabilidades | Referencia rápida para el examen en clase

---

## Tabla de Contenido

1. [Estructura Base de Cada Problema](#1-estructura-base-de-cada-problema)
2. [Librería `random` de Python](#2-librería-random-de-python)
   - [Funciones Esenciales](#21-funciones-esenciales)
   - [Tips y Advertencias](#22-tips-y-advertencias)
3. [Librería `numpy`](#3-librería-numpy)
   - [Generación Aleatoria con numpy](#31-generación-aleatoria-con-numpy)
   - [Operaciones con Arrays Booleanos](#32-operaciones-con-arrays-booleanos)
   - [Funciones de Agregación](#33-funciones-de-agregación)
4. [Patrones Clave de Probabilidad](#4-patrones-clave-de-probabilidad)
   - [Probabilidad Simple](#41-probabilidad-simple)
   - [Probabilidad Condicional](#42-probabilidad-condicional)
   - [Valor Esperado E\[X\]](#43-valor-esperado-ex)
   - [Independencia de Eventos](#44-independencia-de-eventos)
   - [Muestreo Sin Reemplazo](#45-muestreo-sin-reemplazo)
   - [Probabilidad de Bayes (Teorema de Bayes Simulado)](#46-probabilidad-de-bayes-teorema-de-bayes-simulado)
5. [Resumen de los 4 Problemas Base](#5-resumen-de-los-4-problemas-base)
   - [Problema A — Dados](#51-problema-a--dados)
   - [Problema B — Monedas](#52-problema-b--monedas)
   - [Problema C — Canicas](#53-problema-c--canicas)
   - [Problema D — Cartas](#54-problema-d--cartas)
6. [Recetas Rápidas (Cheatsheet)](#6-recetas-rápidas-cheatsheet)
7. [Preguntas Simulador de Examen](#7-preguntas-simulador-de-examen)
   - [Variantes de Dados](#71-variantes-de-dados)
   - [Variantes de Monedas](#72-variantes-de-monedas)
   - [Variantes de Canicas](#73-variantes-de-canicas)
   - [Variantes de Cartas](#74-variantes-de-cartas)
   - [Preguntas de Concepto](#75-preguntas-de-concepto)
8. [Errores Comunes a Evitar](#8-errores-comunes-a-evitar)

---

## 1. Estructura Base de Cada Problema

Todo problema del parcial sigue este esqueleto:

```python
import random
import numpy as np

# OBLIGATORIO: fijar semilla ANTES de cualquier generación aleatoria
random.seed(2026)       # si usas random de Python
np.random.seed(2026)    # si usas numpy

N = 10000  # siempre 10,000 repeticiones

# --- Simulación ---
# ... lógica del experimento ...

# --- Resultados ---
# Imprimir con 4 decimales
print(f"P(evento) = {probabilidad:.4f}")
```

**Reglas del parcial:**
- Semilla `2026` siempre primera línea antes de cualquier número aleatorio
- `N = 10000` repeticiones
- Output con `:.4f` (4 decimales)

---

## 2. Librería `random` de Python

### 2.1 Funciones Esenciales

#### `random.seed(valor)`
Fija la semilla. Garantiza resultados reproducibles.

```python
random.seed(2026)
```

#### `random.randint(a, b)`
Entero aleatorio en `[a, b]` **ambos inclusive**.

```python
dado = random.randint(1, 6)   # simula un dado: 1, 2, 3, 4, 5 o 6
moneda = random.randint(0, 1)  # 0=cruz, 1=cara
```

> **Ojo:** A diferencia de `range()`, el límite superior `b` SÍ está incluido.

#### `random.choice(secuencia)`
Elige un elemento aleatorio de una lista/secuencia **con reemplazo**.

```python
colores = ['roja', 'roja', 'azul', 'verde']
color = random.choice(colores)
```

#### `random.sample(secuencia, k)`
Elige `k` elementos **sin reemplazo**. Retorna lista de `k` elementos únicos.

```python
baraja = [1]*4 + [0]*48  # 4 ases, 48 no-ases
carta1, carta2 = random.sample(baraja, 2)  # extrae 2 sin repetir
```

> Útil para: canicas sin reemplazo, cartas, urnas.

#### `random.shuffle(lista)`
Mezcla la lista **en su lugar** (modifica la original).

```python
baraja = list(range(52))
random.shuffle(baraja)
primera = baraja[0]
segunda = baraja[1]
```

#### `random.uniform(a, b)`
Float aleatorio en `[a, b]`. Útil para probabilidades continuas.

```python
x = random.uniform(0, 1)  # simula P(evento)
```

### 2.2 Tips y Advertencias

| Situación | Usa |
|-----------|-----|
| Dado / moneda / valor discreto | `random.randint(a, b)` |
| Extraer de bolsa/urna sin reemplazo | `random.sample(lista, k)` |
| Elegir una caja al azar | `random.choice(['caja1', 'caja2'])` |
| Probar si ocurre evento con prob p | `random.random() < p` |
| Mezclar y tomar primeros k | `random.shuffle(lista)` + slice |

---

## 3. Librería `numpy`

### 3.1 Generación Aleatoria con numpy

#### `np.random.seed(valor)`
Equivalente a `random.seed()` para el generador de numpy.

```python
np.random.seed(2026)
```

#### `np.random.randint(low, high, size)`
`high` es **exclusivo** (diferente a `random.randint`).

```python
# Simular 10000 lanzamientos de dado (1-6)
dados = np.random.randint(1, 7, size=10000)  # high=7 para incluir 6

# Simular 10000 lanzamientos de moneda (0 o 1)
monedas = np.random.randint(0, 2, size=10000)  # high=2 para incluir 1

# Simular 3 monedas en 10000 repeticiones → matriz (10000, 3)
lanzamientos = np.random.randint(0, 2, (10000, 3))
```

> **Diferencia crítica:**
> - `random.randint(1, 6)` → incluye 6
> - `np.random.randint(1, 7)` → excluye 7, incluye 6

#### `np.random.choice(arr, size, replace)`
Muestreo con o sin reemplazo desde un array.

```python
caja = np.array(['R','R','R','R','R','A','A','A','V','V'])  # 5R 3A 2V

# Con reemplazo (replace=True por defecto)
muestra = np.random.choice(caja, size=2, replace=True)

# Sin reemplazo
muestra = np.random.choice(caja, size=2, replace=False)
```

#### `np.array(lista)`
Convierte lista de Python a array numpy para operaciones vectorizadas.

```python
lista_dados = []
for _ in range(10000):
    lista_dados.append(random.randint(1, 6))

dado = np.array(lista_dados)  # ahora se puede operar vectorialmente
```

### 3.2 Operaciones con Arrays Booleanos

Son la herramienta más poderosa para calcular probabilidades.

```python
dado = np.array([1, 4, 3, 6, 2, ...])  # 10000 valores

# Crear máscara booleana
es_par = dado % 2 == 0          # array de True/False
es_siete = dado1 + dado2 == 7   # suma elemento a elemento

# Operadores lógicos
A_y_B = condA & condB   # AND: ambos True
A_o_B = condA | condB   # OR: al menos uno True
no_A  = ~condA          # NOT: niega la condición
```

#### `np.mean(condicion_booleana)`
Calcula la proporción de `True` = **estimación de probabilidad**.

```python
prob = np.mean(dado == 6)       # P(dado = 6)
prob = np.mean(dado % 2 == 0)   # P(par)
```

> `np.mean()` sobre array booleano cuenta los `True` y divide entre total. Es la forma más concisa de estimar probabilidades.

#### `np.sum(array, axis)`
Suma elementos. Con `axis=1` suma por filas (útil para matriz de monedas).

```python
lanzamientos = np.random.randint(0, 2, (10000, 3))  # forma (10000, 3)
caras_por_rep = np.sum(lanzamientos, axis=1)         # suma cada fila → (10000,)
```

### 3.3 Funciones de Agregación

| Función | Qué hace | Ejemplo |
|---------|----------|---------|
| `np.mean(arr)` | Promedio / probabilidad | `np.mean(dado == 7)` |
| `np.sum(arr)` | Suma total | `np.sum(caras)` |
| `np.sum(arr, axis=1)` | Suma por filas | `np.sum(monedas, axis=1)` |
| `np.round(val, 4)` | Redondear | `np.round(prob, 4)` |

---

## 4. Patrones Clave de Probabilidad

### 4.1 Probabilidad Simple

```python
# P(evento A)
prob_A = np.mean(condicion_A)

# Ejemplo: P(dado1 + dado2 == 7)
prob = np.mean(dado1 + dado2 == 7)
print(f"P(suma = 7) = {prob:.4f}")
```

### 4.2 Probabilidad Condicional

**Fórmula:** P(A | B) = P(A ∩ B) / P(B)

```python
# Definir eventos
evento_A = (dado1 + dado2 == 7)
evento_B = (dado1 % 2 == 0) | (dado2 % 2 == 0)  # al menos uno par

# Calcular
prob_B = np.mean(evento_B)
prob_interseccion = np.mean(evento_A & evento_B)

prob_A_dado_B = prob_interseccion / prob_B
print(f"P(A | B) = {prob_A_dado_B:.4f}")
```

> **Patrón:** siempre calcula `prob_B` y `prob_interseccion` por separado, luego divide.

### 4.3 Valor Esperado E[X]

```python
# Método numpy (una línea)
caras_por_rep = np.sum(lanzamientos, axis=1)  # X por cada repetición
valor_esperado = np.mean(caras_por_rep)
print(f"E[X] = {valor_esperado:.4f}")

# Método loop (equivalente)
suma_total = 0
for _ in range(N):
    X = moneda1 + moneda2 + moneda3
    suma_total += X
E_X = suma_total / N
print(f"E[X] = {E_X:.4f}")
```

> E[X] = promedio de todos los valores de X observados en las N repeticiones.

### 4.4 Independencia de Eventos

**Criterio:** A y B independientes ⟺ P(A ∩ B) = P(A) · P(B)

```python
prob_A = np.mean(cartas1 == 1)          # P(A): primera carta es as
prob_B = np.mean(cartas2 == 1)          # P(B): segunda carta es as
prob_AB = np.mean((cartas1 == 1) & (cartas2 == 1))  # P(A ∩ B)

producto = prob_A * prob_B

# Comparar redondeando a 4 decimales
son_independientes = round(prob_AB, 4) == round(producto, 4)

print(f"P(A ∩ B) = {prob_AB:.4f}")
print(f"P(A) * P(B) = {producto:.4f}")
print(f"Independientes: {son_independientes}")
```

### 4.5 Muestreo Sin Reemplazo

**Opción A — `random.sample` (con loop):**

```python
random.seed(2026)
baraja = [1]*4 + [0]*48  # 1=as, 0=no-as

lista_c1, lista_c2 = [], []
for _ in range(N):
    c1, c2 = random.sample(baraja, 2)
    lista_c1.append(c1)
    lista_c2.append(c2)

cartas1 = np.array(lista_c1)
cartas2 = np.array(lista_c2)
```

**Opción B — `np.random.choice` (vectorizado, más rápido):**

```python
np.random.seed(2026)
caja = np.array([1]*5 + [2]*3 + [3]*2)  # 1=roja, 2=azul, 3=verde

resultados = []
for _ in range(N):
    muestra = np.random.choice(caja, size=2, replace=False)
    resultados.append(muestra)

resultados = np.array(resultados)  # forma (10000, 2)
c1 = resultados[:, 0]  # primera extracción
c2 = resultados[:, 1]  # segunda extracción
```

**Opción C — lista como urna con `random.sample`:**

```python
caja = ['R']*5 + ['A']*3 + ['V']*2
for _ in range(N):
    extraccion = random.sample(caja, 2)
    c1, c2 = extraccion[0], extraccion[1]
```

### 4.6 Probabilidad de Bayes (Teorema de Bayes Simulado)

**Problema C parte 2:** P(Caja 1 | una roja y una verde)

```python
random.seed(2026)

caja1 = ['R']*5 + ['A']*3 + ['V']*2  # total 10
caja2 = ['R']*2 + ['A']*5 + ['V']*3  # total 10

casos_favorables = 0  # caja1 AND (una roja y una verde)
casos_totales = 0     # todos los (una roja y una verde)

for _ in range(N):
    caja_elegida = random.choice([1, 2])  # elige caja al azar

    if caja_elegida == 1:
        extraccion = random.sample(caja1, 2)
    else:
        extraccion = random.sample(caja2, 2)

    conjunto = set(extraccion)
    if conjunto == {'R', 'V'}:  # condición: una roja y una verde
        casos_totales += 1
        if caja_elegida == 1:
            casos_favorables += 1

prob = casos_favorables / casos_totales
print(f"P(Caja 1 | una roja y una verde) = {prob:.4f}")
```

> **Patrón Bayes:** Contar `casos_favorables` (hipótesis Y evidencia) y `casos_totales` (solo evidencia), luego dividir.

---

## 5. Resumen de los 4 Problemas Base

### 5.1 Problema A — Dados

**Setup:** 2 dados de 6 caras, 10,000 repeticiones.

```python
import random
import numpy as np

random.seed(2026)
N = 10000

lista_d1, lista_d2 = [], []
for _ in range(N):
    lista_d1.append(random.randint(1, 6))
    lista_d2.append(random.randint(1, 6))

dado1 = np.array(lista_d1)
dado2 = np.array(lista_d2)

# Inciso A: P(suma = 7)
p_suma7 = np.mean(dado1 + dado2 == 7)
print(f"P(suma = 7) = {p_suma7:.4f}")   # → 0.1642

# Inciso B: P(suma = 7 | al menos un par)
al_menos_un_par = (dado1 % 2 == 0) | (dado2 % 2 == 0)
p_cond = np.mean((dado1 + dado2 == 7) & al_menos_un_par) / np.mean(al_menos_un_par)
print(f"P(suma = 7 | al menos un par) = {p_cond:.4f}")  # → 0.2174
```

**Salidas esperadas:** `0.1642` | `0.2174`

---

### 5.2 Problema B — Monedas

**Setup:** 3 monedas (0=cruz, 1=cara), 10,000 repeticiones.

```python
import numpy as np

np.random.seed(2026)
N = 10000

# Matriz (N, 3): cada fila = un lanzamiento de 3 monedas
lanzamientos = np.random.randint(0, 2, (N, 3))
caras = np.sum(lanzamientos, axis=1)  # caras por repetición

# Inciso A: P(exactamente 2 caras)
p_2caras = np.mean(caras == 2)
print(f"P(exactamente 2 caras) = {p_2caras:.4f}")  # → 0.3768

# Inciso B: E[X]
E_X = np.mean(caras)
print(f"E[X] = {E_X:.4f}")  # → ~1.5002
```

> El notebook usa `random` de Python para obtener los valores exactos del enunciado (`0.3760`, `1.5002`). Con numpy y semilla `2026` el inciso A da `0.3768`.

**Salidas esperadas:** `0.3760` | `1.5002`

---

### 5.3 Problema C — Canicas

**Setup:** Urna con 5R + 3A + 2V, extracción de 2 sin reemplazo.

```python
import random
import numpy as np

random.seed(2026)
N = 10000

caja = ['R']*5 + ['A']*3 + ['V']*2  # ['R','R','R','R','R','A','A','A','V','V']

# Parte 1: P(ambas rojas)
conteo_rr = 0
for _ in range(N):
    c1, c2 = random.sample(caja, 2)
    if c1 == 'R' and c2 == 'R':
        conteo_rr += 1

print(f"P(ambas rojas) = {conteo_rr/N:.4f}")  # → 0.2249

# Parte 2: Dos cajas + Bayes
random.seed(2026)
caja1 = ['R']*5 + ['A']*3 + ['V']*2
caja2 = ['R']*2 + ['A']*5 + ['V']*3

fav, total = 0, 0
for _ in range(N):
    elegida = random.choice([1, 2])
    muestra = random.sample(caja1 if elegida == 1 else caja2, 2)
    if set(muestra) == {'R', 'V'}:
        total += 1
        if elegida == 1:
            fav += 1

print(f"P(Caja 1 | roja y verde) = {fav/total:.4f}")  # → 0.6115
```

**Salidas esperadas:** `0.2249` | `0.6115`

---

### 5.4 Problema D — Cartas

**Setup:** Baraja 52 cartas (4 ases), extracción de 2 sin reemplazo.

```python
import random
import numpy as np

N = 10000
baraja = [1]*4 + [0]*48  # 1=as, 0=no-as

random.seed(2026)

lista_c1, lista_c2 = [], []
for _ in range(N):
    c1, c2 = random.sample(baraja, 2)
    lista_c1.append(c1)
    lista_c2.append(c2)

cartas1 = np.array(lista_c1)
cartas2 = np.array(lista_c2)

# Inciso A: P(ambas ases)
p_ambas = np.mean((cartas1 == 1) & (cartas2 == 1))
print(f"P(ambas ases) = {p_ambas:.4f}")  # → 0.0036

# Inciso B: Independencia
p_A = np.mean(cartas1 == 1)
p_B = np.mean(cartas2 == 1)
producto = p_A * p_B
son_ind = round(p_ambas, 4) == round(producto, 4)

print(f"P(A) * P(B) = {producto:.4f}")        # → 0.0055
print(f"Independientes: {son_ind}")            # → False
```

**Salidas esperadas:** `0.0036` | `0.0055` | `False`

---

## 6. Recetas Rápidas (Cheatsheet)

### Cambiar número de dados/caras/lados

```python
# Dado de 8 caras en vez de 6
dado = random.randint(1, 8)       # random
dado = np.random.randint(1, 9)    # numpy (high exclusivo)
```

### Cambiar número de monedas

```python
# 4 monedas en vez de 3
lanzamientos = np.random.randint(0, 2, (N, 4))  # cambiar 3 → 4
caras = np.sum(lanzamientos, axis=1)
```

### Cambiar composición de urna

```python
# Nueva urna: 6R, 4A, 2V
caja = ['R']*6 + ['A']*4 + ['V']*2

# Con numpy
caja_np = np.array([1]*6 + [2]*4 + [3]*2)  # 1=R, 2=A, 3=V
```

### Cambiar condición de la suma

```python
# P(suma >= 10) en vez de P(suma == 7)
p = np.mean(dado1 + dado2 >= 10)

# P(suma es par)
p = np.mean((dado1 + dado2) % 2 == 0)
```

### Cambiar "sin reemplazo" → "con reemplazo"

```python
# Sin reemplazo (original)
c1, c2 = random.sample(baraja, 2)

# Con reemplazo
c1 = random.choice(baraja)
c2 = random.choice(baraja)
```

### Muestrear más de 2 elementos

```python
# Extraer 3 cartas sin reemplazo
tres_cartas = random.sample(baraja, 3)
c1, c2, c3 = tres_cartas
```

### Verificar "al menos uno"

```python
# Al menos una roja
al_menos_una_roja = (c1 == 'R') | (c2 == 'R')

# Al menos un as (con numpy arrays)
al_menos_un_as = (cartas1 == 1) | (cartas2 == 1)
```

### Formato de salida

```python
# 4 decimales (requerido)
print(f"P(evento) = {prob:.4f}")

# round() equivalente
print(f"P(evento) = {round(prob, 4)}")
```

---

## 7. Preguntas Simulador de Examen

### 7.1 Variantes de Dados

**Q1.** Modifica el Problema A para estimar P(suma = 9).

<details>
<summary>Respuesta</summary>

```python
p = np.mean(dado1 + dado2 == 9)
print(f"P(suma = 9) = {p:.4f}")
```
Valor exacto: 4/36 ≈ 0.1111
</details>

---

**Q2.** Modifica el Problema A para estimar P(suma > 9).

<details>
<summary>Respuesta</summary>

```python
p = np.mean(dado1 + dado2 > 9)
print(f"P(suma > 9) = {p:.4f}")
```
Valor exacto: 6/36 ≈ 0.1667
</details>

---

**Q3.** Modifica el código para usar dados de 10 caras en vez de 6.

<details>
<summary>Respuesta</summary>

```python
# Cambiar en el loop:
lista_d1.append(random.randint(1, 10))  # dado de 10 caras
lista_d2.append(random.randint(1, 10))
```
</details>

---

**Q4.** Estima P(dado1 = dado2) — que salga doble.

<details>
<summary>Respuesta</summary>

```python
p = np.mean(dado1 == dado2)
print(f"P(dobles) = {p:.4f}")
```
Valor exacto: 6/36 = 1/6 ≈ 0.1667
</details>

---

**Q5.** Estima P(suma = 7 | ambos dados impares).

<details>
<summary>Respuesta</summary>

```python
ambos_impares = (dado1 % 2 != 0) & (dado2 % 2 != 0)
p = np.mean((dado1 + dado2 == 7) & ambos_impares) / np.mean(ambos_impares)
print(f"P(suma=7 | ambos impares) = {p:.4f}")
```
Valor exacto: 2/9 ≈ 0.2222 (pares (1,6),(3,4),(5,2) y reversos con impares)
</details>

---

**Q6.** Simula 3 dados. Estima P(la suma de los tres sea mayor a 15).

<details>
<summary>Respuesta</summary>

```python
random.seed(2026)
N = 10000
d1, d2, d3 = [], [], []
for _ in range(N):
    d1.append(random.randint(1,6))
    d2.append(random.randint(1,6))
    d3.append(random.randint(1,6))
d1, d2, d3 = np.array(d1), np.array(d2), np.array(d3)
p = np.mean(d1 + d2 + d3 > 15)
print(f"P(suma > 15) = {p:.4f}")
```
</details>

---

### 7.2 Variantes de Monedas

**Q7.** Modifica el código para lanzar 5 monedas en vez de 3. Estima P(exactamente 3 caras).

<details>
<summary>Respuesta</summary>

```python
np.random.seed(2026)
N = 10000
lanzamientos = np.random.randint(0, 2, (N, 5))  # 5 monedas
caras = np.sum(lanzamientos, axis=1)
p = np.mean(caras == 3)
print(f"P(exactamente 3 caras) = {p:.4f}")
```
Valor exacto: C(5,3)/2^5 = 10/32 = 0.3125
</details>

---

**Q8.** Con 3 monedas, estima P(al menos 2 caras).

<details>
<summary>Respuesta</summary>

```python
p = np.mean(caras >= 2)
print(f"P(al menos 2 caras) = {p:.4f}")
```
Valor exacto: (3+1)/8 = 4/8 = 0.5
</details>

---

**Q9.** Con 3 monedas, estima E[X²] (valor esperado del cuadrado de caras).

<details>
<summary>Respuesta</summary>

```python
E_X2 = np.mean(caras**2)
print(f"E[X²] = {E_X2:.4f}")
```
</details>

---

**Q10.** La moneda 1 está cargada: P(cara) = 0.6. Modifica el código.

<details>
<summary>Respuesta</summary>

```python
random.seed(2026)
N = 10000
resultados = []
for _ in range(N):
    m1 = 1 if random.random() < 0.6 else 0  # cargada
    m2 = random.randint(0, 1)               # normal
    m3 = random.randint(0, 1)               # normal
    resultados.append(m1 + m2 + m3)
caras = np.array(resultados)
```
</details>

---

### 7.3 Variantes de Canicas

**Q11.** La caja tiene 4R, 4A, 2V. Se extraen 2 sin reemplazo. Estima P(ambas azules).

<details>
<summary>Respuesta</summary>

```python
random.seed(2026)
N = 10000
caja = ['R']*4 + ['A']*4 + ['V']*2
conteo = 0
for _ in range(N):
    c1, c2 = random.sample(caja, 2)
    if c1 == 'A' and c2 == 'A':
        conteo += 1
print(f"P(ambas azules) = {conteo/N:.4f}")
```
Valor exacto: C(4,2)/C(10,2) = 6/45 ≈ 0.1333
</details>

---

**Q12.** Misma urna (5R,3A,2V). Se extraen 2. Estima P(una roja y una azul).

<details>
<summary>Respuesta</summary>

```python
random.seed(2026)
N = 10000
caja = ['R']*5 + ['A']*3 + ['V']*2
conteo = 0
for _ in range(N):
    c1, c2 = random.sample(caja, 2)
    if set([c1, c2]) == {'R', 'A'}:  # sin importar orden
        conteo += 1
print(f"P(una roja y una azul) = {conteo/N:.4f}")
```
Valor exacto: (5×3 + 3×5)/(10×9) = 30/90 = 1/3 ≈ 0.3333
</details>

---

**Q13.** Se extraen 3 canicas sin reemplazo. Estima P(las 3 son de diferente color).

<details>
<summary>Respuesta</summary>

```python
random.seed(2026)
N = 10000
caja = ['R']*5 + ['A']*3 + ['V']*2
conteo = 0
for _ in range(N):
    muestra = random.sample(caja, 3)
    if len(set(muestra)) == 3:  # los 3 colores distintos
        conteo += 1
print(f"P(3 colores distintos) = {conteo/N:.4f}")
```
</details>

---

### 7.4 Variantes de Cartas

**Q14.** Estima P(al menos una de las 2 cartas es un as).

<details>
<summary>Respuesta</summary>

```python
p = np.mean((cartas1 == 1) | (cartas2 == 1))
print(f"P(al menos un as) = {p:.4f}")
```
Valor exacto: 1 - C(48,2)/C(52,2) = 1 - 1128/1326 ≈ 0.1493
</details>

---

**Q15.** Modifica para extraer 3 cartas. Estima P(ninguna es as).

<details>
<summary>Respuesta</summary>

```python
random.seed(2026)
N = 10000
baraja = [1]*4 + [0]*48
conteo = 0
for _ in range(N):
    c1, c2, c3 = random.sample(baraja, 3)
    if c1 == 0 and c2 == 0 and c3 == 0:
        conteo += 1
print(f"P(ningún as en 3 cartas) = {conteo/N:.4f}")
```
Valor exacto: C(48,3)/C(52,3) = 17296/22100 ≈ 0.7826
</details>

---

**Q16.** Determina si son independientes los eventos: A="primera carta es as" y C="la suma de valores (As=1) es mayor que 1".

<details>
<summary>Respuesta</summary>

```python
# Reusar cartas1, cartas2 del problema D
p_A = np.mean(cartas1 == 1)
evento_C = (cartas1 + cartas2 > 1)
p_C = np.mean(evento_C)
p_AC = np.mean((cartas1 == 1) & evento_C)
print(f"P(A)*P(C) = {p_A*p_C:.4f}")
print(f"P(A∩C) = {p_AC:.4f}")
print(f"Independientes: {round(p_A*p_C,4) == round(p_AC,4)}")
```
</details>

---

### 7.5 Preguntas de Concepto

**Q17.** ¿Por qué fijar la semilla con `2026` antes de las simulaciones?

> Para reproducibilidad. Con la misma semilla, la secuencia de números pseudoaleatorios es idéntica cada vez que corre el código, garantizando que todos obtengan los mismos resultados.

---

**Q18.** ¿Cuál es la diferencia entre `random.randint(1,6)` y `np.random.randint(1,7)`?

> Ambos generan enteros de 1 a 6 inclusive. `random.randint(a,b)` incluye `b`; `np.random.randint(a,b)` excluye `b`, por eso se usa `b+1`.

---

**Q19.** ¿Por qué usar `np.mean(condicion)` para estimar probabilidades?

> `np.mean()` sobre un array booleano cuenta los `True` (1) y los divide entre el total. Por la Ley de los Grandes Números, este promedio converge a la probabilidad real cuando N → ∞.

---

**Q20.** Si P(A∩B) ≠ P(A)·P(B), ¿qué significa?

> Los eventos A y B son **dependientes**: saber que B ocurrió cambia la probabilidad de A. En el caso de cartas sin reemplazo, saber que la primera fue un as reduce las cartas restantes, afectando P(segunda carta es as).

---

**Q21.** ¿Cómo cambia la simulación si la extracción es CON reemplazo en vez de SIN reemplazo?

> Con reemplazo: usar `random.choice()` repetidamente — la composición de la urna no cambia entre extracciones. Sin reemplazo: usar `random.sample()` — elimina las cartas/canicas extraídas. Eventos sin reemplazo son dependientes; con reemplazo son independientes.

---

**Q22.** ¿Cómo simular el lanzamiento de una moneda cargada con P(cara) = 0.7?

```python
# Método 1: random.random()
cara = 1 if random.random() < 0.7 else 0

# Método 2: np.random.choice con pesos
cara = np.random.choice([0, 1], p=[0.3, 0.7])
```

---

## 8. Errores Comunes a Evitar

| Error | Problema | Corrección |
|-------|----------|------------|
| `np.random.randint(1, 6)` para dado | Excluye el 6 | Usar `np.random.randint(1, 7)` |
| Poner semilla después de generar números | Semilla no aplica retroactivamente | Semilla siempre **primera línea** |
| `random.sample(lista, 2)` con lista numpy | Puede fallar en algunas versiones | Convertir a list: `random.sample(list(arr), 2)` |
| Dividir enteros `conteo / N` en Python 2 | División entera | En Python 3 ya es float; no hay problema |
| Comparar floats directamente `prob_AB == prob_A*prob_B` | Imprecisión de punto flotante | Usar `round(a, 4) == round(b, 4)` |
| Olvidar `axis=1` en `np.sum` sobre matriz | Suma todos los elementos | `np.sum(matriz, axis=1)` suma por filas |
| `set([c1, c2]) == {'R', 'V'}` cuando pueden ser iguales | Si c1=c2='R', el set tiene un elemento | OK para verificar "uno de cada": set de tamaño 2 con esos elementos |
| Resetear la semilla dentro del loop | Borra la aleatoriedad | Semilla solo **una vez** antes del loop |

---

*Documentación generada para el Parcial 4 — MM3014 Teoría de Probabilidades*
