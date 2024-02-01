# U-Net

U-Net fue inventada por primera vez para la segmentación de imágenes biomédicas. Su arquitectura puede definirse como una red codificadora seguida de una red decodificadora. 

- El **codificador** suele tratarse de una red de clasificación preentrenada, como VGG/ResNet, en la que se aplican bloques de convolución seguidos de un muestreo descendente maxpool para codificar la imagen de entrada en representaciones de características a varios niveles. 
> La operación de Max Pooling (MaxPooling) es una técnica de downsampling utilizada en redes neuronales convolucionales para reducir la dimensión espacial de las representaciones características, lo que ayuda a disminuir la cantidad de parámetros y a mejorar la eficiencia computacional.

- El **descodificador** proyecta semánticamente las características discriminativas (de menor resolución) aprendidas por el codificador sobre el espacio de píxeles (de mayor resolución) para obtener una clasificación densa. El descodificador consiste en un muestreo ascendente y una concatenación. 

![Arquitectura U-Net](https://developers.arcgis.com/python/guide/images/unet.png)

