# INFORME

### Definición del conjunto de datos
El conjunto de datos utilizado corresponde al "Forest Cover Type" de scikit-learn, compuesto por aproximadamente 54 columnas: 53 características (observaciones) y una columna adicional llamada `Cover_Type`, que indica el tipo de cobertura forestal. Las características incluyen variables continuas (como Elevation, Aspect, Slope, etc.), variables categóricas codificadas como binarias (por ejemplo, tipo de suelo y tipo de área silvestre), y variables discretas. La columna `Cover_Type` se emplea para distinguir entre datos normales y anómalos, donde se considera como "normal" el tipo de cobertura 2 y como "anómalo" cualquier otro valor. Las variables binarias no se estandarizan, mientras que las variables continuas se escalan para tener valores entre aproximadamente -3 y 3, facilitando el entrenamiento de modelos sensibles a la escala de los datos.

### Preparación de los datos
Para la detección de anomalías, se realiza una separación entre datos normales y anómalos utilizando la columna `Cover_Type`. Los datos normales (donde `Cover_Type == 2`) se emplean para entrenar los modelos, mientras que los anómalos se reservan para la evaluación. Se divide el conjunto de datos en entrenamiento, validación y prueba, asegurando que los modelos solo vean ejemplos normales durante el entrenamiento. Las variables continuas se estandarizan usando `StandardScaler`, mientras que las variables binarias se mantienen sin cambios. El conjunto de prueba se compone de una mezcla de datos normales y anómalos, permitiendo evaluar la capacidad de los modelos para identificar correctamente las anomalías. Este enfoque garantiza que el Autoencoder y otros algoritmos de detección de anomalías aprendan únicamente el patrón de los datos normales, facilitando la identificación de observaciones atípicas durante la inferencia.

## Resultados

Se evaluó distintos modelos y estrategias de umbralización para la detección de anomalías en el conjunto de datos Forest Cover Type. Los principales resultados obtenidos son:

**Autoencoder (AE):**
- El autoencoder con cuello de botella de 8 dimensiones (`AE_enc8`) y umbral basado en el mejor F1 con precisión mínima de 0.85 (`F1_P85`) fue la configuración que obtuvo el mejor desempeño.
- Métricas alcanzadas por el mejor modelo:
    - **F1 Score:** 0.872
    - **Precisión:** 0.885
    - **ROC-AUC:** 0.941
- La estrategia de umbralización basada en percentiles altos (P99, P99.9) sobre los errores de reconstrucción de los datos normales permitió controlar la tasa de falsos positivos.
- El uso de validación desbalanceada (reflejando la prevalencia real de anomalías) mejoró la robustez de la selección de umbral.

**Comparación de configuraciones de Autoencoder:**
- Se probaron variantes con diferentes tamaños de cuello de botella, regularización (dropout, L2), y funciones de pérdida (MSE, MAE, Huber).
- El modelo `AE_enc8` superó consistentemente al modelo original (`AE_enc32`) en F1 y ROC-AUC.

**Isolation Forest (baseline):**
- Tras optimización de hiperparámetros, el mejor Isolation Forest alcanzó:
    - **F1 Score:** 0.849
    - **Precisión:** 0.862
    - **ROC-AUC:** 0.927
- El rendimiento fue competitivo, aunque ligeramente inferior al mejor autoencoder.

**Visualizaciones:**
- Los histogramas de error de reconstrucción muestran una mejor separación entre normales y anomalías en el modelo `AE_enc8`.
- Las matrices de confusión y gráficos comparativos de F1 y ROC-AUC confirman la mejora obtenida con la arquitectura optimizada.

**Resumen comparativo:**
| Modelo         | Estrategia   | F1 Score | Precisión | ROC-AUC |
|----------------|--------------|----------|-----------|---------|
| AE_enc32       | P99          | 0.823    | 0.835     | 0.915   |
| AE_enc8        | F1_P85       | 0.872    | 0.885     | 0.941   |
| IsolationForest| F1           | 0.849    | 0.862     | 0.927   |


## Discusión y recomendaciones

Durante el desarrollo del experimento, se observó que la arquitectura y la estrategia de umbralización influyen significativamente en el desempeño de los modelos de detección de anomalías. El autoencoder con cuello de botella reducido (`AE_enc8`) mostró una mejor capacidad para separar normales y anómalos, especialmente cuando se empleó una validación desbalanceada y umbrales basados en precisión mínima.

El modelo Isolation Forest, tras la optimización de hiperparámetros, logró resultados competitivos, aunque ligeramente inferiores al mejor autoencoder. Sin embargo, ambos enfoques demostraron ser útiles para el problema planteado.

En el caso del modelo LOF (Local Outlier Factor), hubo dificultades técnicas durante el entrenamiento, lo que impidió obtener y visualizar sus resultados. Por lo que posiblemente hubo fallos en la configuración del mismo que inhibió la posibilidad de funcionamiento. 

**Recomendaciones:**
- Priorizar arquitecturas de autoencoder con cuellos de botella estrechos y aplicar regularización para mejorar la generalización.
- Utilizar estrategias de validación desbalanceada y umbrales basados en precisión mínima para reflejar mejor la prevalencia real de anomalías.
- Continuar explorando y ajustando el modelo LOF para superar los problemas de entrenamiento y completar la comparación entre métodos.
- Realizar análisis adicionales sobre la interpretabilidad de los modelos y la robustez ante diferentes tipos de anomalías.

