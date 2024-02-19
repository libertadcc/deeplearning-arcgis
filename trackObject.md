# Seguimiento de objetos

#### Detección de objetos

Muchas veces pensamos que necesitamos un modelo de seguimiento de objetos cuando en realidad solo necesitamos ejecutar el modelo de detección de objetos pero sobre un vídeo. 

Los modelos de detección de objetos también pueden utilizarse para detectar objetos en vídeos con la función ```predict_video```. Esta función aplica el modelo a cada fotograma del vídeo y proporciona las clases y cuadros delimitadores de los objetos detectados en cada fotograma. Toda esa información se alamacena enun archivo de metadatos. Los objetos detectados pueden visualizarse en el vídeo usando el parámetro ```visualize=True```. 
El archivo de metadatos es un CSV que contiene metadatos sobre los fotogramas del vídeo para momentos específicos. El archivo CSV se actualiza codificando las detecciones de objetos en el estándar MISB 0903 en la columna ```vmtilocaldataset```. 

[Ejemplo de detección de objetos en vídeo](./ObjectTracking/predict_video.md)

## Seguimiento de objetos

El seguimiento de objetos nos permite saber cuántos objetos hay y qué trayectorias siguen. Podemos resumirlo como un proceso de:
- Tomar un conjunto inicial de detecciones de objetos (como un conjunto de entrada de coordenadas de cuadro delimitador).
- Crear un identificador único para cada detección inicial.
- Rastrear (track) cada uno de los objetos a medida que se mueven por los fotogramas del vídeo manteniendo la asignación de IDs únicos. 

El **seguimiento de objetos** en *arcgis.learn* se basa en el algoritmo SORT (Simple Online Realtime Tracking) que combina el filtrado de Kalman y el algorimo de asignación húngara. 

El filtro de Kalman se usa para estimar la posición de un tracker mientras que el algoritmo húngaro se utiliza para asignar trackers a una nueva detección. 

#### Filtro de Kalman
El filtrado de Kalman utiliza una serie de mediciones observadas a lo largo del tiempo y produce estimaciones de variables desconocidas mediante la estimación de una distribución de probabilidad conjunta sobre las variables en cada frame. 
Nuestro estado contiene 8 variables (u, v, a, h, u', v', a', h') donde:
- (u, v) son los centros de los cuadros delimitadores
- a es la relación de aspecto
- h es la altura de la imagen
- u', v', a', h' son sus respectivas velocidades

Se utiliza un filtro de Kalman en cada cuadro delimitador por lo que el movimiento se puede asociar con un tracker. Cuando la asociación está hecho, se llaman a las funciones de predicción y actualización:
- Predecir: es una multiplicación de matrices que nos dirá la posición del cuadro delimitador (bounding box) en el tiempo *t* basándose en su posición en el tiempo *t-1*. 
- Actualizar: es un paso de correción. Incluye las nuevas mediciones del modelo de detección de objetos y mejora nuestro filtro. 


#### Algoritmo de asociación húngaro
El algoritmo húngaro puede asociar un obstáculo de un fotograma a otro basándose en una puntuación como intersección sobre unión (IoU). 
Se recorre la lista de trackers y detecciones y se le asigna un tracker a cada detección en base a la puntuación de IoU. El proceso general consiste en detectar obstáculos mediante un algoritmo de detección de objetos, cotejar esas bounding box con los anteriores y predecir las posiciones futuras de esos cuadros o las posiciones reales mediante filtros de Kalman. 

## [Seguimiento de objetos con algoritmo SiamMask](./ObjectTracking/siamMask.md)

Cuando queremos seguir un solo elemento dibujado a mano, podemos utilizar esta arquitectura de modelo. 

## Seguimiento multi-objeto con ObjectTracker

Sin embargo, cuando queramos detectar más de un elemento y seguir su trayectoria deberemos hacer un **seguimiento multi-objeto**. Los modelos de detección de objetos basado en deep learning de *arcgis.learn* pueden utilizarse para detectar objetos de interés. 

La clase [**ObjectTracker**](https://developers.arcgis.com/python/api-reference/arcgis.learn.toc.html#objecttracker) de *arcgis.learn* empareja **modelo de detección de objetos** y **modelos de seguimiento de objetos** para permitir el seguimiento multi-objeto basado en deep learning. 

Podemo utilizar modelos pre-entrenados o entrenar modelos propios. 

Los pasos a seguir serían:
1. **Entrenar o cargar el modelo detector de objetos**.

    Seleccionamos uno de los modelos de detección de objetos de *arcgis.learn* y lo reentrenamos.
    > [Guía para la detección de objetos con *arcgis.learn*](https://developers.arcgis.com/python/guide/object-detection-and-tracking-on-videos/)

2. **Entrenar o cargar el trackeo de objetos**. 

    Con la arquitectura o modelo deseado como puede ser [SiamMask](./ObjectTracking/siamMask.md)

3. **Inicializar y rastrear objetos con ObjectTracker**

    La clase ObjectTracker se inicializa pasandóle como parámetros el modelo de detección de objetos y el de trackeo de objetos. 

### Ejemplo de uso

```python
# Cargar los modelos de detección de objetos y de seguimiento
from arcgis.learn import ObjectDetectionModel, ObjectTrackingModel

# Cargar ObjectTracker
from arcgis.learn import ObjectTracker

# Inicialización de detección de objetos y de seguimiento
detection_model = ObjectDetectionModel.from_model(DETECTION_MODEL_PATH)
tracking_model = ObjectTrackingModel.from_model(TRACKING_MODEL_PATH)
```

Una vez cargados los modelos de detección y seguimiento de objetos, nos faltaría inicializar el ObjectTracker al que le podemos pasar diferentes [parámetros](https://developers.arcgis.com/python/api-reference/arcgis.learn.toc.html#objecttracker):

- ```detection_interval```: intervalo de fotogramas en el que se invoca al detector.
- ```detection_threshold```: umbral inferior para seleccionar las detecciones.
- ```detect_track_failure```: bandera que activa/desactiva la lógica para detectar si la apariencia del objeto ha cambiado la detección.
- ```recover_track```: flag que habilita/deshabilita la recuperación de la pista tras un fallo.
- ```stab_period```: nº de fotogramas tras los cuales se inicia el post-procesado.
- ```detect_fail_interval```: nº de frames tras los que se detecta un fallo de trackeo.
- ```min_obj_size```: tamaño de píxeles por debajo del cual se asume que el seguimiento ha fallado.
- ```template_history```: nº de frames anteriores al frame actual en los que se obtiene la plantilla de la imagen.
- ```status_history```: nº de frames sobre los que se utiliza el estado del trackeo para detectar un fallo.
- ```status_fail_threshold```: umbral del ratio entre el número de frames en el que el objeto es buscado y el nº total de frames que es necesario cruzar para detectar el fallo en el seguimiento. 
- ```search_period```: nº de frames en los que se busca el objeto antes de declarar que se ha perdido.
- ```knn_distance_ratio```: umbral para la relación de las distancias entre el descriptor de la plantilla y los dos descriptores de detección que mejor coinciden. Es utilizado para filtrar las mejores coincidencias.
- ```recover_conf_threshold```: valor mínimo de confianza a partir del cual se activa la lógica de recuperación.
- ```recover_iou_threshold```: superposición mínima entre la plantilla y la detección para una recuperación correcta. 

```python
tracker = ObjectTracker(tracking_model, detector=detection_model, tracker_options=TRACKER_OPTIONS)
``` 
**Iniciar los objetos**
Una vez "montados" los tres componentes clave (ObjectTracker, modelo de detección y modelo de seguimiento) faltaría inicializar el seguimiento de los objetos detectados utilizando el ```tracker``` que acabamos de declarar:

```python
tracks = tracker.init(frame)
```

Este método ```init``` devolverá una lista de los tracks inicializados y como parámetros puede recibir:
- ```frame```: array de numpy obligatorio. Es utilizado para inicializar los objetos a seguir. *
- ```detections```: lista de cajas delimitadoras para iniciar el seguimiento.
- ```labels```: lista de etiquetas correspondientes a las detecciones.
- ```reset```: flag que indica si el tracker debe ser reiniciado y eliminar todos los seguimientos anteriores. 

**Actualizar la posición de los objetos**
El método ```update``` permite actualizar la ubicación de los objetos y reinicializarlos. También tenemos que pasarle el ```frame```, array de numpy, y nos devolverá una lista de los seguimientos actualizada. 

```python
tracks = tracker.update(frame)
```

## Ejemplo de uso
Código ejecutado en **ArcGIS Pro**:
```python
import os
from arcgis.learn import ObjectTracker, YOLOv3, SiamMask
import cv2
import numpy as np

detection_model = YOLOv3()
tracking_model = SiamMask()
tracker = ObjectTracker(tracking_model, detector=detection_model)

# Ruta del video original
video_path = r"C:\XXX\video_2.mp4"

# Obtener el directorio y el nombre del video original
video_directory, video_filename = os.path.split(video_path)

# Crear una nueva ruta para el archivo de video de salida
output_video_path = os.path.join(video_directory, "siam_" + video_filename)

cap = cv2.VideoCapture(video_path)
if not cap.isOpened():
    print("Error: No se pudo abrir el archivo de video.")
    exit()

cap_write = cv2.VideoWriter(
    filename=output_video_path, 
    apiPreference=cv2.CAP_FFMPEG,
    fourcc=cv2.VideoWriter_fourcc(*'MJPG'), 
    fps=30, 
    frameSize=(1920, 1080)
)

success, frame = cap.read()

if success:
    frame_copy = np.copy(frame)  # Hacer una copia de la imagen para evitar el warning de PyTorch
    tracks = tracker.init(frame_copy)
    for track in tracks:
        p1 = (int(track.bbox[0]), int(track.bbox[1]))
        p2 = (int(track.bbox[0] + track.bbox[2]), int(track.bbox[1] + track.bbox[3]))
        cv2.rectangle(frame_copy, p1, p2, (0, 255, 0), 2, 1)
    cap_write.write(frame_copy)

while cap.isOpened():
    success, frame = cap.read()
    if not success:
        break

    frame_copy = np.copy(frame)  # Hacer una copia de la imagen para evitar el warning de PyTorch
    tracks = tracker.update(frame_copy)

    for track in tracks:
        p1 = (int(track.bbox[0]), int(track.bbox[1]))
        p2 = (int(track.bbox[0] + track.bbox[2]), int(track.bbox[1] + track.bbox[3]))
        cv2.rectangle(frame_copy, p1, p2, (0, 0, 255), 2, 1)
    cap_write.write(frame_copy)

cap.release()
cap_write.release()
cv2.destroyAllWindows()

print("El archivo de video se ha guardado en:", output_video_path)

```
**Resultado:**
![Resultado](./assets/siam_video_2.gif)

Si modificamos el código podemos mejorar el resultado añadiendo información extra como la etiqueta que le ha asignado el modelo a cada objeto y el valor de confianza:

```python

import os

from arcgis.learn import ObjectTracker, YOLOv3, SiamMask
import cv2
import numpy as np

detection_model = YOLOv3()
tracking_model = SiamMask()
tracker = ObjectTracker(tracking_model, detector=detection_model)

# Ruta del video original
video_path = r"C:\Datos\test\video_2.mp4"

# Obtener el directorio y el nombre del video original
video_directory, video_filename = os.path.split(video_path)

# Crear una nueva ruta para el archivo de video de salida
output_video_path = os.path.join(video_directory, "siam_tracker_score_label_" + video_filename)

cap = cv2.VideoCapture(video_path)
if not cap.isOpened():
    print("Error: No se pudo abrir el archivo de video.")
    exit()

# Usar el códec MJPEG
cap_write = cv2.VideoWriter(filename=output_video_path, apiPreference=cv2.CAP_FFMPEG, \
                            fourcc=cv2.VideoWriter_fourcc(*'MJPG'), fps=30, frameSize=(1920, 1080))

success, frame = cap.read()

if success:
    frame_copy = np.copy(frame)  # Hacer una copia de la imagen para evitar el warning de PyTorch
    tracks = tracker.init(frame_copy)
    for i, track in enumerate(tracks):
        p1 = (int(track.bbox[0]), int(track.bbox[1]))
        p2 = (int(track.bbox[0] + track.bbox[2]), int(track.bbox[1] + track.bbox[3]))
        threshold = round(track.score, 2)  # Redondear el umbral a dos decimales
        label = track.label  # Obtener la etiqueta del objeto
        cv2.rectangle(frame_copy, p1, p2, (0, 255, 0), 2, 1)
        cv2.putText(frame_copy, f"{label} ({threshold})", (p1[0], p1[1] - 5), cv2.FONT_HERSHEY_SIMPLEX, 0.5, (255, 255, 255), 2)
    cap_write.write(frame_copy)

while cap.isOpened():
    success, frame = cap.read()
    if not success:
        break

    frame_copy = np.copy(frame)  # Hacer una copia de la imagen para evitar el warning de PyTorch
    tracks = tracker.update(frame_copy)

    for i, track in enumerate(tracks):
        p1 = (int(track.bbox[0]), int(track.bbox[1]))
        p2 = (int(track.bbox[0] + track.bbox[2]), int(track.bbox[1] + track.bbox[3]))
        threshold = round(track.score, 2)  # Redondear el umbral a dos decimales
        label = track.label  # Obtener la etiqueta del objeto
        cv2.rectangle(frame_copy, p1, p2, (0, 0, 255), 2, 1)
        cv2.putText(frame_copy, f"{label} ({threshold})", (p1[0], p1[1] - 5), cv2.FONT_HERSHEY_SIMPLEX, 0.5, (255, 255, 255), 2)
    cap_write.write(frame_copy)

cap.release()
cap_write.release()
cv2.destroyAllWindows()

print("El archivo de video se ha guardado en:", output_video_path)
```

**Resultado:**
![Resultado con etiquetas y score](./assets/siam_tracker_score_label_video_2.gif)
