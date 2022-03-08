<!--
Terminan la práctica en unas 1,5 horas + 1 hora de explicación = 2,5 horas
-->


# Práctica 2: Mapas y localización

## 1. Antes de empezar la clase presencial

1. Antes de la clase presencial deberás mirar los tres vídeos con demostraciones que podrás encontrar en Moodle, en la sesión 2.

2. Una vez vistas estas demostraciones, debes leer los siguientes
apartados del tema de teoría:

    - [Aspectos básicos de
      MapKit](https://domingogallardo.github.io/apuntes-spm-ios/teoria/mapas-localizacion/mapas-localizacion.html#aspectos-basicos-de-mapkit):
      desde _Aspectos básicos de MapKit_ hasta _Uso del delegado_
      (incluido). Explicación de la creación de mapas en una app y del
      uso del delegado para procesar las acciones del usuario sobre el
      mapa. 

    - [Anotaciones](https://domingogallardo.github.io/apuntes-spm-ios/teoria/mapas-localizacion/mapas-localizacion.html#anotaciones):
      desde _Anotaciones_ hasta _Elementos en el callout_
      (incluido). Explicación de la creación y uso de
      anotaciones. Con estos apartados y los anteriores puedes hacer
      la parte obligatoria de la práctica.

    - [Geocoding](https://domingogallardo.github.io/apuntes-spm-ios/teoria/mapas-localizacion/mapas-localizacion.html#geocoding):
      desde _Geocoding_ hasta _Conversión de placemarks en
      localizaciones_ (incluido). Explicación de la técnica de
      _geocoding_ (obtención del nombre a partir de las coordenadas
      geográficas). Este apartado se usan para la primera parte
      opcional de la práctica.

    - [Localización](https://domingogallardo.github.io/apuntes-spm-ios/teoria/mapas-localizacion/mapas-localizacion.html#localizacion):
      explicación de cómo activar y acceder a los servicios de
      localización. Necesario para la tercera parte opcional de la práctica.


## 2. Parte obligatoria (hasta 7 puntos) ##

1. Debes crear la app `es.ua.mastermoviles.Mapas`.

2. Empieza por definir un _View Controller_ en el que debes incluir un
mapa centrado inicialmente en Alicante. Inclúyelo en un _Navigation Controller_ con la
opción _Editor > Embed In > Navigation Controller_.

3. Añade en el centro de la barra del _Navigation Controller_ un
_Segmented Control_ con los valores `Mapa` y `Satélite`, conéctalos
con el _ViewController_ y haz que el mapa cambie de tipo cuando se
pulse en el control.

<p style="text-align:center;">
<img src="imagenes/practica-1.png" width="300px"/>
</p>

4. En el _Storyboard_ añade un `Bar Button Item` en la parte derecha de
la barra de navegación.

5. Llámalo `Pin` y enlázalo con una acción en el `ViewController` que
  añada una anotación en el mapa.

<p style="text-align:center;">
<img src="imagenes/practica-2.png" width="300px"/>
</p>

6. Añade en los _callouts_ imágenes _thumbnails_ predefinidas,
dependiendo de si el número de pin es par o impar.

7. Añade en los _callouts_ el botón de información.

<p style="text-align:center;">
<img src="imagenes/practica-3.png" width="300px"/>
</p>

## 3. Parte opcional (3 puntos) ##

**Parte opcional 1**

1. Implementa un _segue_ que haga aparecer otra vista con un detalle
  de la foto. Puedes definir un segue haciendo control click desde un
 view controller hasta otro. Después debes dar un identificador al
  segue. Por ejemplo `DetalleImagen`.

<p style="text-align:center;">
<img src="imagenes/storyboard.png" width="500px"/> 
<img src="imagenes/practica-4.png" width="200px"/>
</p>


**Parte opcional 2**

2. Implementa una llamada al servicio de geolocalización que coloque
   como subtítulo del Pin el país en el que se ha colocado el
   mismo. 

<p style="text-align:center;">
<img src="imagenes/mapa-con-pais.png" width="300px"/>
</p>

**Parte opcional 3**

3. Añade el tracking de localización a la aplicación, imprimiendo la
localización en la salida estándar cada 10 metros. Comprueba el
funcionamiento activando la localización en el simulador.

4. Añade la localización al mapa, haciendo que aparezca en la parte
izquierda de la barra de navegación el botón de navegación.

    - Cuando pulses el botón de navegación se debe mostrar la posición
      actual del dispositivo.

    - Deberás modificar la función que muestra la vista de una
      anotación
      ([`mapView(_:viewFor:)`](https://developer.apple.com/reference/mapkit/mkmapviewdelegate/1452045-mapview))
      porque se utiliza también para mostrar la vista de la posición
      actual del dispositivo (que también es una anotación). Si la
      anotación que se quiere mostrar no es de tipo Pin debes poner la
      vista a `nil` para que se use la vista por defecto (el círculo).
      
<p style="text-align:center;">
<img src="imagenes/localizacion.png" width="300px"/>
</p>


## 4. Entrega ##

Entrega una carpeta comprimida con el proyecto y un pequeño documento
PDF en el que expliques las funcionalidades implementadas. 

Si el tamaño de la entrega supera los 20MB, sube la entrega a Google
Drive o similar e incluye un enlace.

