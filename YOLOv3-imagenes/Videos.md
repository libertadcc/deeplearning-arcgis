# Detección de objetos en vídeos con YOLOv3

Como comentamos para la detección de objetos en imágenes, el modelo YOLO es uno de los más utilizados ya que nos permite hacer detecciones bastante acertadas de una forma rápida. 

Gracias a cargar el modelo de la ArcGIS API for Python, tenemos acceso al método concreto de [*predict_video*](https://developers.arcgis.com/python/api-reference/arcgis.learn.toc.html#arcgis.learn.YOLOv3.predict_video) que nos permite aplicar YOLO sobre vídeos. 

Este método ejecuta la predicción de un vídeo y añade las predicciones [VMTI](https://www.impleotv.com/content/stplayer/help/page_vmti.html) en el archivo de salida de metadatos. Hay que tener en cuenta que esta función solo es compatible con imágenes RGB. 

## Aplicación del modelo sobre vídeos en local

Como siempre, lo primero será cargar las librerías necesarias. 
```python
from arcgis.learn import YOLOv3
```

A continuación, tendremos que iniciar el modelo. No vamos a pasarle ningún parámetro por lo que estaremos usando el dataset de COCO.
```python
model = YOLOv3()
```

Y ya por último solo tendríamos que aplicar el modelo sobre el vídeo en cuestión. En este primer caso, es un único vídeo al cual podemos acceder por el path.

```python
video = r"C:\XXXXX\cars.mp4"
model.predict_video(video,
                    metadata_file=r"C:\XXXX\prd.csv",
                    visualize=True,
                    resize=True,
                    visual_options={
                        'color': (0, 0, 255), 
                        'fontface': 0, 
                        'show_labels': True, 
                        'show_scores': True, 
                        'thickness':2
                    })

```

## Aplicación del modelo sobre vídeos adjuntos a una Feature Layer
El proceso será el mismo, la única diferencia será el acceso a los vídeos que, en este caso, será a través de una URL en uno de los campos.

```python
### Carga de librerías necesarias
import os, json
from arcgis.learn import YOLOv3
import cv2
import matplotlib.pyplot as plt
import time
import requests
import tempfile

from arcgis.gis import GIS
gis = GIS("home")

### Acceso a la capa que tiene los vídeos en una de sus variables
videosFL = gis.content.get("478aef0a9d4743adba95f5c8377da6b6")
videosFL

### Acceso a los datos como dataframe
videosFLayer = videosFL.layers[0]
videosDF = videosFLayer.query(as_df=True)

### Carga del modelo
model = YOLOv3()

### Ejecución del modelo
# Umbral para la predicción del modelo
threshold = 0.4  
# Archivo temporal donde guardaremos los vídeos
dirpath = tempfile.mkdtemp()

# Iteración para fila del dataframe
for index, row in videosDF.iterrows():
    # Accedemos a la variable que contiene los vídeos (videoURL) y hacemos una petición con requests
    response = requests.get(row['videoURL'])
    
    # Obtenemos el identifcador de esa misma fila (Guardado en la variable OBJECTID)
    objectId = row['OBJECTID']
    
    # Path donde guardaremos el vídeo original
    local_video_path = os.path.join(dirpath, f"video_{index + 1}.mp4")

    # Path donde guardaremos los metadatos de las predicciones
    pred_path = os.path.join(dirpath, f"pred_{index + 1}.csv")
    
    # Guardar el archivo de video localmente
    with open(local_video_path, 'wb') as file:
        file.write(response.content)
    
    # Realizar predicciones con el modelo
    model.predict_video(
        input_video_path=local_video_path,
        metadata_file=pred_path, 
        threshold=threshold, 
        visualize=True, 
        visual_options={
            'color': (0, 0, 255), 
            'fontface': 0, 
            'show_labels': True, 
            'show_scores': True, 
            'thickness': 2
        }
    )
    
    # Path donde se guarda el vídeo con las predicciones
    output_path = os.path.join(dirpath, f"video_{index + 1}_predictions.avi")

    # Adjuntar el video al atributo en el Feature Layer
    videosFLayer.attachments.add(objectId, output_path)
```
**Vídeo original**
https://github.com/libertadcc/deeplearning-arcgis/blob/main/YOLOv3-imagenes/images/cars-predictions_Fq130B3m.mp4
> Vídeo de [Mike Bird](https://www.pexels.com/es-es/video/diferentes-tipos-de-vehiculos-en-la-autopista-2053100/)

**Vídeo con modelo aplicado**

https://libertadcc.github.io/deeplearning-arcgis/YOLOv3-imagenes/images/cars-predictions_Fq130B3m.mp4

Como en el caso de las imágenes, obtenemos un mejor resultado si ponemos el parámetro *resize* como verdadero:

https://libertadcc.github.io/deeplearning-arcgis/YOLOv3-imagenes/images/resizeTrue.mp4
