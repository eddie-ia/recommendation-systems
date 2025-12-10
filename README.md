# 🧠 Proyecto POC — Segmentación de Clientes con ML No Supervisado (K-Means)

Este proyecto corresponde a un **POC técnico** orientado a identificar patrones de consumo en clientes del canal digital del organization, usando técnicas de **aprendizaje no supervisado**, principalmente **K-Means**, con el objetivo de crear segmentos de clientes relevantes para **personalización de promociones** y **estrategias de marketing automatizadas**.

---

## 📁 Estructura del Proyecto

- `/data/`: Datasets de entrada (consumos con tarjeta, ubicación, productos, canal digital).
- `/outputs/`: Resultados del modelo (CSV con segmentos por cliente).
- `poc.ipynb`: Notebook principal con:
  - Exploración de datos
  - Feature engineering
  - Entrenamiento y evaluación de K-Means
  - Exportación de resultados para backend

---

## 🔍 Objetivo Técnico

- Aplicar **K-Means** para descubrir grupos de clientes según comportamiento transaccional.
- Generar un archivo `clientes_segmentados.csv` con:
  - `dni`
  - `cluster_id`
  - `cluster_nombre` (ej. "Farmacia", "Supermercado")
  - Scores por rubro
  - Fecha del modelo
- Dejar los resultados listos para **consumo por backend / app móvil**.

---

## ⚙️ Proceso Técnico

1. **Preprocesamiento**
   - Agrupación por `dni` (cliente).
   - Transformación de variables categóricas (`CanalTransaccion`, `Rubro`, etc.).
   - Generación de features como:
     - Frecuencia de consumo por rubro
     - Monto promedio por rubro
     - Recencia (última fecha de consumo)

2. **Modelo de Clustering**
   - Algoritmo: `KMeans` de `sklearn`
   - Selección de número de clusters usando método del codo (elbow method)
   - Entrenamiento del modelo sobre variables numéricas normalizadas (`StandardScaler`)

3. **Asignación y exportación**
   - Cada cliente recibe un `cluster_id`
   - Se genera un archivo CSV final con la siguiente estructura:

```csv
dni,cluster_id,cluster_nombre,rubro_top,score_supermercado,score_farmacia,score_restaurante,fecha_modelo
70457247,1,Farmacia,Farmacia,0.22,0.85,0.45,2024-08-07
...
```

4. **Carga del archivo**
   - Exportado en `/outputs/clientes_segmentados.csv`
   - Listo para carga en:
     - S3 / Blob Storage
     - Base de datos relacional
     - API backend para consumo por la app móvil

---

## 📊 Principales Hallazgos

- Se identificaron 3 clusters diferenciados:
  - 🟩 **Cluster 0:** clientes con mayor consumo en supermercados
  - 🟦 **Cluster 1:** comportamiento dominante en farmacias
  - 🟥 **Cluster 2:** clientes con gasto en restaurantes y delivery
- Estos segmentos permiten definir **campañas específicas por grupo**, optimizando el targeting.

---

## 💡 Casos de Uso Técnicos

| Use Case ML | Implementación | Resultado Esperado |
|-------------|----------------|--------------------|
| Segmentación por consumo | K-Means sobre features de rubro y monto | Archivo con cluster_id por cliente |
| Scoring por rubro | `groupby` + normalización de montos | Score por rubro como variable continua |
| Reentrenamiento automático | Parametrizable por Airflow / cron job | Actualización semanal del segmento |

---

## 📤 Output Listo para Backend

Formato entregado para integración con sistemas del organization:

- Archivo `.csv` o `.parquet`
- Estructura legible por APIs o backend SQL
- Clave primaria: `dni`
- Particionado por fecha del modelo (`fecha_modelo`)

---

## 🧩 Recomendaciones de Escalabilidad

- Implementar **Soft Clustering (GMM)** si se requiere pertenencia parcial a múltiples rubros.
- Agregar feedback posterior de promociones como `label` para futuros modelos **supervisados**.
- Automatizar con pipelines en Airflow, Step Functions o similar.

---
```json
{
  "dni": "70457247",
  "cluster_id": 1,
  "cluster_nombre": "Farmacia",
  "rubro_top": "xyz",
  "score_supermercado": 0.22,
  "score_farmacia": 0.85,
  "score_restaurante": 0.45,
  "fecha_modelo": "2024-08-07"
}
```
---

## 🛠️ Tecnologías Usadas

- Python 3.11
- Pandas, NumPy, scikit-learn
- Matplotlib, Seaborn
- Jupyter Notebook
- (Opcional) Airflow / S3 / Parquet / APIs
