# <M24>  ¿Este atleta está sobreentrenado?

**Integrantes:** Valentino Herrera · Juan Costabel

**IA declarada:** Chat GPT

**Pista:** Neurona: perceptrón con pesos y sesgo.

**Publicación en LinkedIn:** <link>

---

## 1. Formulación y PEAS

**La pregunta.** ¿El atleta rendirá peor en el test de la semana siguiente?, SI = El atleta rinde peor en el test de la semana siguiente.

| PEAS | Nuestro sistema |
|---|---|
| Medida de rendimiento | evaluar si el atleta logra mantener o mejorar su rendimiento en el test de la semana siguiente y evitar posibles caídas de rendimiento. También se busca evitar reducciones innecesarias de la carga de entrenamiento.|
| Entorno |Atletas de un club deportivo que realizan entrenamientos semanales y evaluaciones de rendimiento. |
| Actuadores | Una pantalla o aplicación que muestra la predicción de la IA (SÍ o NO) y recomienda al preparador físico si debería reducir la carga de entrenamiento. |
| Sensores | Reloj o pulsera deportiva para registrar la variabilidad de la frecuencia cardíaca y las horas de sueño; registros del entrenamiento para obtener la carga semanal; y una escala de percepción del esfuerzo completada por el atleta. |

**Objetivo y métrica.** 
Objetivo: ayudar al preparador físico a identificar a los atletas que podrían rendir peor en el test de la semana siguiente, para decidir si es necesario reducir su carga de entrenamiento y/o prevenir posibles consecuencias del sobreentrenamiento. 

Metrica: Vamos a usar exactitud, precisión, sensibilidad y especificidad para evaluar el funcionamiento del sistema mediante las matrices de confusión. 

No son lo mismo. El objetivo indica qué queremos conseguir, mientras que las métricas permiten medir qué tan bien funciona el sistema.

## 2. El sistema

**Cómo decide.** 
Utilizamos una neurona artificial de cuatro entradas, implementada mediante regresión logística. Cada variable se multiplica por un peso, se suman los resultados y se agrega un sesgo. Luego se aplica una función sigmoide que genera una probabilidad y, mediante un umbral de decisión, se obtiene la respuesta SÍ o NO. Probamos dos umbrales: 0,45 y 0,32, para analizar cómo cambia el comportamiento del sistema.

**Variables que usa y por qué.** usamos las cuatro variables disponibles:
- Variabilidad de frecuencia cardíaca: puede aportar información sobre la recuperación del atleta.
- Esfuerzo percibido: indica qué tan exigente siente el entrenamiento.
- Horas de sueño: aportan información sobre el descanso.
- Carga semanal: permite observar el volumen de entrenamiento acumulado.
No descartamos ninguna porque todas pueden aportar información para anticipar una posible caída del rendimiento.

**Cómo se corre en vivo.** 
1- Se ingresan los cuatro valores del atleta.
2- Se estandariza cada valor utilizando las medias y los desvíos calculados durante el entrenamiento.
3- La neurona multiplica cada valor estandarizado por su peso, suma los resultados y agrega el sesgo. Luego aplica la función sigmoide para obtener una probabilidad.
4- Se compara esa probabilidad con el umbral elegido (0,45). Si es igual o superior, predice SÍ; si es inferior, predice NO.
5- El preparador físico utiliza esa predicción como apoyo para decidir si reduce la carga
**Dos casos de ejemplo.**

|Caso|Variabilidad|Esfuerzo|Sueño|Carga semanal|Salida (SI\NO)|
| A |     35ms     | 9 |   5,5h  |     4000    | SÍ |
| B |     90ms     | 3 |    8h   |     1800    | NO |

## 3. Medición
Para medir el funcionamiento, usamos los 14 casos de entrenamiento para ajustar los pesos y el sesgo, y dejamos los 6 casos de prueba separados para comprobar cómo responde ante datos nuevos.

Con el umbral ajustado a 0,45, las predicciones quedan así:

ENTRENAMIENTO
Real:      [1 0 0 1 0 0 1 1 1 0 1 1 1 1]

Predicho:  [1 0 1 1 0 0 1 1 0 0 1 1 1 1]

PRUEBA
Real:      [1 0 0 0 0 1]

Predicho:  [1 0 0 0 0 0]

MATRIZ DE ENTRENAMIENTO
VP = 8

FN = 1

FP = 1

VN = 4


MATRIZ DE PRUEBA
VP = 1

FN = 1

FP = 0

VN = 4

MÉTRICAS
              Entrenamiento  Prueba
Exactitud            85.71%  83.33%

Precisión            88.89%  100.0%

Sensibilidad         88.89%   50.0%

Especificidad         80.0%  100.0%

LÍNEA BASE: SIEMPRE SÍ
Exactitud entrenamiento: 64.29 %

Exactitud prueba: 33.33 %

PESOS APRENDIDOS
[[-1.04203214  0.64280057 -0.604763   -0.10015842]]

SESGO APRENDIDO
[0.61730024]

Después de analizar los resultados obtenidos con el umbral de 0,45, decidimos probar un umbral más bajo, de 0,32, para reducir los falsos negativos, ya que consideramos que son el error más costoso en nuestro problema.:

ENTRENAMIENTO
Real:      [1 0 0 1 0 0 1 1 1 0 1 1 1 1]
Predicho:  [1 0 1 1 0 0 1 1 1 0 1 1 1 1]

PRUEBA
Real:      [1 0 0 0 0 1]
Predicho:  [1 0 0 0 0 1]

MATRIZ DE ENTRENAMIENTO

VP = 9

FN = 0

FP = 1

VN = 4

MATRIZ DE PRUEBA

VP = 2

FN = 0

FP = 0

VN = 4

MÉTRICAS
              Entrenamiento  Prueba
Exactitud            92.86%  100.0%

Precisión             90.0%  100.0%

Sensibilidad         100.0%  100.0%

Especificidad         80.0%  100.0%

LÍNEA BASE: SIEMPRE SÍ

Exactitud entrenamiento: 64.29 %

Exactitud prueba: 33.33 %

PESOS APRENDIDOS
[[-1.04203214  0.64280057 -0.604763   -0.10015842]]

SESGO APRENDIDO
[0.61730024]



**Entrenamiento contra prueba.** 
Al comparar ambos umbrales, observamos que con 0,45 la exactitud fue de 85,71 % en entrenamiento y 83,33 % en prueba. La sensibilidad fue de 88,89 % y 50 %, respectivamente. En cambio, con 0,32 la exactitud aumentó a 92,86 % en entrenamiento y 100 % en prueba, mientras que la sensibilidad llegó al 100 % en ambos conjuntos. Esto muestra que bajar el umbral permitió detectar todos los casos positivos de nuestra muestra, aunque debemos tener en cuenta que solamente contamos con 6 casos de prueba, por lo que no podemos asegurar que el modelo mantenga esos resultados con otros atleta

## 4. El error más caro
Consideramos que el falso negativo es el error más caro porque puede provocar que se mantenga una carga de entrenamiento excesiva en un atleta que podría rendir peor, aumentando el riesgo de lesiones y de caída del rendimiento.

Para reducir este error, probamos distintos umbrales de decisión. Con el umbral de 0,45 obtuvimos un falso negativo en entrenamiento y otro en prueba. Por eso, decidimos bajar el umbral a 0,32, buscando priorizar la detección de los casos positivos y evitar falsos negativos, aunque esto implique aceptar algún falso positivo. Con este cambio, los falsos negativos pasaron de 1 a 0 tanto en entrenamiento como en prueba, y la sensibilidad aumentó del 88,89 % al 100 % en entrenamiento y del 50 % al 100 % en prueba. A cambio, apareció un falso positivo en entrenamiento, lo que significa que se podría reducir innecesariamente la carga de un atleta.
## 5. ¿Comparado contra qué?

**Línea base trivial.** Comparamos nuestra neurona con un sistema muy simple que siempre responde SÍ.

En los 14 casos de entrenamiento hay 9 resultados SÍ y 5 NO. Por eso, la línea base siempre responde SÍ y obtiene un 64,29 % de exactitud.

Si aplicamos esa misma estrategia a los 6 casos de prueba, acierta solamente 2 de 6: un 33,33 %.

También comparamos el modelo con un sistema que siempre responde NO, ya que es la clase más frecuente en los casos de prueba. Este sistema acierta 4 de los 6 casos, obteniendo una exactitud del 66,67 %.

Con el umbral de 0,45, nuestra neurona obtuvo una exactitud de 85,71 % en entrenamiento y 83,33 % en prueba. Al bajar el umbral a 0,32, obtuvo un 92,86 % en entrenamiento y un 100 % en prueba. En ambas corridas superó las líneas base utilizadas como comparación.

| Checklist (Clase 6) | Respuesta |
|---|---|
| ¿Qué datos? | 14 casos de entrenamiento y 6 de prueba del archivo M24. |
| ¿Qué métrica? | Exactitud, precisión, sensibilidad y especificidad. |
| ¿Comparado contra qué? | Un sistema que siempre predice SÍ y otro que siempre predice NO. |
| ¿Quién lo midió? |Nuestro grupo, mediante un programa en Python ejecutado en Google Colab. |

## 6. Riesgo: el camino completo

El sistema recibe los datos del atleta: variabilidad cardíaca, esfuerzo percibido, horas de sueño y carga semanal. La neurona procesa esos valores y predice SÍ o NO.

El preparador físico utiliza esa predicción para decidir si reduce la carga de entrenamiento.

Un riesgo concreto sería que la neurona prediga NO cuando el atleta realmente está por rendir peor. Si el preparador confía completamente en el sistema y mantiene la carga, podría aumentar el riesgo de lesiones y de una caída del rendimiento.

Por eso, la predicción debe servir como apoyo y no reemplazar la evaluación del preparado

## 7. Bitácora

**Uso de la IA**

| # | Qué le pedimos | Qué aceptamos | Qué rechazamos y por qué |
|---|---|---|---|
| 1 | Una evaluación del README para identificar errores y aspectos que podíamos mejorar en el proyecto. | La recomendación de bajar el umbral de 0,45 a 0,32 para reducir los falsos negativos, ya que consideramos que son el error más costoso. También aceptamos mejorar la explicación del PEAS, detallar cómo funciona la neurona y agregar la comparación con las dos líneas base. | No eliminamos los resultados de la primera corrida (umbral 0,45), porque queríamos conservarlos para mostrar la comparación con la segunda corrida (umbral 0,32). |

**Corridas contra el evaluador**

| Corrida | Nota | Agujero que encontró | Qué cambiamos |
|---|---|---|---|
| 1 | 8 | Inconsistencia entre la explicación de los falsos negativos y las matrices de confusión. También faltaba comparar con la clase más frecuente en prueba. | Corregimos la explicación de la sensibilidad y agregamos la línea base que siempre predice NO. |
|2|8,5|El umbral de 0,45 aumentaba los falsos negativos; faltaba explicar el cálculo de salida, medir el resultado del atleta y mencionar ambas líneas base en el checklist|Modificamos la medida de rendimiento, explicamos el cálculo de la neurona, bajamos el umbral a 0,32 y agregamos ambas líneas base.|
|3|9|---|---|




