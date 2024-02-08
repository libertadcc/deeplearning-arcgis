# Mask R-CNN

La **segmentación de instancias de objetos** integra tareas de detección de objetos donde el objetivo es dectectar objetos dentro de una caja delimitadora y tareas de segmentación semántica que clasifica cada píxel en categorías predefinidas. De forma que podemos detectar objetos en una imagen a la vez que segmentamos una máscara para cada instancia de objeto. 

La segmentación de instancias nos permite hacer tareas como detectar daños conociendo su alcance, la generación de huellas de edificios...

## Arquitectura Mask R-CNN 
Mask R-CNN es un modelo de segmentación de instancias desarrollado a partir de Faster R-CNN. 
> Faster R-CNN es una red neuronal convolucional basada en regiones que devuelve cuadros delimitadores para cada objeto y su etiqueta con un grado de confianza. 

La arquitectura de **Faster R-CNN** trabaja en dos etapas:
1. El primer paso consiste en dos redes: backbone (ResNet, VGG, etc) y una red de propuestas regionales. Estas redes se ejecutan una vez por imagen para obtener un conjunto de propuestas de regiones que son regiones del mapa de características que contienen el objeto. 

2. En el segundo paso la red predice las cajas delimitadoras y la clase de objeto para cada una de las regiones propuestas en el paso 1. Cada región propuesta puede tener un tamaño diferente, mientras que las capas conectadas de las redes siempre requieren un vector de tamaño fijo para realizar predicciones. El tamaño de las regiones propuestas se fija utilizando el método RoI pool o RoIAlign. 

![Arquitectura Faster R-CNN](https://developers.arcgis.com/python/guide/base64-images/how_maskrcnn_works-g09b8c215-1700-d272-6b24-d331a5088b4d.png)

Faster R-CNN predice la clase de objeto y los cuadros delimitadores. **Mask R-CNN es una extensión de Faster R-CNN con una rama adicional para predecir máscaras de segmentación en cada región de interés (RoI).**

En el segundo paso de Faster R-CNN, RoI pool se sustituye por RoIAlign que ayuda a conservar la información espacial que se desalinea con RoI pool. RoIAlign utiliza la interpolación binaria para crear un mapa de características de tamaño fijo, p.e. 7x7. 

La salida de la capa RoIAlign se introduce en Mask head que consta de dos capas de convolución y genera una máscara para cada RoI, segmentando una imagen píxel a píxel. 

![Mask R-CNN pra la segmentación de instancias](https://developers.arcgis.com/python/guide/base64-images/how_maskrcnn_works-gdb244685-6dc7-af5f-6099-c6d45ab8cfc9.png)


## PointRend Enhancement
Los modelos de segmentación tienden a generar límites demasiado suaves lo que puede hacer que no sean precisos para los objetos. Para obtener un límite de segmentación nítido, se ha añadido al modelo un módulo de red neuronal de renderizado basado en puntos llamado **PointRend**. Este módulo aporta perspectiva de renderizado a la segmentación de objetos obteniendo así resultados de alta resolución de forma eficiente.

![Comparación entre el resultado de segmentación sin PointRend y con PointRend](https://developers.arcgis.com/python/guide/images/pointrend_maskrcnn.jpg)

Para usar esta mejora hay que definirla junto al modelo:
```python
model = MaskRCNN(data=data, pointrend=True)
```

# Modelos pre-entrenados del Living Atlas
Algunos modelos que usan este algoritmo son:
* [Building Footprint Extraction - USA](https://www.arcgis.com/home/item.html?id=a6857359a1cd44839781a4f113cd5934)
* [Solar Panel Detection - USA](https://www.arcgis.com/home/item.html?id=c2508d72f2614104bfcfd5ccf1429284)
* [Ship detection](https://www.arcgis.com/home/item.html?id=2fd653cd9de446ccbab34f69e9e70d81)

# Ejemplos con MaskRCNN
* [Detección de cráteres lunares](https://developers.arcgis.com/python/samples/lunar-craters-detection-from-dem-using-deep-learning/)