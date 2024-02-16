# Detección y seguimiento de objetos con la función *predict_video*

Vamos a ver cómo implementar el método ```predict_video```.
> [Definición del método *predict_video*](https://developers.arcgis.com/python/api-reference/arcgis.learn.toc.html?highlight=predict_video#arcgis.learn.SingleShotDetector.predict_video)

Podemos definir los siguientes parámetros en la función:
* El VMTI final puede multiplexarse con el vídeo de entrada pasando ```multiplex=True```. Podemos definir la ruta donde se guardará en ```multiplex_file_path``` aunque por defecto el vídeo se guarda en el directorio del vídeo original. 

* El parámetro ```track=True``` puede ser usada para rastrear objetos detectados en el vídeo. Al seguir objetos detectados, podemos definir el parámetro ```tracker_options```: 
    - ```assignment_iou_thrd```: puede haber varios trackers detectando y rastreando objetos. El umral de la intersección sobre la unión (IoU) puede establecerse para asignar un rastreador con el valor de umbral mencionado.
    - ```vanish_frames``` : es el número de frames que el objeto está ausente para considerarlo desaparecido.
    - ```detect_frames```: número de frames que un objeto permanece presente para empezar a rastrearlo. 

* Con el parámetro ```visualize=True``` las detecciones serán visualizadas en el vídeo de salida que crea la función. Si queremos visualizar el resultado, también podemos personalizarlo con el parámetro ```visual_options```:
    - ```show_scores```: para visualizar la puntuación en las predicciones.
    - ```show_labels```: para visualizar etiquetas en las predicciones.
    - ```thickness```: grosor de la caja.
    - ```fontface```: tamaño de letra.
    - ```color```: (B, G, R) como en opencv.

## Uso de la función

```python
# Cargar el modelo, en este caso el YOLOv3 pero se puede poner cualquier modelo
from arcgis.learn import YOLOv3
model = YOLOv3()

model.predict_video(
    input_video_path=r"\path\to\video.mp4",
    metadata_file=r"\path\to\metadata\file.csv",
    visualize=True
)
```

Con personalización de las opciones visuales ```visual_options```:
```python
model.predict_video(
    input_video_path=r'\path\to\input_video.mp4', 
    metadata_file=r'\path\to\metadata\metedata_file.csv',
    track=True,
    output_file_path=r'\path\to\output\output_file.mp4',
    multiplex=True,
    multiplex_file_path=r'\path\to\output\multiplexed_file.mp4',
    tracker_options={
        'assignment_iou_thrd': 0.3, 
        'vanish_frames': 40, 
        'detect_frames': 10
        },
    visual_options={
        'show_scores': True, 
        'show_labels': True, 
        'thickness': 2, 
        'fontface': 0, 
        'color': (255, 255, 255)
        }
    )
```

[Ejemplo de uso en ArcGIS Notebook](https://preventas.maps.arcgis.com/home/item.html?id=8e3ed0db5503452e9737831a9a9b2dca)

