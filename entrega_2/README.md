# Entrega 2 — Análisis exploratorio y modelos de referencia

**Universidad Icesi · Maestría en Inteligencia Artificial Aplicada · 2026-2**  
**Fuente de datos:** [Child Mind Institute — Problematic Internet Use (Kaggle)](https://www.kaggle.com/competitions/child-mind-institute-problematic-internet-use).

> Los resultados corresponden a evaluación interna exploratoria. El documento académico final y un único notebook serán los dos productos de entrega; este apartado presenta su síntesis metodológica para consulta en GitHub.

## Desarrollo y resultados de la segunda etapa

**Corte de resultados: 23 de octubre de 2026.** Esta sección documenta actividades realizadas y resultados propios de la segunda etapa. Complementa la formulación de la primera entrega y no constituye una validación clínica ni una predicción prospectiva.

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

Los ocho campos se seleccionaron por pertinencia, disponibilidad y comparabilidad de los bloques, **no por optimización posterior al resultado**. Los campos PCIAT se excluyeron como predictores porque `sii` se deriva de su puntuación total; `id` se utilizó exclusivamente como identificador. CGAS, PAQ-A/PAQ-C, FitnessGram, bioimpedancia, variables de temporada y actigrafía quedaron reservados para análisis posteriores por cobertura, solapamiento conceptual, redundancias o temporalidad no confirmada. Aplazamiento no significa descarte definitivo [14–16].

### Hipótesis y protocolo experimental

Se compararon tres contrastes:

- **H1:** agregar medidas físicas al bloque demográfico mejora el QWK (**B − A**).
- **H2:** agregar conjuntamente SDS-T e internet declarado al bloque físico mejora el QWK (**C − B**).
- **H3 (exploratoria):** cada indicador, por separado, aporta información al bloque físico (**B + SDS − B** y **B + internet − B**).

**Precisión metodológica:** los bloques A/B/C y el protocolo de comparación se definieron antes de entrenar. Sin embargo, la redacción direccional de H1/H2 se formalizó después de observar los primeros resultados y H3 se planteó en una ablación posterior. Por consiguiente, los contrastes son **descriptivos y exploratorios, no pruebas confirmatorias preinscritas**.

Se implementaron dos clasificadores de referencia: **regresión logística multiclase regularizada** e **HistGradientBoostingClassifier**. Se utilizó **validación cruzada estratificada de cinco particiones, repetida tres veces** (15 evaluaciones internas), con particiones emparejadas para las comparaciones. La imputación, indicadores de ausencia, escalamiento y codificación se ajustaron exclusivamente en la porción de entrenamiento de cada pliegue; ambos algoritmos utilizaron `class_weight="balanced"`. No se optimizaron hiperparámetros en estas corridas.

### Resultados iniciales

| Configuración | Logística QWK | Logística F1-macro | Boosting QWK | Boosting F1-macro |
|---|---:|---:|---:|---:|
| A. Demográfica | 0,325 | 0,316 | 0,284 | 0,284 |
| B. Física | 0,323 | 0,321 | 0,291 | 0,331 |
| B + sueño SDS-T | 0,368 | 0,339 | 0,362 | 0,357 |
| B + internet declarado | 0,361 | 0,342 | 0,338 | 0,346 |
| C. Ampliada | **0,405** | 0,356 | **0,388** | **0,365** |

En QWK, **B no mejoró consistentemente A** (H1 no apoyada de forma consistente). El bloque C superó B en los 15 pliegues de ambos clasificadores: diferencias medias **+0,082** en logística y **+0,097** en boosting. Los indicadores SDS-T e internet declarado también mostraron aportes individuales positivos en comparaciones exploratorias. Debido a que las evaluaciones repetidas comparten participantes, **no se tratan como 15 muestras independientes** ni se interpretan como evidencia confirmatoria.

En la categoría severa (`sii=3`), con solo **30 participantes**, la precisión de C fue de aproximadamente **6,0 %** para logística y **5,4 %** para boosting; sus sensibilidades respectivas fueron **62,2 %** y **8,9 %**. Esto evidencia numerosas falsas alarmas. El desempeño agregado no sustenta identificar casos severos de manera fiable ni implementar la solución como herramienta clínica.

### Reproducibilidad, límites y trabajo siguiente

La trazabilidad académica se apoya en el informe y en un **notebook de Python único**, que integrará el preprocesamiento, el análisis exploratorio y los experimentos principales. Dicho notebook **aún no ha sido incorporado a este repositorio**. Los datos de entrada se obtienen desde la [competición oficial de Kaggle](https://www.kaggle.com/competitions/child-mind-institute-problematic-internet-use), previa aceptación de sus condiciones de acceso; no se redistribuyen aquí conjuntos de datos individuales ni series Parquet.

Los resultados representan una **estimación concurrente exploratoria** mediante validación interna; no demuestran causalidad, rendimiento prospectivo, validez externa ni utilidad clínica. La siguiente fase podrá examinar estrategias ordinales, sensibilidad a la ponderación de clases, mecanismos de datos faltantes y evaluación independiente si resulta viable [15, 16].

**Referencias:** [bibliografía completa, con autores explícitos](../docs/referencias.md).  
**Formulación y objetivos:** [primera entrega](../entrega_1/README.md).
