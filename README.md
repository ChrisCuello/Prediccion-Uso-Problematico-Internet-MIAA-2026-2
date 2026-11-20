# Predicción del uso problemático de internet en niños y adolescentes

**Universidad Icesi · Maestría en Inteligencia Artificial Aplicada (MIAA) · 2026-2**  
**Autor** [Christian Cuello](https://github.com/ChrisCuello)

## Proyecto

Se estudia en qué medida las variables demográficas, físicas, de sueño y de uso declarado de internet permiten estimar los cuatro niveles de severidad (`sii`, de 0 a 3) en la cohorte de 5–17 años del Healthy Brain Network (HBN). El análisis es académico, retrospectivo y exploratorio.

## Documentación

| Recurso | Contenido |
|---|---|
| [Entrega 1 · Formulación](entrega_1/README.md) | Problema, antecedentes, objetivos y planificación. |
| [Entrega 2 · Análisis y resultados](entrega_2/README.md) | Cohorte, selección de variables, protocolos de evaluación, resultados y limitaciones. |
| [Notebook unificado](entrega_2/PIU_MIAA_2026_2_Notebook_Unificado.ipynb) | Análisis exploratorio, modelos de referencia, evaluación ampliada, pruebas de robustez y evaluación complementaria. |
| [Protocolo de evaluación complementaria](docs/protocolo_evaluacion_complementaria.md) | Hipótesis, criterios de selección, regla de alerta y diseño de la reserva. |
| [Referencias](docs/referencias.md) | Bibliografía del proyecto. |

Los productos académicos previstos son un informe escrito y el notebook unificado.

## Métodos y resultados

Se estudiaron **2.652 participantes de 5–17 años**, incluidos **30 con `sii=3`**. Se compararon tres bloques de predictores. El bloque A incluyó edad y sexo. El bloque B añadió IMC, frecuencia cardíaca y presión arterial sistólica y diastólica. El bloque C añadió la puntuación T de sueño (SDS-T) y la categoría de uso declarado de internet. El preprocesamiento se ajustó dentro de cada partición de entrenamiento.

En el análisis de referencia, con cinco pliegues estratificados y tres repeticiones, el bloque C alcanzó un **QWK medio de 0,405** con regresión logística y **0,388** con *boosting* por histogramas.

La evaluación ampliada comparó versiones de referencia, optimizadas y calibradas, además de un clasificador ordinal acumulativo. En la comparación de **13 estrategias** del bloque C, con cinco pliegues externos y tres internos, el *boosting* por histogramas y Random Forest alcanzaron un **QWK de 0,425** al redondear a tres decimales. Detectaron **12 y 8 de los 30 casos severos**, con **100 y 78 falsas alertas**, respectivamente. Random Forest obtuvo un **QWK de 0,417** al reservar cada estación de ingreso por turno. El QWK se redujo al evaluar a los cuatro modelos de robustez en un rango de edad diferente del empleado para entrenarlos.

El QWK de la evaluación anidada se calculó sobre las predicciones fuera de muestra de los 2.652 participantes. El análisis de referencia reporta la media de los pliegues de validación repetida. El detalle de cada protocolo está en la [documentación de la Entrega 2](entrega_2/README.md).

## Evaluación complementaria

La sección 14 incorpora distribuciones y asociaciones ordinales ajustadas por edad y sexo. Se comparó el aporte de PAQ, CGAS, FitnessGram y bioimpedancia bajo el [protocolo fijado antes de la ejecución](docs/protocolo_evaluacion_complementaria.md). La bioimpedancia fue el único panel que alcanzó el criterio de inclusión en desarrollo.

Al reservar 663 participantes, con 8 casos severos, el *boosting* obtuvo QWK de 0,373 con C y de 0,402 al añadir bioimpedancia. Random Forest con C obtuvo 0,404. La comparación pareada de Random Forest y *boosting* con C fue +0,031 (IC 95 % de −0,012 a 0,078). La regla de alerta calibrada detectó 4 de 8 casos severos y produjo 75 falsas alertas. La reserva es interna y proviene de la cohorte ya estudiada.

## Ejecución y reproducibilidad

La fuente es la competición [Child Mind Institute — Problematic Internet Use](https://www.kaggle.com/competitions/child-mind-institute-problematic-internet-use). El notebook utiliza `train.csv` y `data_dictionary.csv`. La evaluación de modelos se realizó con variables tabulares. La auditoría de actigrafía se describe en la Entrega 2.

- **Google Colab.** Abre el [notebook en Colab](https://colab.research.google.com/github/ChrisCuello/Prediccion-Uso-Problematico-Internet-MIAA-2026-2/blob/main/entrega_2/PIU_MIAA_2026_2_Notebook_Unificado.ipynb). Para descargar los datos desde Kaggle se requiere aceptar las reglas de la competición y habilitar el secreto `KAGGLE_API_TOKEN` en Colab. El notebook reutiliza los CSV si ambos están disponibles en el entorno. Si Colab agota el tiempo de espera al leer el secreto, se puede continuar con los CSV en modo local.
- **CSV disponibles.** Selecciona `FUENTE_DATOS = "LOCAL"` en la primera celda y ajusta `ARCHIVO_TRAIN_LOCAL` y `ARCHIVO_DICCIONARIO_LOCAL`. En un entorno Python propio, instala las dependencias de [`requirements.txt`](requirements.txt) para ejecutar la comparación completa.

Los resultados de la evaluación complementaria se verificaron en Python con los CSV oficiales. Las tablas principales y su interpretación se pueden leer en GitHub. Las celdas muestran los gráficos, matrices de confusión y demás resultados agregados al ejecutarse. Los archivos de salida se guardan individualmente en el entorno de ejecución, sin generar un ZIP.

## Alcance

Los resultados provienen de validación interna con datos retrospectivos. La base tabular carece de fechas completas que permitan establecer el orden de las mediciones. La prueba por estación examina sensibilidad estacional. La viabilidad clínica y el desempeño en una cohorte independiente quedan por evaluar.
