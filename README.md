# Detección de Fraude en Títulos de Transporte mediante Aprendizaje No Supervisado

Trabajo Fin de Máster (TFM) — Sistema de detección de anomalías sobre registros de validaciones de transporte público, combinando modelos generativos y de aislamiento con un mecanismo de **umbral adaptativo de reconstrucción (ART)**.

---

## 📋 Descripción

Este proyecto aborda la detección de fraude en títulos de transporte público a partir de datos de validaciones. Dado que las etiquetas de fraude son escasas y costosas de obtener, el enfoque es **no supervisado**: los modelos se entrenan únicamente con validaciones normales y detectan anomalías por desviación respecto al comportamiento aprendido.

Se implementan y comparan **cuatro detectores**:

| Modelo | Tipo | Señal de anomalía |
|---|---|---|
| **Autoencoder (AE)** | Generativo | Error de reconstrucción |
| **Restricted Boltzmann Machine (RBM)** | Generativo | Error de reconstrucción |
| **Self-Organizing Map (SOM)** | Clustering / topológico | Distancia a la BMU |
| **Isolation Forest (IF)** | Aislamiento | Anomaly score |

La aportación principal es un **mecanismo de umbral adaptativo (ART)** que sustituye el umbral global fijo por umbrales locales asociados a cada neurona del mapa SOM, ajustados mediante percentiles sobre la distribución de errores de las validaciones normales.

---

## 🎯 Objetivos

- Detectar validaciones fraudulentas sin depender de etiquetas supervisadas.
- Comparar el rendimiento de cuatro técnicas no supervisadas sobre el mismo conjunto de datos.
- Proponer y validar un mecanismo de umbralización contextual (**ART**) que se adapte a la variabilidad estructural de los datos (franjas horarias, líneas, patrones de uso).
- Evaluar los detectores combinando **métricas no supervisadas** con un **conjunto reducido de fraudes etiquetados** para verificación empírica.


---

## ⚙️ Instalación

```bash
# Clonar el repositorio
git clone https://github.com/<usuario>/<repo>.git
cd <repo>

# Crear entorno virtual
python -m venv venv
source venv/bin/activate      # Linux/Mac
venv\Scripts\activate         # Windows

# Instalar dependencias
pip install -r requirements.txt
```
