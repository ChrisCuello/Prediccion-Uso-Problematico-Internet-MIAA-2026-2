# Entrega 2 — Análisis exploratorio y evaluación de modelos

**Universidad Icesi · Maestría en Inteligencia Artificial Aplicada · 2026-2**

El [notebook unificado](PIU_MIAA_2026_2_Notebook_Unificado.ipynb) reúne la caracterización de la cohorte, la comparación de predictores y la evaluación de modelos. Las secciones 9, 12 y 13.4 presentan los resultados del análisis de referencia, la evaluación de siete estrategias y la comparación de 13 estrategias con pruebas de robustez.

## Análisis de referencia

El análisis de referencia corresponde al corte del 23 de octubre de 2026 y utiliza validación cruzada estratificada de cinco pliegues con tres repeticiones.

### Población y análisis exploratorio de datos

Se examinó el archivo tabular de entrenamiento de la competición HBN/Kaggle, integrado por **3.960 registros y 82 columnas**. La etiqueta ordinal `sii` estaba disponible en **2.736 participantes**. Para el análisis principal se delimitó, antes del entrenamiento, una cohorte de **2.652 participantes de 5 a 17 años**: clase 0, **1.562**; clase 1, **700**; clase 2, **360**; y clase 3, **30**. Como análisis de sensibilidad se conservaron las cohortes de 5–19 y 5–22 años.

El EDA comprendió integridad estructural, disponibilidad de etiquetas, distribución por severidad, datos faltantes, plausibilidad de mediciones físicas, relaciones y redundancias entre variables. El desbalance de la categoría severa (**1,13 % de la cohorte principal**) obliga a examinar métricas por clase, además de las métricas globales.

La auditoría instrumental de **996 series Parquet** identificó heterogeneidad en continuidad cronológica y uso del dispositivo. En **966 series** existía algún registro posterior a la administración de PCIAT. Por este motivo, y por la cobertura parcial de actigrafía, estas señales **no se utilizaron en los modelos de referencia**. La existencia de registros posteriores no equivale por sí sola a fuga matemática directa, pero impide asumir disponibilidad anticipada para una aplicación prospectiva.

### Selección razonada de características

La matriz de selección evaluó los **58 campos tabulares distintos del identificador, la etiqueta y las 22 columnas PCIAT**. Las primeras comparaciones se definieron antes de entrenar modelos:

| Bloque | Variables incluidas | Justificación |
|---|---|---|
| **A. Demográfico** | Edad y sexo | Línea base contextual, con cobertura completa en la cohorte principal. |
| **B. Físico** | A + IMC, frecuencia cardíaca, presión sistólica y presión diastólica | Determinar si las mediciones físicas aportan información adicional a la demografía. |
| **C. Ampliado** | B + puntuación T de sueño SDS y categoría declarada de horas de internet | Explorar el aporte incremental de dos indicadores conductuales, sin atribuir causalidad. |

Los ocho campos se seleccionaron por pertinencia, disponibilidad y comparabilidad de los bloques, **no por optimización posterior al resultado**. Los campos PCIAT se excluyeron como predictores porque `sii` se deriva de su puntuación total. El campo `id` se utilizó como identificador. CGAS, PAQ-A/PAQ-C, FitnessGram, bioimpedancia, variables de temporada y actigrafía quedaron reservados para análisis posteriores por cobertura, solapamiento conceptual, redundancias o temporalidad no confirmada [14–16].

### Hipótesis y protocolo experimental

Se compararon tres contrastes:

- **H1:** agregar medidas físicas al bloque demográfico mejora el QWK (**B − A**).
- **H2:** agregar conjuntamente SDS-T e internet declarado al bloque físico mejora el QWK (**C − B**).
- **H3 (exploratoria):** cada indicador, por separado, aporta información al bloque físico (**B + SDS − B** y **B + internet − B**).

**Precisión metodológica:** los bloques A/B/C y el protocolo de comparación se definieron antes de entrenar. Sin embargo, la redacción direccional de H1/H2 se formalizó después de observar los primeros resultados y H3 se planteó en una ablación posterior. Los contrastes se interpretan como descriptivos y exploratorios.

Se implementaron dos clasificadores de referencia: **regresión logística multiclase regularizada** e **HistGradientBoostingClassifier**. Se utilizó **validación cruzada estratificada de cinco particiones, repetida tres veces** (15 evaluaciones internas), con particiones emparejadas para las comparaciones. La imputación, indicadores de ausencia, escalamiento y codificación se ajustaron exclusivamente en la porción de entrenamiento de cada pliegue; ambos algoritmos utilizaron `class_weight="balanced"`. En este análisis se usaron hiperparámetros fijos.

### Resultados iniciales

| Configuración | Logística QWK | Logística F1-macro | Boosting QWK | Boosting F1-macro |
|---|---:|---:|---:|---:|
| A. Demográfica | 0,325 | 0,316 | 0,284 | 0,284 |
| B. Física | 0,323 | 0,321 | 0,291 | 0,331 |
| B + sueño SDS-T | 0,368 | 0,339 | 0,362 | 0,357 |
| B + internet declarado | 0,361 | 0,342 | 0,338 | 0,346 |
| C. Ampliada | **0,405** | 0,356 | **0,388** | **0,365** |

El efecto de añadir medidas físicas al bloque demográfico varió según el modelo y el pliegue. El bloque C superó B en los 15 pliegues de ambos clasificadores: diferencias medias **+0,082** en logística y **+0,097** en boosting. Los indicadores SDS-T e internet declarado también mostraron aportes individuales positivos en comparaciones exploratorias. Las tres repeticiones reutilizan a los mismos participantes, por lo que las métricas por pliegue tienen un alcance descriptivo.

En la categoría severa (`sii=3`), con solo **30 participantes**, la precisión de C fue de aproximadamente **6,0 %** para logística y **5,4 %** para boosting; sus sensibilidades respectivas fueron **62,2 %** y **8,9 %**. En ambos modelos, la mayoría de alertas severas correspondió a otras categorías.

## Evaluación ampliada

### Optimización y calibración

Las secciones 11 y 12 comparan siete estrategias con el bloque C en la cohorte de 5–17 años. Se evaluaron la regresión logística y el *boosting* por histogramas con sus configuraciones de referencia, optimizadas y calibradas, junto con un clasificador ordinal acumulativo. La selección de hiperparámetros y la calibración se realizaron dentro de los conjuntos de entrenamiento de los cinco pliegues externos, con tres pliegues internos.

El *boosting* optimizado alcanzó un QWK de **0,425** frente a **0,401** en su configuración de referencia. Detectó **12 de 30 casos severos**, frente a **3 de 30**, y las falsas alertas aumentaron de **48 a 100**. La calibración redujo el puntaje de Brier y el *log-loss* de ambos modelos. El QWK también disminuyó al asignar la clase de mayor probabilidad.

La sección **11.3** presenta el QWK por estrategia y la relación entre precisión y sensibilidad de la clase severa. Los ejes de la segunda gráfica están en porcentajes. La leyenda y los marcadores huecos y rellenos permiten identificar los modelos con valores próximos o coincidentes. La sección 11.4 muestra las matrices de confusión, los hiperparámetros elegidos y las métricas por pliegue.

### Comparación de 13 estrategias y robustez

La sección 13 compara una línea base de clase mayoritaria, regresiones logísticas regularizadas, KNN, ensambles de árboles, un clasificador ordinal acumulativo y aproximaciones de regresión sobre la escala 0–3. La comparación principal utilizó los mismos cinco pliegues externos estratificados para todas las estrategias. Se calculó un QWK global a partir de una predicción fuera de muestra por participante y estrategia.

| Modelo | QWK | F1-macro | Casos severos detectados de 30 | Falsas alertas severas |
|---|---:|---:|---:|---:|
| Boosting por histogramas | 0,425 | 0,397 | 12 | 100 |
| Random Forest | 0,425 | 0,392 | 8 | 78 |
| Logística L2 | 0,400 | 0,357 | 18 | 300 |
| XGBoost | 0,362 | 0,344 | 0 | 2 |

Las falsas alertas severas corresponden a predicciones de `sii=3` en participantes de las categorías 0 a 2. El *boosting* por histogramas y Random Forest obtuvieron el mayor QWK al redondear a tres decimales. La logística L2 detectó más casos severos y también generó más falsas alertas.

- **Partición.** Random Forest obtuvo el mayor QWK al reservar una estación por turno (0,417) y al reservar un 20 % estratificado de la cohorte (0,424). En la evaluación por estación detectó 4 de los 30 casos severos.
- **Disponibilidad de predictores.** El bloque C presentó el mayor QWK frente a B, B + sueño y B + internet en los cuatro modelos evaluados bajo las mismas particiones.
- **Edad.** Al entrenar en el rango de 5–12 años y evaluar en el de 13–17 años, y viceversa, los QWK oscilaron entre 0,016 y 0,234. La categoría severa incluyó 6 casos en 5–12 años y 24 en 13–17 años.
- **Diagnóstico de modelos.** El VIF máximo fue 10,33 en la matriz de entrenamiento de OLS. La edad produjo la mayor pérdida media de QWK al permutar predictores en los pliegues externos de Random Forest y del *boosting* por histogramas.

## Ejecución y alcance

El notebook usa los CSV oficiales de la [competición de Kaggle](https://www.kaggle.com/competitions/child-mind-institute-problematic-internet-use). En [Google Colab](https://colab.research.google.com/github/ChrisCuello/Prediccion-Uso-Problematico-Internet-MIAA-2026-2/blob/main/entrega_2/PIU_MIAA_2026_2_Notebook_Unificado.ipynb), la descarga utiliza `KAGGLE_API_TOKEN` desde Secretos. La lectura se reintenta una vez si el servicio no responde. Si ambos CSV ya están en el directorio de datos del entorno, el notebook los reutiliza con la configuración predeterminada. También admite `FUENTE_DATOS = "LOCAL"` y rutas explícitas a ambos archivos.

La ejecución integral se verificó en Python con los CSV oficiales. Las tablas e interpretaciones principales están incorporadas en el notebook. Al ejecutar las celdas se muestran además las gráficas y los resultados agregados. Las salidas se guardan individualmente, sin generar un ZIP. El repositorio publica el código y los resultados resumidos.

Los protocolos corresponden a validación interna sobre una sola cohorte. Las estaciones de ingreso carecen de un año asociado y se usaron para examinar sensibilidad estacional. La evaluación prospectiva y la validación en una cohorte independiente quedan pendientes [15, 16].

[Referencias del proyecto](../docs/referencias.md) · [Formulación y objetivos de la Entrega 1](../entrega_1/README.md)
