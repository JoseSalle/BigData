# Comparación de modelos de regresión

Práctica para México: regresión lineal, polinomial de grado 2 y Random Forest
(100 árboles, semilla 42), aplicadas a nacimientos y defunciones.
Se conservan las 104 celdas originales y se completan las respuestas.
El enunciado reserva las métricas y la evaluación formal para la siguiente sesión.
Las proyecciones OWID no se consideran datos de prueba observados.

## Entorno

Probado con Python 3.14.7 en macOS. Desde `UI_3_modelos_ml`:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

En un editor compatible con Jupyter, abrir
`notebooks/U3_1_modelos_regresion_demografia.ipynb`, seleccionar `.venv/bin/python`
y ejecutar todas las celdas desde un kernel nuevo.
No se necesita instalar el servidor completo de Jupyter para ejecutarlo con nbclient.

Ejecución automatizada desde `UI_3_modelos_ml`, con el entorno activado:

```python
import sys
from pathlib import Path
import nbformat
from nbclient import NotebookClient
from jupyter_client import KernelManager

archivo = Path("notebooks/U3_1_modelos_regresion_demografia.ipynb")
notebook = nbformat.read(archivo, as_version=4)
km = KernelManager(kernel_name="python3")
km.kernel_spec.argv = [sys.executable, "-m", "ipykernel_launcher", "-f", "{connection_file}"]
NotebookClient(notebook, km=km, timeout=180,
               resources={"metadata": {"path": str(archivo.parent.resolve())}}).execute()
nbformat.write(notebook, archivo)
```

## Datos y decisiones

El CSV completo original de OWID se conserva en `data/` para reproducibilidad
sin conexión: 39,562 filas y siete columnas; México tiene 151 años.
Fuente: https://ourworldindata.org/grapher/births-and-deaths-projected-to-2100
URL de descarga: https://ourworldindata.org/grapher/births-and-deaths-projected-to-2100.csv?v=1&csvType=full&useColumnShortNames=true
Consulta: 21 de septiembre de 2026. Fuente demográfica indicada por la práctica: ONU, escenario medio.
SHA-256 del CSV: `7b11552ad61e0acea08ef25f53a973944e5283826731864125a20f609d9087c8`.

Se verifican años únicos, valores completos y no negativos, y el corte temporal
1950–2023 / 2024–2100 para ambas variables. Los NaN de columnas separadas
históricas/proyectadas se combinan sin imputar ceros. No hay que codificar
categorías porque el único predictor es el año. Se conservan extremos históricos;
no hay evidencia de errores que justifique eliminarlos.

Las rutas relativas aceptan ejecución desde la raíz del repositorio,
`UI_3_modelos_ml` o `notebooks`. Si falta el CSV, se intenta la fuente original;
un error de descarga detiene la ejecución, sin inventar datos.

Los conteos negativos de la regresión polinomial se muestran deliberadamente
como limitación de extrapolación. Las conclusiones son visuales y no constituyen
una evaluación con métricas ni una validación de pronósticos hasta 2100.

`.venv/`, cachés y archivos de Finder se excluyen. No se requieren credenciales.
