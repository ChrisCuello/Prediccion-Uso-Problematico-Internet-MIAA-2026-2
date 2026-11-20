# Protocolo de evaluación complementaria

La cohorte principal incluye a los participantes de 5 a 17 años con `sii` conocido. Este protocolo se fijó antes de ejecutar la evaluación complementaria. Los análisis anteriores de la misma cohorte se interpretan como exploratorios.

## Partición y datos

Se reserva el 25 % mediante una división estratificada por `sii`, con semilla 20261119. La reserva queda fuera del análisis exploratorio complementario, la selección de predictores, el ajuste, la calibración y la elección de la regla de alerta de este protocolo. La división se hace sobre la misma cohorte estudiada previamente, por lo que la reserva es una evaluación interna sobre datos ya explorados. El porcentaje de reserva permite conservar más casos severos para evaluación que una reserva del 20 %.

Se usan cinco pliegues estratificados en desarrollo y tres pliegues internos para ajustar hiperparámetros o calibrar. La semilla de validación es 2026. Cada estrategia produce una predicción por participante fuera de su pliegue de entrenamiento. Se reutilizan los preprocesadores y las rejillas del notebook. El ajuste de hiperparámetros maximiza el QWK medio interno.

## Hipótesis y criterios

| Comparación | Criterio fijado antes de la ejecución |
|---|---|
| Boosting por histogramas con C frente a B | Apoyo si la mejora observada de QWK es al menos 0,020 y el límite inferior del IC pareado del 95 % es mayor que cero. |
| Boosting con C más un bloque candidato frente a C | Un bloque es elegible si mejora el QWK al menos 0,010 y el límite inferior del IC pareado del 98,75 % supera cero. El nivel por contraste corresponde al ajuste de Bonferroni para cuatro comparaciones. Se elige como máximo un bloque y, si varios cumplen, se toma el de mayor ΔQWK. |
| Random Forest con C frente a boosting con C | Compatibilidad con una diferencia pequeña si el IC pareado del 95 % se contiene totalmente entre −0,020 y +0,020. |

Los márgenes de QWK son criterios operativos del proyecto. Los intervalos se estiman con 5.000 remuestreos pareados por participante y estratificados por clase. En desarrollo, los intervalos son descriptivos y condicionados a las predicciones fuera de pliegue. En la reserva se condicionan a los modelos congelados y a la composición por clase. Las 13 estrategias de la comparación previa conservan sus estimaciones históricas y se añade el intervalo pareado entre los dos ensambles.

## Candidatos y exploración ajustada

Además de la cobertura por edad, sexo y severidad, se examina la redundancia mediante correlaciones de Spearman entre pares disponibles. Se comparan C y C más cada uno de los siguientes paneles representativos por separado:

- PAQ-A y PAQ-C, puntuaciones totales de actividad.
- CGAS, puntuación global.
- FitnessGram, etapa máxima de resistencia, fuerza de agarre dominante y repeticiones abdominales.
- Bioimpedancia, porcentaje de grasa y masa libre de grasa.

En desarrollo se estiman asociaciones crudas y ajustadas por edad y sexo para un predictor por panel y para sueño e internet declarados. Se usa regresión logística ordinal de odds proporcionales con casos disponibles y una función spline para edad en el modelo ajustado. Los predictores se expresan por una desviación estándar calculada en su muestra de análisis. Se muestran los IC del 95 %, los denominadores y las distribuciones.

## Calibración y evaluación de una regla de alerta

Se evalúa la regresión logística de referencia y su calibración sigmoide con las mismas particiones de desarrollo. Para esta última se fija un umbral de `P(sii = 3)` con las predicciones fuera de pliegue. Entre los umbrales con sensibilidad de al menos 70 % se elige el de mayor precisión y, en caso de empate, el mayor umbral. Este objetivo es metodológico y exploratorio. Se describe el costo en alertas frente a la regla de clase con mayor probabilidad.

Después de decidir los predictores y congelar la regla, se ajustan en desarrollo el boosting con C, Random Forest con C, el boosting con el bloque adicional si alguno fue elegible, y la logística calibrada para la evaluación de alertas. Se evalúan una vez en la reserva, sin incorporar sus resultados al ajuste. Se reportan QWK, F1-macro, casos severos detectados y falsas alertas, además del IC pareado de Random Forest frente a boosting. La baja frecuencia de casos severos limita la precisión de la sensibilidad en la reserva.
