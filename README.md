# Parcial 4 — MM3014 Teoría de Probabilidades

Simulaciones de Monte Carlo para cuatro problemas de probabilidad.

## Integrantes

| Nombre | Carné |
|---|---|
| Diego Andre Calderon | 241263 |
| Pedro Julio Caso | 241286 |

## Estructura del Proyecto

```
Parcial4prob1/
├── requirements.txt
├── problema_A_dados.ipynb
├── problema_B_monedas.ipynb
├── problema_C_canicas.ipynb
└── problema_D_cartas.ipynb
```

| Notebook | Problema |
|---|---|
| `problema_A_dados.ipynb` | Lanzamiento de dos dados de 6 caras |
| `problema_B_monedas.ipynb` | Lanzamiento de tres monedas justas |
| `problema_C_canicas.ipynb` | Extracción de canicas sin reemplazo (una y dos cajas) |
| `problema_D_cartas.ipynb` | Extracción de cartas de baraja estándar |

## Requisitos Generales

- Semilla aleatoria: **2026**
- Repeticiones por experimento: **10,000**
- Formato de salida: número decimal con **4 decimales**

---

## Instrucciones para Levantar el Entorno

### Linux

```bash
# 1. Clonar el repositorio
git clone <url-del-repo>
cd Parcial4prob1

# 2. Crear entorno virtual
python3 -m venv .venv

# 3. Activar entorno virtual
source .venv/bin/activate

# 4. Instalar dependencias
pip install -r requirements.txt

# 5. Lanzar JupyterLab
jupyter lab
```

### macOS

```bash
# 1. Clonar el repositorio
git clone <url-del-repo>
cd Parcial4prob1

# 2. Crear entorno virtual
python3 -m venv .venv

# 3. Activar entorno virtual
source .venv/bin/activate

# 4. Instalar dependencias
pip install -r requirements.txt

# 5. Lanzar JupyterLab
jupyter lab
```

> **Nota macOS:** Si Python 3 no está instalado, instálalo con `brew install python` (requiere [Homebrew](https://brew.sh)).

### Windows

```powershell
# 1. Clonar el repositorio
git clone <url-del-repo>
cd Parcial4prob1

# 2. Crear entorno virtual
python -m venv .venv

# 3. Activar entorno virtual (PowerShell)
.venv\Scripts\Activate.ps1

# Si aparece error de permisos en PowerShell, ejecutar antes:
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

# 3b. Activar entorno virtual (CMD)
.venv\Scripts\activate.bat

# 4. Instalar dependencias
pip install -r requirements.txt

# 5. Lanzar JupyterLab
jupyter lab
```

> **Nota Windows:** Si Python no está instalado, descargarlo desde [python.org](https://www.python.org/downloads/). Marcar la opción **"Add Python to PATH"** durante la instalación.

---

## Uso

Una vez abierto JupyterLab en el navegador, abrir el notebook del problema deseado y completar la sección **Solución**. Cada notebook ya contiene la descripción del problema, los requisitos de implementación y las salidas esperadas.

Para desactivar el entorno virtual al terminar:

```bash
deactivate
```
