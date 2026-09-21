# Detección de Fraude en Títulos de Transporte mediante Aprendizaje No Supervisado

Trabajo Fin de Máster (TFM) — Sistema de detección de anomalías sobre registros de validaciones de transporte público, combinando modelos generativos y de aislamiento con un mecanismo de **umbral adaptativo de reconstrucción (ART)**.

---

## Descripción

Este proyecto aborda la detección de fraude en títulos de transporte público a partir de datos de validaciones. Dado que las etiquetas de fraude son escasas y costosas de obtener, el enfoque es **no supervisado**: los modelos se entrenan únicamente con validaciones normales y detectan anomalías por desviación respecto al comportamiento aprendido.

Se implementan y comparan **cuatro detectores**:

| Modelo | Tipo | Señal de anomalía |
|---|---|---|
| **Autoencoder (AE)** | Generativo | Error de reconstrucción |
| **Restricted Boltzmann Machine (RBM)** | Generativo | Error de reconstrucción |
| **Self-Organizing Map (SOM)** | Clustering / topológico | Distancia a la BMU |
| **Isolation Forest (IF)** | Aislamiento | Anomaly score |

La aportación principal es un **mecanismo de umbral adaptativo (ART)** que sustituye el umbral global fijo por umbrales locales asociados a cada neurona del mapa SOM, calibrados mediante **estimadores robustos** (mediana y desviación absoluta mediana, MAD) sobre la distribución de errores de reconstrucción de las validaciones normales.

---

## Objetivos

- Detectar validaciones fraudulentas sin depender de etiquetas supervisadas.
- Comparar el rendimiento de cuatro técnicas no supervisadas sobre el mismo conjunto de datos.
- Proponer y validar un mecanismo de umbralización contextual (**ART**) que se adapte a la variabilidad estructural de los datos (franjas horarias, líneas, patrones de uso).
- Evaluar los detectores combinando **métricas no supervisadas** con un **conjunto reducido de fraudes etiquetados** para verificación empírica.

---

## Mecanismo ART (Adaptive Reconstruction Threshold)

El umbral global fijo asume implícitamente que la noción de "normalidad" es homogénea en todo el espacio de validaciones. Sin embargo, la distribución de errores de reconstrucción varía significativamente entre regiones del mapa SOM: franjas horarias, líneas y patrones de uso heterogéneos generan colas de error distintas.

ART resuelve esta limitación sustituyendo el umbral único por **umbrales locales asociados a cada neurona del SOM**:

$$
\theta_{(g_x, g_y)} = \mathrm{mediana}\bigl(E_{(g_x, g_y)}\bigr) + k \cdot 1{,}4826 \cdot \mathrm{MAD}\bigl(E_{(g_x, g_y)}\bigr)
$$

donde:

- $E_{(g_x, g_y)}$ es el conjunto de errores de reconstrucción de las validaciones de entrenamiento asignadas a la neurona $(g_x, g_y)$.
- $k$ es el factor de estrictez del detector (calibrado por **criterio de prevalencia**: $k = 7{,}5$).
- La constante $1{,}4826$ escala el MAD para que sea consistente con la desviación típica bajo el supuesto de normalidad.

**Propiedades clave:**

- **Robusto a valores atípicos**: mediana y MAD tienen un punto de ruptura del 50%, mientras que media y desviación típica tienen punto de ruptura nulo.
- **Calibración independiente del ground truth**: $k$ se elige para que la tasa global de anomalías coincida con la prevalencia estimada (~0,01%), evitando sobreajuste sobre los 7 fraudes etiquetados.
- **Interpretabilidad estructural**: la relación inversa entre densidad de validaciones por neurona y umbral local (Spearman $\rho \approx -0{,}36$, $p < 10^{-6}$) confirma empíricamente la heterogeneidad del espacio de validaciones.

**Regla de decisión:**

$$
\hat{y}_i = 1 \iff e_i > \theta_{b(\mathbf{x}_i)}
$$

con umbral global de *fallback* ($P_{99.99}$) para neuronas con menos de 20 validaciones de entrenamiento.

---

## ⚙️ Instalación

```bash
# Clonar el repositorio
git clone https://github.com/pabloosmo/tfm.git
cd tfm

# Crear entorno virtual
python -m venv venv
source venv/bin/activate        # Linux/Mac
venv\Scripts\activate           # Windows

# Instalar dependencias
pip install -r requirements.txt
