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

Los tres gestos requieren mostrar la palma de la mano y para distingir el gesto se requiere usar la secuencia de movimientos

