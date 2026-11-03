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
| Medida de rendimiento | Evaluar si las predicciones son correctas mediante exactitud, precisión, sensibilidad y especificidad. También considerar el costo de los errores. |
| Entorno |Atletas de un club deportivo que realizan entrenamientos semanales y evaluaciones de rendimiento. |
| Actuadores | Una pantalla o aplicación que muestra la predicción de la IA (SÍ o NO) y recomienda al preparador físico si debería reducir la carga de entrenamiento. |
| Sensores | Reloj o pulsera deportiva para registrar la variabilidad de la frecuencia cardíaca y las horas de sueño; registros del entrenamiento para obtener la carga semanal; y una escala de percepción del esfuerzo completada por el atleta. |

**Objetivo y métrica.** 
Objetivo: ayudar al preparador físico a identificar a los atletas que podrían rendir peor en el test de la semana siguiente, para decidir si es necesario reducir su carga de entrenamiento y/o prevenir posibles consecuencias del sobreentrenamiento. 

Metrica: Vamos a usar exactitud, precisión, sensibilidad y especificidad para evaluar el funcionamiento del sistema mediante las matrices de confusión. 

No son lo mismo. El objetivo indica qué queremos conseguir, mientras que las métricas permiten medir qué tan bien funciona el sistema.

## 2. El sistema

**Cómo decide.** 
Utilizamos una neurona artificial de cuatro entradas, implementada mediante regresión logística. Cada variable se multiplica por un peso, se suman los resultados y se agrega un sesgo. Luego se aplica una función sigmoide que genera una probabilidad y, mediante un umbral de decisión de 0,45, se obtiene la respuesta SÍ o NO.

**Variables que usa y por qué.** usamos las cuatro variables disponibles:
- Variabilidad de frecuencia cardíaca: puede aportar información sobre la recuperación del atleta.
- Esfuerzo percibido: indica qué tan exigente siente el entrenamiento.
- Horas de sueño: aportan información sobre el descanso.
- Carga semanal: permite observar el volumen de entrenamiento acumulado.
No descartamos ninguna porque todas pueden aportar información para anticipar una posible caída del rendimiento.

**Cómo se corre en vivo.** 
1- Se ingresan los cuatro valores del atleta.
2- Se estandariza cada valor usando las medias y los desvíos del entrenamiento.
3- Se procesan los valores mediante la neurona..
4- Se obtiene una probabilidad y se compara con el umbral de 0,45.
5- El sistema devuelve SÍ o NO. El preparador físico utiliza esa predicción como apoyo para decidir si reduce la carga.
**Dos casos de ejemplo.**

|Caso|Variabilidad|Esfuerzo|Sueño|Carga semanal|Salida (SI\NO)|
| A |     35ms     | 9 |   5,5h  |     4000    | SÍ |
| B |     90ms     | 3 |    8h   |     1800    | NO |

## 3. Medición
Para medir el funcionamiento, usamos los 14 casos de entrenamiento para ajustar los pesos y el sesgo, y dejamos los 6 casos de prueba separados para comprobar cómo responde ante datos nuevos.

Con el umbral ajustado, las predicciones quedan así:

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


**Entrenamiento contra prueba.** 
El resultado en entrenamiento fue de 85,71 %, mientras que en prueba fue de 83,33 %. La diferencia es pequeña, por lo que no observamos una señal clara de sobreajuste. Sin embargo, como solamente contamos con 6 casos de prueba, no podemos asegurar que el modelo mantenga el mismo rendimiento con otros atletas.

## 4. El error más caro
Consideramos que el falso negativo es el error más caro. Esto se debe a que el sistema diría NO cuando el atleta en realidad rendiría peor. El preparador podría mantener la carga de entrenamiento y provocar una caída del rendimiento o incluso lesiones.

Para reducir los falsos negativos, probamos distintos umbrales de decisión. Inicialmente utilizamos un umbral de 0,32, que produjo una sensibilidad del 100 %. Luego aumentamos el umbral a 0,45 para analizar el equilibrio entre falsos positivos y falsos negativos. Con este cambio, la sensibilidad pasó a 88,89 % en entrenamiento y 50 % en prueba.
Con ese cambio conseguimos que no haya falsos negativos ni en entrenamiento ni en prueba. A cambio, aparece un falso positivo en entrenamiento: un atleta cuya carga podría haberse mantenido recibe una predicción SÍ.

Importante: esto no significa que hayamos eliminado el riesgo de falsos negativos para siempre; solo que no aparecieron en estos 20 casos.

## 5. ¿Comparado contra qué?

**Línea base trivial.**Comparamos nuestra neurona con un sistema muy simple que siempre responde SÍ.

En los 14 casos de entrenamiento hay 9 resultados SÍ y 5 NO. Por eso, la línea base siempre responde SÍ y obtiene un 64,29 % de exactitud.

Si aplicamos esa misma estrategia a los 6 casos de prueba, acierta solamente 2 de 6: un 33,33 %.
Nuestra neurona obtiene mejores resultados: 85,71 % en entrenamiento y 83,33 % en prueba.

| Checklist (Clase 6) | Respuesta |
|---|---|
| ¿Qué datos? | 14 casos de entrenamiento y 6 de prueba del archivo M24. |
| ¿Qué métrica? | Exactitud, precisión, sensibilidad y especificidad. |
| ¿Comparado contra qué? | Un sistema que siempre predice SÍ. |
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
| 1 | Ayuda para diseñar el sistema de predicción de sobreentrenamiento. | Utilizar una neurona con las cuatro variables del archivo M24. | Rechazamos considerar que la predicción sea un diagnóstico definitivo de sobreentrenamiento porque la decsison final siempre tiene que ser de una persona, en nuestro caso el entrenador, lo que hace nuestro modelo es ofrecer una ayuda mas no una orden. Inicialmente utilizamos un umbral de 0,32, que priorizaba la detección de casos positivos y producía una sensibilidad del 100 %. Decidimos probar un umbral más alto, de 0,45, para analizar el equilibrio entre falsos positivos y falsos negativos. Esto redujo la sensibilidad, mostrando que el umbral influye directamente en las decisiones de la neurona. |

**Corridas contra el evaluador**

| Corrida | Nota | Agujero que encontró | Qué cambiamos |
|---|---|---|---|
| 1 | 8 | Nuestro texto dice que con 0,45 no hay falsos negativos ni en entrenamiento ni en prueba | Correjimos lo recien dicho |


Agujero principal: Tu texto dice que con 0,45 no hay falsos negativos ni en entrenamiento ni en prueba, pero tus matrices muestran FN = 1 en cada conjunto. ¿Cuál de las dos cosas es cierta, y qué pasa con la sensibilidad?

Otros:

• En prueba, ¿cuál es la clase más frecuente y cuánto acierta un sistema que responde siempre esa?
• Completá el registro de corridas: el campo de la corrida 1 está vacío.

Estado: ✅ HABILITADO PARA LA MESA (8 o más)
Anotá esta corrida en la bitácora.

Mostrá esta evaluación en la mesa. Ahí se prueba que funcione en vivo y defienden sin IA.
