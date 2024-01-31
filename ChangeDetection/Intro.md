# Change Detection

La detección de cambios es primordial en los SIG donde obtenemos muchas imágenes de la misma localización pero de diferentes épocas. Con el modelo de detección de cambios podemos resolver problemas como la identificación de nuevas construcciones y cambios en la cobertura del suelo.

La detección binaria de cambios significa que la salida del modelo tendrá dos valores: cambio o ausencia de cambio. Por ejemplo, si queremos averiguar qué carreteras se han añadido en los últimos 5 años necesitamos pasar dos imágenes de un mismo punto en ese tiempo. 

## Arquitectura

La arquitectura implementada en *arcgis.learn* para la detección de cambios se basa en STANet paper. Nos permite entrenar imágenes acopladas con un mapa semántico de cambio como su objetivo. Por ejemplo, si queremos ver cambios en edificios, la etiqueta para el par de imágenes será el mapa de cambio de las huellas que ha aumentado o desaparecido. 

Esta arquitectura usa un mecanismo de autoatención en las activaciones de la capa fina de una red neuronal convolucional. La arquitectura base es una arquitectura tipo UNet con un codificador y un decodificador. El codificador suele ser una arquitectura basada en ResNet preentrenada por Imagenet, y el decodificador es una combinación de capas de remuestreo, convolución 1x1 y autoatención.

El avance a través de la red se realiza sobre imágenes en ambas líneas temporales. Una vez que recibe las características, pasa por el módulo de atención y recibimos mapas de características atendidas. A partir de ellas, se calcula una función de pérdida o error que indica que los modelos actualizaron sus parámetros. La salida de esta arquitectura es el mapa semántico del cambio en nuestra caracterísitca de interés. 

![Arquitectura de Red de STANet](https://developers.arcgis.com/python/guide/base64-images/how_change_detection_works-gf07c027a-5e62-5d54-35ae-8576293ac486.png)

> En la figura:
> - *I* representa la imagen
> - *X* las características del codificador
> - *Z* las características después de aplicar la atención.
> - El bloque del módulo métrico es la función de pérdida.
> - En *P* aparece destacado los nuevos edificios. 

**PAM vs BAM**
En el paper de STANet se proponen dos tipos de módulos de atención: PAM y BAM.  
* **PAM** (Pyramid spatial-temporal Attention Module): es una ampliación del módulo de atención básica.
* **BAM** (Basic spatial-temporal Attention Module): módulo que permite al modelo aprender las ubicaciones en el mapa de características a las que debe prestar atención.

![PAM vs BAM](https://developers.arcgis.com/python/guide/base64-images/how_change_detection_works-g61582f5c-0e1e-382d-0cba-5de88b88dece.png)

PAM es una versión más grande y mejorada del BAM ya que utiliza el módulo BAM en las diferentes resoluciones del mapa de características final. 

Con el módulo *arcgis.learn* se puede implementar este modelo de detección de cambios y se puede cambiar entre PAM y BAM cambiando el parámetro *attention_type*. El paper sugiera usar BAM para detectar grandes cambios y PAM para cambios más finos. 

Podemos entrenar un modelo de detección de cambios como se muestra en el [notebook](https://www.arcgis.com/home/item.html?id=d700b1713cbf404bad01d92dfca0c91b). Los datos exportados deben tener un formato específicos:
- Una carpeta llamada "images_before" con las imágenes de la línea de tiempo anterior.
- Una carpeta llamada "images_after" con las imágenes de la línea de tiempo posterior.
- Una "labels" con el mapa semántico de los cambios. 

La ruta raíz de todas ellas será lo que pasemos a la función *prepare_data* especificando que el conjunto de datos sea *ChangeDetection*.
```python
data = prepare_data(root_path,
                    chip_size=224,
                    dataset_type='ChangeDetection', 
                    batch_size=2
                   )
```

Podemos usar este conjunto de datos para inicializar la clase ChangeDetector.
```python
cd = ChangeDetector(data,
                    attention_type='BAM'   # 'PAM' is default.
                   )
```


# Recursos
- [Change Detection of Building from Satellite Imagery](https://developers.arcgis.com/python/samples/change-detection-of-buildings-from-satellite-imagery/)