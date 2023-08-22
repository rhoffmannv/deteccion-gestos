# Deteccion de Acciones

Detección de gestos usando secuencias y redes LSTM en tiempo real

El repositorio contiene dos Jupyter Notebooks:

- DeteccionAccionesFull: Contiene el proyecto completo, incluyendo la recolección de datos para entrenar, la definición y entrenamiento del modelo, el testing y finalmente la implementación.
- DeteccionAccionesImplementacion: Contiene solo las partes necesarias para ejecutar el modelo entrenado en tiempo real, usando la cámara del dispositivo.

## DeteccionAccionesFull

En este proyecto se utilizaron 3 gestos distintos a clasificar:

- Counting: Contar con los dedos de 1 a 5
- Waving: Saludar moviendo la mano abierta de izquierda a derecha
- Bye: Despedirse abriendo y cerrando la mano

La razón de usar estos gestos/acciones es que se quiere utilizar una red LSTM para discernir usando una secuencia de frames.
Los tres gestos requieren mostrar la palma de la mano y para distingir el gesto se requiere usar la secuencia de movimientos.

Cabe destacar que el **proyecto es generalizable y se puede usar para detectar cualquier acción realizada por una persona frente a la camara**.
Basta realizar la recolección de datos con videos de las acciones que se quieren detectar.

## DeteccionAccionesImplementacion

Para testear el proyecto, basta con ejecutar las celdas de este notebook, se abrirá la camara del ordenador y una ventana donde se muestra en tiempo real las predicciones del modelo.

- Por cada acción/gesto (Counting, Waving y Bye) muestra una barra horizontal correpondiente a la probabilidad de gesto (se actualiza frame a frame).
- En la parte superior se muestran los últimos 5 gestos detectados.
- Se muestra también las "landmarks" detectadas sobre el cuerpo con MediaPipe


### Video de muestra

https://github.com/rhoffmannv/deteccion-gestos/assets/44439632/d0b63236-f19d-4f33-880e-40c7e58764fb









