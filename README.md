# Predicción del uso problemático de internet en niños y adolescentes

**Universidad Icesi · Maestría en Inteligencia Artificial Aplicada (MIAA), 2026-2**  
**Autor:** [Christian Cuello](https://github.com/ChrisCuello)  
**Estado:** primera y segunda entrega documentadas; fase final en desarrollo.

## Resumen del proyecto

Este proyecto investiga si modelos de aprendizaje automático pueden estimar los cuatro niveles ordinales de severidad del uso problemático de internet (`sii`) en niños y adolescentes a partir de datos demográficos, físicos y conductuales del **Healthy Brain Network (HBN)**. Su propósito es académico y exploratorio: **no pretende diagnosticar, establecer causalidad ni sustituir una evaluación clínica**.

La pregunta central es: **¿en qué medida distintos bloques de características permiten estimar la severidad de `sii` y cuáles son las limitaciones metodológicas de esa estimación?**

## Documentación por entrega

| Recurso | Contenido y estado |
|---|---|
| [**Entrega 1: formulación**](entrega_1/README.md) | Problema social, árbol de problemas, revisión bibliográfica, pregunta, objetivos y planificación. |
| [**Entrega 2: análisis y experimentos**](entrega_2/README.md) | EDA, población de estudio, selección de variables, hipótesis, modelos de referencia, métricas y limitaciones. |
| [**Referencias completas**](docs/referencias.md) | Bibliografía con autorías explícitas, DOI y enlaces. |

**Productos académicos previstos para la entrega final:** un **informe escrito** y un **notebook de Python consolidado**. El notebook unificado todavía no está publicado en este repositorio; no debe confundirse la documentación existente con código completamente ejecutable aquí.

## Datos y acceso

**Fuente oficial:** [Child Mind Institute — Problematic Internet Use (Kaggle)](https://www.kaggle.com/competitions/child-mind-institute-problematic-internet-use).

Se utilizan las variables tabulares de `train.csv` y su diccionario, además de una auditoría de series de actigrafía. Los datos originales deben obtenerse directamente de Kaggle siguiendo sus condiciones de acceso. **El repositorio no incluye bases individuales, identificadores ni archivos Parquet.**

## Métodos y tecnologías

Se utiliza **Python**, principalmente en **Google Colab**, con bibliotecas de análisis de datos y aprendizaje automático como **pandas** y **scikit-learn**. La evaluación inicial emplea validación cruzada estratificada de 5 particiones × 3 repeticiones; imputación y transformaciones dentro de cada partición de entrenamiento; **QWK** como métrica principal, junto con **F1-macro** y análisis de clases.

Los modelos de referencia son regresión logística multiclase regularizada y **HistGradientBoostingClassifier**. Se comparan tres bloques de características: **A** (edad y sexo), **B** (A más IMC, frecuencia cardíaca y presiones arterial sistólica/diastólica), y **C** (B más SDS-T y categoría declarada de horas de internet).

## Resultados iniciales de la segunda etapa

En la cohorte principal (**2.652 participantes de 5–17 años**, con 30 casos de clase `sii=3`), el bloque C alcanzó un **QWK medio de 0,405** con regresión logística y **0,388** con boosting. El aporte incremental de las medidas físicas sobre el bloque demográfico no fue consistente. La clase severa mostró precisión muy baja (aproximadamente 5–6 %), de modo que los resultados **no justifican una aplicación clínica**.

Los resultados detallados, la evaluación por bloques y sus advertencias se presentan en la [documentación de la segunda entrega](entrega_2/README.md).

## Ejecución y reproducibilidad

1. Obtener los datos autorizados desde la [competición oficial](https://www.kaggle.com/competitions/child-mind-institute-problematic-internet-use).
2. Consultar las decisiones de preparación, exclusión de variables PCIAT y evaluación en la [entrega 2](entrega_2/README.md).
3. **Pendiente:** publicar y verificar un único notebook consolidado, junto con las dependencias e instrucciones para ejecutarlo. No se afirma aún reproducibilidad independiente desde GitHub.

## Limitaciones y uso responsable

Se trata de una evaluación **retrospectiva y concurrente** mediante validación interna, sin prueba externa independiente. Las fechas individuales de varias mediciones tabulares no están verificadas; por tanto, no se debe interpretar el resultado como predicción prospectiva. El desbalance extremo de la categoría severa, los datos faltantes y el posible solapamiento entre constructos requieren cautela. Este proyecto no ofrece diagnóstico ni recomendaciones clínicas individuales.
