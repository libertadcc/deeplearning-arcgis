# PSPNet
PSPNet (Pyramid scene parsing network) es otro modelo de segmentación semática implementado en el módulo de *arcgis.learn* junto a U-Net que nos permite **clasificar píxeles**. 

El PSPNet es una mejora respecto a la segmentación basada en FCN (Fully Convolutional Network - redes totalmente convolucionales) porque tiene en cuenta el **contexto global de la imagen** en las predicciones locales. 

Se entiende mejor con el ejemplo:

![Ejemplo clasificación de píxeles con varios modelos](https://developers.arcgis.com/python/guide/base64-images/how_pspnet_works-gd9321451-4475-3a5c-9c21-044c0b096f9b.png)

En este caso, podemos ver como el FCN clasifica el barco que aparece en la imagen (a) con un recuadro amarillo como coche. Esto lo hace porque por forma y tamaño un coche y un barco son parecidos, pero si el modelo hubiera tenido en cuenta el contexto (el río) lo hubiera clasificado como barco como ha hecho PSPNet (d).

## Arquitectura Encoder-Decoder para la segmentación semántica

La mayoría de los modelos de segmentación semántica tienen dos partes, un Encoder y un Decoder. 
* Encoder: responsable de la extracción de elementos de la imagen.
* Decoder: el que predice la clase de cada píxel. 

![Arquitectura típica](https://developers.arcgis.com/python/guide/base64-images/how_pspnet_works-g83775eb3-9006-4971-cd15-57357436ef3d.png)

### PSPNet Encoder

![](https://developers.arcgis.com/python/guide/images/dilated.gif)