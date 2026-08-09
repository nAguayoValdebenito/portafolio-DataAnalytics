Objetivo del proyecto

El notebook desarrolla un sistema de análisis de datos climáticos para la empresa ficticia Agroinnova, con tres metas: predecir la temperatura futura, predecir si lloverá en las próximas horas, y descubrir perfiles climáticos ocultos en los datos. Todo esto pensado para apoyar decisiones agrícolas como riego, protección de cultivos y prevención de heladas.

Los datos vienen de un CSV con mediciones meteorológicas horarias de distintas localidades de Chile.

Sección A – Preparación de datos y predicción de temperatura

Limpieza de datos: se revisó el dataset en busca de filas duplicadas, valores nulos e inconsistencias lógicas (humedad fuera de 0-100%, nubosidad negativa, etc.). No se encontró ningún problema, por lo que los datos ya venían limpios.

Valores atípicos: se detectaron datos fuera de rango en precipitación y viento, pero se decidió conservarlos porque representan eventos climáticos reales (temporales, ráfagas) y son útiles para que el modelo aprenda de condiciones extremas.

Ingeniería de características (lo más importante de esta sección): se creó una clase de transformación personalizada que:

Convierte la hora y el mes en variables cíclicas (usando seno/coseno), para que el modelo entienda que, por ejemplo, la hora 23 está "cerca" de la hora 0.
Genera variables de historial (lags): mediciones de 1, 2, 3, 12 y 24 horas atrás, además de promedios móviles de 6 horas, para temperatura, humedad, sensación térmica, nubosidad y horas de sol.
Calcula "deltas" térmicos: qué tan rápido está subiendo o bajando la temperatura.
Elimina todas las variables del momento presente antes de entrenar. Esto es clave para evitar "fuga de datos" (data leakage): el modelo solo puede usar información pasada, nunca datos del mismo momento que se quiere predecir, para que sea realmente funcional en un escenario real.

Preprocesamiento: las variables numéricas se estandarizan (misma escala) y las categóricas (como la localidad) se convierten a formato numérico mediante one-hot encoding. Todo esto se organiza en un pipeline automatizado de Scikit-Learn.

Modelos entrenados:

Regresión Lineal – R² de 0.989 en test.
Árbol de Decisión (profundidad máxima 25) – R² de 0.982 en test.
Error promedio (MAE) de ambos modelos: aproximadamente 0.4°C, lo que se considera un margen aceptable para decisiones agrícolas.
Se comparó el rendimiento en entrenamiento vs. prueba para descartar sobreajuste.

Casos de uso propuestos: riego preventivo dinámico (Regresión Lineal) y alertas tempranas de heladas (Árbol de Decisión), además de comparación de microclimas entre localidades.

Sección B – Predicción de lluvia (clasificación)

Preparación: se construye una variable objetivo binaria (is_rain_hour: llueve o no llueve). Igual que en la sección anterior, solo se usan datos de horas anteriores (lags de 1 hora) y variables cíclicas de tiempo, eliminando las variables del presente para evitar fuga de datos.

Verificación de supuestos: antes de usar Naive Bayes, se comprobó si las variables cumplían los supuestos que este algoritmo necesita (normalidad e independencia). El resultado fue que no se cumplen (las variables cíclicas y los datos meteorológicos de distintas localidades no siguen una distribución normal), pero se entrenó el modelo de todas formas, sabiendo que sus probabilidades de salida serán menos confiables que las de los otros algoritmos.

Modelos entrenados (usando Pipeline + búsqueda de mejores parámetros con GridSearchCV):

Regresión Logística
Árbol de Decisión
Naive Bayes Gaussiano

Se validó que los modelos cumplieran métricas mínimas (F1 ≥ 0.48, ROC-AUC ≥ 0.79) y que no hubiera sobreajuste. Se seleccionó automáticamente el mejor modelo según su desempeño.

Ajuste del umbral de decisión: se probaron distintos umbrales de corte (0.3, 0.5 y 0.7) para decidir cuándo el modelo "avisa" que va a llover. Se eligió el umbral de 0.3 porque, en el contexto agrícola, es más costoso no avisar una lluvia real (falso negativo, con riesgo de pérdida de cultivos) que avisar una lluvia que no ocurre (falso positivo, con un costo menor y reversible). Por eso se prioriza detectar la mayor cantidad posible de lluvias reales (recall) por sobre la precisión exacta.

Salida: se generan archivos CSV con las predicciones y probabilidades de los tres modelos.

Sección C – Segmentación de perfiles climáticos (clustering)

Objetivo: a diferencia de las secciones anteriores (que predicen algo puntual), aquí se busca descubrir grupos o "perfiles climáticos" ocultos en los datos históricos, sin necesidad de una variable objetivo.

Variables usadas: temperatura, humedad, precipitación, viento, nubosidad y horas de sol — elegidas por su relevancia agronómica directa (estrés térmico, riesgo de hongos, fotosíntesis, daño mecánico a plantas, etc.).

Método:

Se estandarizaron los datos (obligatorio para K-Means, que se basa en distancias).
Se determinó automáticamente el número óptimo de grupos (clusters) usando el método del codo con la librería Kneed.
Se entrenó el modelo K-Means con ese número óptimo de clusters.
Se usó PCA para reducir las 6 variables a 2 dimensiones y poder visualizar los clusters en un gráfico 2D, conservando la mayor parte de la información posible.

Resultado: se identificaron 5 perfiles climáticos distintos, interpretados en términos de negocio: invernal con viento fuerte, frío-nublado-calmo, veraniego-despejado (óptimo para labores agrícolas), tormenta activa (alerta máxima) y frío con niebla/llovizna.

Recomendaciones derivadas: riego dinámico según el perfil de alta radiación/baja humedad, protocolos preventivos de fungicidas en perfiles fríos y húmedos, y programación de labores físicas intensivas durante los periodos templados y despejados.

Resumen de archivos generados
data_modelo_G4.csv — dataset preparado para el modelo de temperatura.
predicciones_threshold_0_3.csv / predicciones_threshold_0_7.csv — predicciones de lluvia con distintos umbrales.
predicciones_G4.csv — predicciones finales de los tres modelos de clasificación.
estructura_arbol_agroinnova.png — visualización del árbol de decisión.Objetivo del proyecto

El notebook desarrolla un sistema de análisis de datos climáticos para la empresa ficticia Agroinnova, con tres metas: predecir la temperatura futura, predecir si lloverá en las próximas horas, y descubrir perfiles climáticos ocultos en los datos. Todo esto pensado para apoyar decisiones agrícolas como riego, protección de cultivos y prevención de heladas.

Los datos vienen de un CSV con mediciones meteorológicas horarias de distintas localidades de Chile.

Sección A – Preparación de datos y predicción de temperatura

Limpieza de datos: se revisó el dataset en busca de filas duplicadas, valores nulos e inconsistencias lógicas (humedad fuera de 0-100%, nubosidad negativa, etc.). No se encontró ningún problema, por lo que los datos ya venían limpios.

Valores atípicos: se detectaron datos fuera de rango en precipitación y viento, pero se decidió conservarlos porque representan eventos climáticos reales (temporales, ráfagas) y son útiles para que el modelo aprenda de condiciones extremas.

Ingeniería de características (lo más importante de esta sección): se creó una clase de transformación personalizada que:

Convierte la hora y el mes en variables cíclicas (usando seno/coseno), para que el modelo entienda que, por ejemplo, la hora 23 está "cerca" de la hora 0.
Genera variables de historial (lags): mediciones de 1, 2, 3, 12 y 24 horas atrás, además de promedios móviles de 6 horas, para temperatura, humedad, sensación térmica, nubosidad y horas de sol.
Calcula "deltas" térmicos: qué tan rápido está subiendo o bajando la temperatura.
Elimina todas las variables del momento presente antes de entrenar. Esto es clave para evitar "fuga de datos" (data leakage): el modelo solo puede usar información pasada, nunca datos del mismo momento que se quiere predecir, para que sea realmente funcional en un escenario real.

Preprocesamiento: las variables numéricas se estandarizan (misma escala) y las categóricas (como la localidad) se convierten a formato numérico mediante one-hot encoding. Todo esto se organiza en un pipeline automatizado de Scikit-Learn.

Modelos entrenados:

Regresión Lineal – R² de 0.989 en test.
Árbol de Decisión (profundidad máxima 25) – R² de 0.982 en test.
Error promedio (MAE) de ambos modelos: aproximadamente 0.4°C, lo que se considera un margen aceptable para decisiones agrícolas.
Se comparó el rendimiento en entrenamiento vs. prueba para descartar sobreajuste.

Casos de uso propuestos: riego preventivo dinámico (Regresión Lineal) y alertas tempranas de heladas (Árbol de Decisión), además de comparación de microclimas entre localidades.

Sección B – Predicción de lluvia (clasificación)

Preparación: se construye una variable objetivo binaria (is_rain_hour: llueve o no llueve). Igual que en la sección anterior, solo se usan datos de horas anteriores (lags de 1 hora) y variables cíclicas de tiempo, eliminando las variables del presente para evitar fuga de datos.

Verificación de supuestos: antes de usar Naive Bayes, se comprobó si las variables cumplían los supuestos que este algoritmo necesita (normalidad e independencia). El resultado fue que no se cumplen (las variables cíclicas y los datos meteorológicos de distintas localidades no siguen una distribución normal), pero se entrenó el modelo de todas formas, sabiendo que sus probabilidades de salida serán menos confiables que las de los otros algoritmos.

Modelos entrenados (usando Pipeline + búsqueda de mejores parámetros con GridSearchCV):

Regresión Logística
Árbol de Decisión
Naive Bayes Gaussiano

Se validó que los modelos cumplieran métricas mínimas (F1 ≥ 0.48, ROC-AUC ≥ 0.79) y que no hubiera sobreajuste. Se seleccionó automáticamente el mejor modelo según su desempeño.

Ajuste del umbral de decisión: se probaron distintos umbrales de corte (0.3, 0.5 y 0.7) para decidir cuándo el modelo "avisa" que va a llover. Se eligió el umbral de 0.3 porque, en el contexto agrícola, es más costoso no avisar una lluvia real (falso negativo, con riesgo de pérdida de cultivos) que avisar una lluvia que no ocurre (falso positivo, con un costo menor y reversible). Por eso se prioriza detectar la mayor cantidad posible de lluvias reales (recall) por sobre la precisión exacta.

Salida: se generan archivos CSV con las predicciones y probabilidades de los tres modelos.

Sección C – Segmentación de perfiles climáticos (clustering)

Objetivo: a diferencia de las secciones anteriores (que predicen algo puntual), aquí se busca descubrir grupos o "perfiles climáticos" ocultos en los datos históricos, sin necesidad de una variable objetivo.

Variables usadas: temperatura, humedad, precipitación, viento, nubosidad y horas de sol — elegidas por su relevancia agronómica directa (estrés térmico, riesgo de hongos, fotosíntesis, daño mecánico a plantas, etc.).

Método:

Se estandarizaron los datos (obligatorio para K-Means, que se basa en distancias).
Se determinó automáticamente el número óptimo de grupos (clusters) usando el método del codo con la librería Kneed.
Se entrenó el modelo K-Means con ese número óptimo de clusters.
Se usó PCA para reducir las 6 variables a 2 dimensiones y poder visualizar los clusters en un gráfico 2D, conservando la mayor parte de la información posible.

Resultado: se identificaron 5 perfiles climáticos distintos, interpretados en términos de negocio: invernal con viento fuerte, frío-nublado-calmo, veraniego-despejado (óptimo para labores agrícolas), tormenta activa (alerta máxima) y frío con niebla/llovizna.

Recomendaciones derivadas: riego dinámico según el perfil de alta radiación/baja humedad, protocolos preventivos de fungicidas en perfiles fríos y húmedos, y programación de labores físicas intensivas durante los periodos templados y despejados.

Resumen de archivos generados
data_modelo_G4.csv — dataset preparado para el modelo de temperatura.
predicciones_threshold_0_3.csv / predicciones_threshold_0_7.csv — predicciones de lluvia con distintos umbrales.
predicciones_G4.csv — predicciones finales de los tres modelos de clasificación.
estructura_arbol_agroinnova.png — visualización del árbol de decisión.
