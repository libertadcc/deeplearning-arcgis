# Clasificación de píxeles

La **segmentación de objetos** es una tarea en visión por ordenador que implica la asignación de una etiqueta a cada píxel de una imagen para indicar a qué objeto pertenece. Mientras que la detección de objetos identifica la presencia de objetos en una imagen y los delimita con cajas delimitadoras. 

En resumen, mientras que la detección de objetos nos dice qué objetos están presentes y dónde se encuentran en la imagen, la segmentación de objetos nos proporciona información detallada sobre la ubicación exacta de cada píxel perteneciente a un objeto específico.

Existen dos tipos principales de segmentación de objetos:

- **Segmentación semántica**: asigna una etiqueta a cada píxel para indicar a qué clase o categoría de objeto pertenece. Por ejemplo, en una imagen de calle puede haber píceles de la clas automóvil, peatón o edificio. 
- **Segmentación de instancias**: asigna una etiqueta a cada píxel y diferencia intancias del mismo tipo de objeto. Por ejemplo, si hay dos coches en una imagen, la segmentación de instancias distingue qué píxeles pertenecen a cada uno. 

Por ejemplo, si tenemos una imagen de una calle donde aparecen personas, coches y carreteras, la *segmentación semántica* asignará una etiqueta con una categoría (persona, coche, carretera) a cada píxel mientras que la *segmentación de instancias* clasificará los píxeles y asignará identificadores únicos a cada instancia individual de objeto. 

> Algunos modelos y bibliotecas populares para la segmentación de objetos incluyen U-Net, SegNet y MMSegmentation.


La **segmentación semántica también se llama clasificación de píxeles** y que consiste en la clasificación de cada píxel en determinadas categorías. Para hacer esta segmentación semántica hay dos inputs de información:
- Una imagen raster con varias bandas.
- Una imagen de etiqueta que contiene etiquetas para cada píxel. 

Hay diferentes algoritmos de segmentación semántica como U-net, Mask R-CNN, Feature Pyramid Network, etc. Uno de los más reconocidos en U-net. 

### Modelos de clasificación de píxeles
* [U-Net](./U-Net/Intro.md)
* [PSPNet](./PSPNet/Intro.md)
