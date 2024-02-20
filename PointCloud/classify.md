# Clasificación de nubes de puntos usando PointCNN

## Introducción 
Las mediciones de puntos suelen recoilarse mediante sensores LiDAR y una vez procesados se denominan *nubes de puntos*. Las **nubes de puntos** son grandes colecciones de puntos de elevación en 3D (incluyen x, y, z) junto a atributos como marcas de tiempo GPS, intensidad y número de retornos de un pulso. Tras su post-procesamiento, los datos LiDAR pueden atribuirse a bandas RGB o a la clasificación de puntos (a cada punto es asignado una clasificación que define el tipo de objeto). Las categorías en las que se clasifican son muy variadas como: suelo, edificios, árboles, pasos elevados, agua, etc. Estas claes se definen mediante códigos numéricos enteros. Todos esos datos de las nubes de puntos se almacenan normalmente en un archivo estándar: LAS. 

Es en la clasificación de puntos donde los modelos de deep learning aportan valor, ya no es necesaria la clasificación manual o semiasistida de nubes de puntos. Para esto se suelen usar redes PointCNN. 

Las **redes PointCNN** emulan las redes neuronales de convolución tradicionales y son una generalización de las redes convolucionales que se utilizar para extraer objetos de las imágenes. La diferencia es que las PointCNN incorporan la capacidad de aprendender de puntos de naturaleza irregular y desordenada, característica típica de las nubes de puntos. Eso es gracias a **X-Conv** que es una red neuronal convolucional diseñada para el el procesamiento de nubes de punto en tareas de visión por ordenador. 

> En PointCNN, las operaciones convolucionales tradicionales no se pueden aplicar directamente a los datos de nube de puntos debido a su naturaleza no estructurada y la falta de una topología definida. Por lo tanto, PointCNN introduce X-Conv como una forma de realizar convoluciones sobre datos de nube de puntos.



## Clasificación de nubes de puntos
La clasificación de nubes de puntos es una tarea en la que a cada punto de la nube de puntos se le asigna una etiqueta, que representa una entidad del mundo real. Es diferente de la categorización de nubes de puntos, en la que se asigna una etiqueta a todo el conjunto de datos de nubes de puntos.

Igual que hacen las redes neuronales convolucionales tradicionales, el proceso de clasificación de nubes de puntos implica un entrenamiento de la red con un conjunto de nubes de puntos ya clasificados en el que cada punto tiene un código de clase único. Estos código se utilizan para representar las características que queremos que reconozca la red neuronal y es importante que la red aprenda a diferenciar los puntos que nos interesan de los que no y clasificar solo aquellos de interés. Mejorará el modelo los **atributos adicionales** presentes en los datos de entrenamiento como la intensidad, RGB, número de retornos, etc. *Aunque estos atributos extra mejoren la precisión del modelo, puede afectar negativamente si esos parámetros no están en los datos sobre los que se va a aplicar el modelo.*

> Se recomienda usar una porción etiquetada de los datos (sobre los que vamos a hacer la clasificación) o utilizar un modelo más generalizado construido con los atributos más comunes como puede ser un modelo entrenado para diferenciar elementos solo con la ayuda de los valores x, y, z. 

Cuando se entrena un modelo PointCNN, las nubes de puntos en bruto se convierten en bloques de puntos que contienen un número específico de puntos y, a continuación, esos bloques pasan al modelo para su entrenamiento junto a las etiquetas. 

## Operación X-Conv 
La X-Conv es la operación análoga a la de convolución en las CNN. Básicamente consiste en operaciones sobre los bloques de nubes de puntos procesados como el muestreo y la normalización mediante K-Nearest Neighbors.

![Comparativa entre CNN y X-Conv](https://www.researchgate.net/profile/Daniele-Mari-3/publication/358526565/figure/fig8/AS:1123382917644294@1644846767861/PointCNN-76-ch-Conv-operator.ppm)

En este esquema se pueden ver las diferencias entre una CNN (arriba) y PointCNN (debajo). 
* En el proceso de **CNN** podemos ver cuadrículas regulares donde las convoluciones se aplican recursivamente en parques de cuadrículas locales. Este proceso implica reducciones de rejilla/celdas mientras se incrementa el número de canales (grosor del punto). 
* En las nubes de puntos, **X-Conv** se aplica recursivamente para agregar información a partir de los puntos vecinos menos representativos (9--> 5 --> 2) pero con información más rica. 

## Arquitectura de clasificación de PointCNN
La rede PointCNN tienen una arquitectura similar a la de U-Net ya que también se utiliza el paradigma de **encoder-decoder** donde el encoder reduce el número de puntos mientras que aumenta el número de canales y el decoder aumenta el número de puntos y reduce progresivamente el número de canales. Esta red también usa "conexiones de salto" como en U-Net. 

En resumen, PointCNN difiere de las CNN convencionales basadas en rejilla principalmente por la aplicación de capas X-Conv. Aun así, el proceso general es similar a cómo se utilizan las CNN en los marcos de convolución basados en rejilla. Las principales diferencias son con respecto a:
- La forma en la que se extraen las regiones locales.
- La forma en la que aprende información de las regiones locales. 

# Implementación con arcgis.learn
Cuando se entrena un modelo PointCNN con el módulo *arcgis.learn*, el conjunto de datos de nubes de puntos en bruto en archivos LAS se convierte primero en bloques de puntos, que contienen un número específico de puntos junto con sus códigos de clase.

```python
output_path=r'path/to/export.pctd'
data = prepare_data(output_path, dataset_type='PointCloud', batch_size=2)
pointcnn = PointCNN(data)
pointcnn.fit(20)
```

Una vez entrenado el modelo PointCNN,  el método `compute_precision_recall()` se usa para calcular las métricas por clase (precisión, recall, f1-score) con respecto a los datos de validación. Y con el método `save()` podemos guardar el modelo. 

> [Información más detallada](https://developers.arcgis.com/python/guide/point-cloud-segmentation-using-pointcnn/#for-advanced-users)