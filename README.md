# Detección de Acciones

Detección de gestos usando secuencias y redes LSTM en tiempo real.

El repositorio contiene dos Jupyter Notebooks:

- **DeteccionAccionesFull**: Contiene el proyecto completo, incluyendo la recolección de datos para entrenar, la definición y entrenamiento del modelo, el testing y finalmente la implementación.
- **DeteccionAccionesImplementacion**: Contiene solo las partes necesarias para ejecutar el modelo entrenado en tiempo real, usando la cámara del dispositivo.

## DeteccionAccionesFull

En este proyecto se utilizaron 3 gestos distintos a clasificar:

- Counting: Contar con los dedos de 1 a 5.
- Waving: Saludar moviendo la mano abierta de izquierda a derecha.
- Bye: Despedirse abriendo y cerrando la mano.

La razón de usar estos gestos/acciones es que se quiere utilizar una red LSTM para discernir usando una secuencia de frames.
Los tres gestos requieren mostrar la palma de la mano y para distingir el gesto se requiere usar la secuencia de movimientos.

Cabe destacar que el **proyecto es generalizable y se puede usar para detectar cualquier acción realizada por una persona frente a la camara**.
Basta realizar la recolección de datos con videos de las acciones que se quieren detectar.

## DeteccionAccionesImplementacion

Para testear el proyecto, basta con ejecutar las celdas de este notebook, se abrirá la camara del ordenador y una ventana donde se muestra en tiempo real las predicciones del modelo.

- Por cada acción/gesto (Counting, Waving y Bye) muestra una barra horizontal correpondiente a la probabilidad de gesto (se actualiza frame a frame).
- En la parte superior se muestran los últimos 5 gestos detectados.
- Se muestra también las *"landmarks"* detectadas sobre el cuerpo con la librería MediaPipe.


### Video de muestra

https://github.com/rhoffmannv/deteccion-gestos/assets/44439632/d0b63236-f19d-4f33-880e-40c7e58764fb


# Detalles del proyecto

El proyecto se puede dividir a grandes rasgos en:

- Importación de librerías
  - MediaPipe.
  - OpenCV.
  - TensorFlow.
  - Scikit Learn.
  - Numpy.
 
- Obtención de Dataset
  - Preparación.
    - Definir acciones/gestos.
    - Definir cantidad de videos y de frames.
    - Creación de carpetas para datos.
  - Obtención de *"landmarks"*.
    - Acceder a cámara.
    - Usar MediaPipe sobre frames.
  - Extracción de *features*.
  - Creación de *dataset*.
  - División en conjuntos de *train* y *test*.

- Modelo
  - Definición de modelo.
  - Entrenamiento de modelo.
   
- Testing
  - Inferencias sobre conjunto de *test*.
  - Cálculo de *accuracy*.
 
- Implementación
  - Obtener frames usando camara en tiempo real.
  - Realizar predicción con modelo sobre *features* de secuencia de *frames*.
  - Mostrar probabilidades de detección de cada gesto.
  - Guardar predicciones y validar nuevas detecciones de gestos.
  - Mostrar texto con detecciones de gestos.

## Importación de librerías

  Entre las librerías utilizadas destacan:

  - MediaPipe: Para detectar partes del cuerpo (*"landmarks"*) en las imágenes.
  - OpenCV: Para usar la cámara del dipositivo en tiempo real.
  - TensorFlow: Para definir y entrenar el modelo con redes LSTM.
  - Scikit Learn: Para dividir conjuntos de *train* y *test* y para el cálculo de *accuracy*.
  - Numpy: Para trabajar y guardar vectores de *features* y usar datos en *pipeline* de TensorFlow.

## Obtención de Dataset

### Preparación
  #### Definir acciones/gestos

  Se decide usar los siguientes 3 gestos, usando la mano derecha:
  - Counting: Contar con los dedos de 1 a 5.
  - Waving: Saludar moviendo la mano abierta de izquierda a derecha.
  - Bye: Despedirse abriendo y cerrando la mano.

  #### Definir cantidad de videos y de frames

  Por cada acción se decide usar:
  - 30 videos/secuencias.
  - Cada secuencia es de 40 frames.

  #### Creación de carpetas para datos

  - Se crea una carpeta por gesto.
  - Dentro de cada carpeta "gesto" se crean 30 carpetas de videos (30 videos por gesto).
  - Cada carpeta de video se llenará con features (n° frames numpy arrays).
  > Notar que se guardarán los *features* extraidos, no videos.


### Obtención de *"landmarks"*

  #### Acceder a cámara
  - Para acceder a la cámara del dispositivo se usa OpenCV con línea:  
    - *cap = cv2.VideoCapture(0)*  
  - Para obtener el frame actual se usa:  
    - *ret, frame = cap.read()*  

  #### Cálculo de *landmarks*
  - Se usa librería MediaPipe y se importa modelo Holistic para detectar las partes del cuerpo en el *frame*.  
  - El modelo entrega predicciones de cara, pose, mano derecha y mano izquierda.  
  - Para cada uno entrega una serie de *landmarks* con atributos x, y, z.  
  > Ver función mediapipe_detection() en código.  
  
### Extracción de *features*
  - A partir de las *landmarks* entregadas por el modelo de MediaPipe se crea un vector de *features* para caracterizar el *frame*.  
  - Simplemente se concatenan las *landmarks* en un vector plano.  
  > Ver función extract_keypoints() en código.  
  
  > Se usan todas las *landmarks* inicialmente para poder ser usado en problema general, pero para este proyecto se usan solo las *landmarks* de la mano derecha.

### Creación de Dataset
  El proceso de recolección de datos consiste en: 
  
  - Por cada gesto:  
    * Por cada video a grabar:  
      * Por cada frame por secuencia:  
        - Extraer frame de OpenCV.  
        - Cálcular *landmarks*.  
        - Extraer vector de *feature* del *frame*.  
        - Guardar en numpy array en carpeta correspondiente.  

  - Una vez terminado el proceso de grabación, se cargan los arrays .npy para crear una matriz de *dataset* de dimensiones:  
  **(90, 40, n_landmarks)** 

  >Donde 90 corresponde al total de secuencias/videos (es 3*30 = n_gestos * n_secuencias).  
  >Donde 40 es el número de frames por video.

  - A su vez se crea en paralelo un vector de etiquetas, en códificación *One-Hot*.
  - El vector de *labels* es de dimensiones **(90, 3).
  - 
  >Donde 90 es el total de secuencias/videos.  
  >Donde 3 es el número de etiquetas distintas.
  
### División en conjuntos de *train* y *test*
  - Se divide en conjuntos de entrenamiento y de *test* usando función *train_test_split()* de Scikit Learn.
  - Se deja el 5% del *dataset* para *testing* (5 secuencias).

### Modelo
  #### Definición de modelo

  - Se usa un modelo Sequential de Keras en TensorFlow.
  - Se agregan tres redes LSTM.
  - Se agregan dos capas fully connected.
  - Se tienen tres neuronas en capa de salida con función de activación *softmax*.

  #### Parámetros de entrenamiento

  - Se usa como optimizador algoritmo Adam.
  - Se usa como función de *loss* ***Categorical Crossentropy***.
    
  #### Entrenamiento

  - Se entrena modelo usando el conjunto de entrenamiento, por medio de la función *model.fit()*.
  - Se usa TensorBoard para visualizar el entrenamiento en tiempo real.

  #### Guardar modelo
  - Se guardan los pesos del modelo en archivo **detector_gestos.h5**.
  - Uso de función *model.save()*.

### Test del modelo

  #### Inferencias sobre conjunto de *test*
  - Uso de función *model.predict()* para hacer inferencias sobre conjunto de *test*.
  - La salida del modelo corresponde a 3 números, correspondientes a la probabilidad de detección de "Counting", "Waving" y "Bye" respectivamente.
  - Se usa *np.argmax()* para determinar la etiqueta "ganadora".
  > La suma de las 3 probabilidades es 1 ie. 100%.
    
  #### Cálculo de *accuracy*
  - Uso de función accuracy_score() importada desde scikit.metrics para cálculo de *accuracy*.
  - Porcentaje de *accuracy* de 100% en conjunto de *test*.
  > El *accuracy* tan alto se explica dado la poca cantidad de instancias para el test (5% del dataset, correspondiente a 5 secuencias de video).

### Implementación
  #### Obtener frames usando camara en tiempo real
  - Se usa OpenCV para acceder a la camara y obtener *frames* en tiempo real.
  #### Realizar predicción con modelo sobre secuencia
  - Se extraen vectores de *features* de cada *frame* y se guardan en array **"sequence"**.
  - Se hace inferencia con modelo usando las *features* de los últimos 40 *frames*.
  - Se agrega etiqueta "ganadora" a array **"predictions"**
  #### Mostrar probabilidades de detección de cada gesto
  - Se muestra una barra horizontal correpondiente a la probabilidad de gesto.
  - Sobre la barra se escribe el nombre de la etiqueta.
  #### Validar nuevas detecciones de gestos
  - Se aplican filtros sobre array  **"predictions"** para válidar gestos:
    - Estabilidad: Se exige que durante los últimos 15 *frames* el modelo prediga el mismo gesto.
    - Umbral: La probabilidad de detección debe superar el 80%.
    - Distinto al último: Solo se agrega si es distinto al último gesto validado.
  - Si se pasan los 3 filtros, se agrega etiqueta a array **"sentence"**.
  #### Mostrar texto con detecciones de gestos
  - Se muestra texto en parte superior de la pantalla con los últimos 5 gestos válidados en array **"sentence"**.
  > Notar que si se realiza mismo gesto consecutivamente solo se valida una vez.
  
