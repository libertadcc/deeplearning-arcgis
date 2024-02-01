# Deep learning y ArcGIS

# WIP 🚧

Este repositorio me sirve personalmente para ordenar las ideas y conceptos relacionados con el deep learning y su uso con la tecnología de ArcGIS. 

![](./YOLOv3-imagenes/images/schemaWIP.png)

## Clasificación de imágenes y detección de objetos
La **clasificación de imágenes** por ordenador coge una imagen y predice el objeto que contiene mientras que la **detección de objetos** predice el objeto y encuentra su ubicación en términos de cuadros delimitadores. Por ejemplo, un clasificador de piscinas nos dirá si en la imagen hay piscinas, mientras que un modelo de detección nos dirá si hay piscina y dónde está. De forma que un modelo de detección nos dirá:
- La probabilidad de que haya ese objeto
- La altura de la caja delimitadora
- El ancho de la caja delimitadora
- La coordenada horizontal del centro de la caja
- Y la coordenada vertical del centro de la caja

Esto es en términos generales, también dependerá del modelo y su implementación.

Podemos decir que, en la práctica, hay dos tipos de algoritmos de detección de objetos:
* Algoritmos como R-CNN y Fast(er) R-CNN que hacen el trabajo en dos pasos:
    1. Identifican las regiones en las que se espera encontrar objetos.
    2. Detectan los objetos solo en esas regiones utilizando convnet (redes convolucionales).

* Algoritmos de una pasada como YOLO (You Only Look Once) y SSD (Single-Shot Detector) que son capaces de encontrar todos los objetos de una sola pasada gracias al enfoque convolucional de su red. 

En comparación, los algoritmos de propuesta de región (primer grupo) suelen tener una precisión superior pero son más lentos en comparación con los de una única pasada que son bastante eficientes a pesar de hacer una única pasada. 


![Esquema para saber qué modelo de detección de objetos elegir](./assets/schema.png)


## Segmentación de objetos

Es una tarea en visión por computadora que implica la asignación de una etiqueta a cada píxel de una imagen para indicar a qué objeto pertenece. Mientras que la detección de objetos identifica la presencia de objetos en una imagen y los delimita con cajas delimitadoras. 

En resumen, mientras que la detección de objetos nos dice qué objetos están presentes y dónde se encuentran en la imagen, la segmentación de objetos nos proporciona información detallada sobre la ubicación exacta de cada píxel perteneciente a un objeto específico.

Existen dos tipos principales de segmentación de objetos:

- **Segmentación Semántica**: asigna una etiqueta a cada píxel para indicar a qué clase o categoría de objeto pertenece. Por ejemplo, en una imagen de calle puede haber píceles de la clas automóvil, peatón o edificio. 
- **Segmentación de instancias**: asigna una etiqueta a cada píxel y diferencia intanscias del mismo tipo de objeto. Por ejemplo, si hay dos coches en una imagen, la segmentación de instancias distingue qué píxeles pertenecen a cada uno. 

Algunos modelos y bibliotecas populares para la segmentación de objetos incluyen U-Net, SegNet, y MMSegmentation.

### Clasificación de píxeles

La segmentación semántica también se llama **clasificación de píxeles** y que consiste en la clasificación de cada píxel en determinadas categorías. Para hacer esta segmentación semántica hay dos inputs de información:
- Una imagen raster con varias bandas.
- Una imagen de etiqueta que contiene etiquetas para cada píxel. 

Hay diferentes algoritmos de segmentación semántica como U-net, Mask R-CNN, Feature Pyramid Network, etc. Uno de los más reconocidos en U-net. 


# Temas:
1. [Modelo YOLOv3 con imágenes](./YOLOv3-imagenes/Imagenes.md)
2. [Modelo YOLOv3 con vídeos](./YOLOv3-imagenes/Videos.md)
3. [Modelos SSD](./SSD/Intro.md)

# Usar modelos preentrenados

Lo ideal es tener tiempo y recursos para entrenar tus propios modelos porque será cuando se llegue a la mejor conclusión pero no es real, muchas veces tendremos que utilizar modelos pre-entrenados por otras personas. Un sitio del que podemos obtener modelos pre-entrenados es el Living Atlas donde hay más de [70 dlpks](https://livingatlas.arcgis.com/en/browse/?q=dlpk#q=dlpk&d=2) compartidos de distintas temáticas.




# Enlaces de interés
- [¡Redes Neuronales CONVOLUCIONALES! ¿Cómo funcionan?](https://www.youtube.com/watch?v=V8j1oENVz00&ab_channel=DotCSV)
- [¿Qué es una Red Neuronal?](https://www.youtube.com/watch?v=MRIv2IwFTPg&ab_channel=DotCSV)