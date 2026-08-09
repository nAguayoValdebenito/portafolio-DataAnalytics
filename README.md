# Predicción Climática y Segmentación — Agroinnova

Sistema de análisis de datos climáticos para apoyar decisiones agrícolas: riego preventivo, protección de cultivos y prevención de heladas.

**Hecho por:** Nicolás Aguayo

**Versión:** 2.0 (Integración Entregas 2 y 3)

---

## Índice

- [Objetivo del proyecto](#objetivo-del-proyecto)
- [Sección A — Predicción de Temperatura](#sección-a--predicción-de-temperatura)
- [Sección B — Predicción de Lluvia](#sección-b--predicción-de-lluvia)
- [Sección C — Segmentación Climática](#sección-c--segmentación-climática)
- [Archivos generados](#archivos-generados)

---

## Objetivo del proyecto

Los datos provienen de mediciones meteorológicas horarias de distintas localidades de Chile. Con ellos se persiguen tres metas:

| Meta | Tipo de modelo | Sección |
|---|---|---|
| Predecir la temperatura futura | Regresión | A |
| Predecir si lloverá en las próximas horas | Clasificación | B |
| Descubrir perfiles climáticos ocultos | Clustering (no supervisado) | C |

---

## Sección A — Predicción de Temperatura

### Limpieza de datos

Se revisó el dataset en busca de duplicados, valores nulos e inconsistencias lógicas (humedad fuera de 0–100%, nubosidad negativa, etc.).

**Resultado:** el dataset venía limpio, sin problemas detectados.

### Valores atípicos

Se detectaron datos fuera de rango en precipitación y viento, pero se decidió conservarlos porque representan eventos climáticos reales (temporales, ráfagas fuertes) y son útiles para que el modelo aprenda de condiciones extremas.

### Ingeniería de características

Pieza clave del proyecto. Se construyó una clase de transformación personalizada que:

- Convierte hora y mes en variables cíclicas (seno/coseno), para que el modelo entienda que la hora 23 está "cerca" de la hora 0.
- Genera variables de historial (lags): mediciones de 1, 2, 3, 12 y 24 horas atrás, más promedios móviles de 6 horas, para temperatura, humedad, sensación térmica, nubosidad y horas de sol.
- Calcula deltas térmicos: qué tan rápido sube o baja la temperatura.
- Elimina todas las variables del momento presente antes de entrenar.

> **Por qué importa esto:** evita la fuga de datos (*data leakage*). El modelo solo puede usar información pasada — nunca datos del mismo instante que quiere predecir — para que funcione de forma realista en un escenario real.

### Preprocesamiento

- Variables numéricas → estandarizadas (misma escala).
- Variables categóricas (localidad) → codificadas con one-hot encoding.
- Todo integrado en un pipeline automatizado de Scikit-Learn.

### Modelos entrenados

| Modelo | R² (test) | MAE (test) |
|---|---|---|
| Regresión Lineal | 0.989 | ~0.4 °C |
| Árbol de Decisión (profundidad máx. 25) | 0.982 | ~0.4 °C |

Se comparó el rendimiento entrenamiento vs. prueba para descartar sobreajuste.

### Casos de uso propuestos

- Riego preventivo dinámico → Regresión Lineal
- Alertas tempranas de heladas → Árbol de Decisión
- Comparación de microclimas entre localidades

---

## Sección B — Predicción de Lluvia

### Variable objetivo

Se construyó una variable binaria `is_rain_hour` (llueve / no llueve). Al igual que en la Sección A, solo se usan datos de horas anteriores (lag de 1 hora) y variables cíclicas de tiempo — se elimina el presente para evitar fuga de datos.

### Verificación de supuestos (Naive Bayes)

Antes de aplicar Naive Bayes se comprobó si se cumplían sus supuestos estadísticos (normalidad e independencia de variables).

**Resultado:** no se cumplen — las variables cíclicas y los datos meteorológicos de distintas localidades no siguen una distribución normal. Aun así, se entrenó el modelo, sabiendo que sus probabilidades de salida serían menos confiables que las de los otros algoritmos.

### Modelos entrenados

Pipeline + GridSearchCV para búsqueda de mejores hiperparámetros:

- Regresión Logística
- Árbol de Decisión
- Naive Bayes Gaussiano

Se validó que cumplieran métricas mínimas (F1 ≥ 0.48, ROC-AUC ≥ 0.79) sin sobreajuste, y se seleccionó automáticamente el mejor modelo.

### Ajuste del umbral de decisión

| Umbral | Efecto |
|---|---|
| 0.3 | Elegido — prioriza detectar más lluvias reales (recall) |
| 0.5 | Balance estándar |
| 0.7 | Más estricto, menos alertas falsas pero más lluvias no detectadas |

**Justificación de negocio:** no avisar una lluvia real (falso negativo) es mucho más costoso que avisar una lluvia que no ocurre (falso positivo) — puede significar pérdida de cultivos. Por eso se prioriza el recall por sobre la precisión exacta.

### Salida

Archivos CSV con las predicciones y probabilidades de los tres modelos.

---

## Sección C — Segmentación Climática

### Objetivo

A diferencia de las secciones anteriores, aquí se buscan grupos o "perfiles climáticos" ocultos en los datos históricos, sin necesidad de una variable objetivo (aprendizaje no supervisado).

### Variables usadas

Temperatura, humedad, precipitación, viento, nubosidad y horas de sol — elegidas por su relevancia agronómica directa (estrés térmico, riesgo de hongos, fotosíntesis, daño mecánico a plantas).

### Método

1. Estandarización de los datos (obligatoria para K-Means, que se basa en distancias).
2. Número óptimo de clusters determinado automáticamente con el método del codo (librería Kneed).
3. Entrenamiento del modelo K-Means.
4. PCA para reducir las 6 variables a 2 dimensiones y visualizar los clusters en un gráfico 2D.

### Perfiles climáticos identificados

| Cluster | Perfil | Impacto agrícola |
|---|---|---|
| 0 | Invernal, viento fuerte, cielo cubierto | Riesgo de daño mecánico en plantas |
| 1 | Frío, nublado, calmo | Típico de madrugadas/mañanas heladas |
| 2 | Veraniego, despejado, soleado | Óptimo para labores agrícolas |
| 3 | Tormenta activa | Alerta máxima, suspender labores |
| 4 | Frío con niebla/llovizna | Suelo y hojas no logran secarse |

### Recomendaciones de negocio

- Riego dinámico en periodos de alta radiación / baja humedad.
- Protocolo preventivo de fungicidas en perfiles fríos y húmedos prolongados.
- Programar labores físicas intensivas en los periodos templados y despejados.

---

## Archivos generados

| Archivo | Descripción |
|---|---|
| `data_modelo_G4.csv` | Dataset preparado para el modelo de temperatura |
| `predicciones_threshold_0_3.csv` | Predicciones de lluvia — umbral 0.3 |
| `predicciones_threshold_0_7.csv` | Predicciones de lluvia — umbral 0.7 |
| `predicciones_G4.csv` | Predicciones finales de los 3 modelos de clasificación |
| `estructura_arbol_agroinnova.png` | Visualización del árbol de decisión |
