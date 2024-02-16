# Detección de objetos

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

### Modelos de detección de objetos
* [Single-Shot Detector](./SSD/Intro.md)
* [YOLOv3](./YOLOv3-imagenes/Imagenes.md)
* [Faster R-CNN](./FasterRCNN/Intro.md)
* [Mask R-CNN](./RCNN/Intro.md)
* [RetinaNet](./RetinaNet/Intro.md)
* MMDetection*

> *MMDetection no es un modelo de deep learning como tal sino que es un marco de código abierto para la detección de objetos que **puede usar diferentes modelos de deep learning** para ello como Faster R-CNN, SSD ...  es decir, modelos de una o varias pasadas.

