# <M24>  ¿Este atleta está sobreentrenado?

Las bases están en `BASES.md` y su ficha en `problematicas/`.

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
| Sensores | Reloj deportivo, Planilla de entrenamiento o pulsera inteligente: registra frecuencia cardíaca, esfuerzo percibido y actividad física. |

**Objetivo y métrica.** 
Objetivo: ayudar al preparador físico a identificar a los atletas que podrían rendir peor en el test de la semana siguiente, para decidir si es necesario reducir su carga de entrenamiento y/o prevenir posibles consecuencias del sobreentrenamiento. 

Metrica: Vamos a usar exactitud, precisión, sensibilidad y especificidad para evaluar el funcionamiento del sistema mediante las matrices de confusión. 

No son lo mismo. El objetivo indica qué queremos conseguir, mientras que las métricas permiten medir qué tan bien funciona el sistema.

## 2. El sistema

**Cómo decide.** 
Utilizamos una neurona artificial de cuatro entradas (perceptrón). Cada variable se multiplica por un peso, se suman los resultados y se agrega un sesgo. Finalmente, la función escalón devuelve SÍ o NO.

**Variables que usa y por qué.** usamos las cuatro variables disponibles:
- Variabilidad de frecuencia cardíaca: puede aportar información sobre la recuperación del atleta.
- Esfuerzo percibido: indica qué tan exigente siente el entrenamiento.
- Horas de sueño: aportan información sobre el descanso.
- Carga semanal: permite observar el volumen de entrenamiento acumulado.
No descartamos ninguna porque todas pueden aportar información para anticipar una posible caída del rendimiento.

**Cómo se corre en vivo.** 
1- Se ingresan los cuatro valores del atleta.
2- Se estandariza cada valor usando las medias y los desvíos del entrenamiento.
3- Se aplica la fórmula de la neurona.
4- Se obtiene una salida SÍ o NO.
5- El preparador físico utiliza esa predicción como apoyo para decidir si reduce la carga.
**Dos casos de ejemplo.**

|Caso|Variabilidad|Esfuerzo|Sueño|Carga semanal|Salida (SI\NO)|
| A |     35ms     | 9 |   5,5h  |     4000    | SÍ |
| B |     90ms     | 3 |    8h   |     1800    | NO |

## 3. Medición
Para medir el funcionamiento, usamos los 14 casos de entrenamiento para ajustar los pesos y el sesgo, y dejamos los 6 casos de prueba separados para comprobar cómo responde ante datos nuevos.

Con el umbral ajustado, las predicciones quedan así:

**Entrenamiento (casos 1–14)**

| | Predice SÍ | Predice NO |
|---|---|---|
| **Real SÍ** | VP = 9 | FN = 0 |
| **Real NO** | FP = 1 | VN = 4 |

**Prueba (casos 15–20)**

| | Predice SÍ | Predice NO |
|---|---|---|
| **Real SÍ** | VP = 2 | FN = 0 |
| **Real NO** | FP = 0 | VN = 4 |

| Métrica | Entrenamiento | Prueba |
|---|---|---|
| Exactitud | 92,86% | 100 % |
| Precisión | 90 % | 100 % |
| Sensibilidad | 100 % | 100 % |
| Especificidad | 80 % | 100 % |

**Entrenamiento contra prueba.** El resultado en prueba fue mejor que en entrenamiento: la neurona clasificó correctamente los 6 casos de prueba.
No observamos señales de sobreajuste en estos resultados, ya que el modelo también funciona bien con los datos que no utilizó para entrenarse. Sin embargo, la muestra de prueba es muy pequeña, por lo que no podemos asegurar que mantenga ese rendimiento con otros atletas.

## 4. El error más caro
Consideramos que el falso negativo es el error más caro. Esto se debe a que el sistema diría NO cuando el atleta en realidad rendiría peor. El preparador podría mantener la carga de entrenamiento y provocar una caída del rendimiento o incluso lesiones.

Para reducir este error, bajamos el umbral de decisión de 0 a -2 en la entrada neta original. Así, la neurona tiene mayor facilidad para predecir SÍ.

Con ese cambio conseguimos que no haya falsos negativos ni en entrenamiento ni en prueba. A cambio, aparece un falso positivo en entrenamiento: un atleta cuya carga podría haberse mantenido recibe una predicción SÍ.

Importante: esto no significa que hayamos eliminado el riesgo de falsos negativos para siempre; solo que no aparecieron en estos 20 casos.

## 5. ¿Comparado contra qué?

**Línea base trivial.**Comparamos nuestra neurona con un sistema muy simple que siempre responde SÍ.

En los 14 casos de entrenamiento hay 8 resultados SÍ y 6 NO. Por eso, la línea base siempre responde SÍ y obtiene un 57,14 % de exactitud.

Si aplicamos esa misma estrategia a los 6 casos de prueba, acierta solamente 2 de 6: un 33,33 %.
Nuestra neurona obtiene mejores resultados: 92,86 % en entrenamiento y 100 % en prueba.

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
| 1 | Ayuda para diseñar el sistema de predicción de sobreentrenamiento. | Utilizar una neurona con las cuatro variables del archivo M24. | Rechazamos considerar que la predicción sea un diagnóstico definitivo de sobreentrenamiento porque la decsison final siempre tiene que ser de una persona, en nuestro caso el entrenador, lo que hace nuestro modelo es ofrecer una ayuda mas no una orden. |

**Corridas contra el evaluador**

| Corrida | Nota | Agujero que encontró | Qué cambiamos |
|---|---|---|---|
| 1 | <> | <> | <> |





import numpy as np
import pandas as pd

from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import confusion_matrix

# 1. Datos del problema M24
# Columnas:
# Variabilidad cardíaca, esfuerzo, horas de sueño, carga semanal

X = np.array([
    [31, 8, 8.2, 1491],
    [88, 5, 8.5, 2175],
    [98, 8, 5.6, 3042],
    [50, 6, 7.2, 3659],
    [91, 8, 8.6, 3940],
    [78, 6, 8.0, 4192],
    [106, 8, 5.0, 1482],
    [53, 8, 5.7, 2751],
    [82, 8, 8.1, 3081],
    [80, 6, 7.6, 1501],
    [20, 9, 7.4, 3995],
    [47, 7, 6.5, 4340],
    [84, 8, 5.6, 3039],
    [32, 10, 7.0, 1000],
    [26, 5, 7.3, 1000],
    [50, 2, 8.2, 1859],
    [101, 3, 6.5, 2347],
    [91, 5, 5.6, 4464],
    [69, 3, 6.8, 2498],
    [98, 6, 5.3, 1063]
])

# Resultado real: 1 = SÍ, 0 = NO
y = np.array([
    1, 0, 0, 1, 0, 0, 1, 1, 1, 0,
    1, 1, 1, 1, 1, 0, 0, 0, 0, 1
])

# 2. Separar entrenamiento y prueba
X_train = X[:14]
y_train = y[:14]

X_test = X[14:]
y_test = y[14:]

# 3. Estandarizar las variables
# El escalador aprende únicamente de entrenamiento
escalador = StandardScaler()

X_train_scaled = escalador.fit_transform(X_train)
X_test_scaled = escalador.transform(X_test)

# 4. Crear y entrenar la neurona
modelo = LogisticRegression(
    C=1,
    solver="liblinear",
    random_state=42
)

modelo.fit(X_train_scaled, y_train)

# 5. Obtener las salidas y aplicar el umbral
umbral = 0.32

prob_train = modelo.predict_proba(X_train_scaled)[:, 1]
prob_test = modelo.predict_proba(X_test_scaled)[:, 1]

pred_train = (prob_train >= umbral).astype(int)
pred_test = (prob_test >= umbral).astype(int)

# 6. Mostrar las predicciones
print("ENTRENAMIENTO")
print("Real:     ", y_train)
print("Predicho: ", pred_train)

print("\nPRUEBA")
print("Real:     ", y_test)
print("Predicho: ", pred_test)

# 7. Matrices de confusión
# Orden: VP, FN, FP, VN
matriz_train = confusion_matrix(
    y_train, pred_train, labels=[1, 0]
)

matriz_test = confusion_matrix(
    y_test, pred_test, labels=[1, 0]
)

def mostrar_matriz(matriz):
    vp, fn = matriz[0]
    fp, vn = matriz[1]

    print(f"VP = {vp}")
    print(f"FN = {fn}")
    print(f"FP = {fp}")
    print(f"VN = {vn}")

print("\nMATRIZ DE ENTRENAMIENTO")
mostrar_matriz(matriz_train)

print("\nMATRIZ DE PRUEBA")
mostrar_matriz(matriz_test)

# 8. Calcular métricas
def calcular_metricas(matriz):
    vp, fn = matriz[0]
    fp, vn = matriz[1]

    exactitud = (vp + vn) / (vp + fn + fp + vn)
    precision = vp / (vp + fp) if vp + fp > 0 else 0
    sensibilidad = vp / (vp + fn) if vp + fn > 0 else 0
    especificidad = vn / (vn + fp) if vn + fp > 0 else 0

    return {
        "Exactitud": exactitud,
        "Precisión": precision,
        "Sensibilidad": sensibilidad,
        "Especificidad": especificidad
    }

resultados = pd.DataFrame({
    "Entrenamiento": calcular_metricas(matriz_train),
    "Prueba": calcular_metricas(matriz_test)
})

print("\nMÉTRICAS")
print((resultados * 100).round(2).astype(str) + "%")

# 9. Línea base: siempre predecir SÍ
base_train = np.ones(len(y_train))
base_test = np.ones(len(y_test))

print("\nLÍNEA BASE: SIEMPRE SÍ")
print("Exactitud entrenamiento:",
      round(np.mean(base_train == y_train) * 100, 2), "%")
print("Exactitud prueba:",
      round(np.mean(base_test == y_test) * 100, 2), "%")

# 10. Mostrar pesos y sesgo aprendidos
print("\nPESOS APRENDIDOS")
print(modelo.coef_)

print("\nSESGO APRENDIDO")
print(modelo.intercept_)

