# Práctica 3: Notificaciones 

## 1. Antes de empezar la clase presencial

1. Antes de la clase presencial deberás mirar los tres vídeos con
   demostraciones que podrás encontrar en Moodle, en la sesión de Notificaciones. En caso de optar por realizar los ejercicios en su versión de SwiftUI, se recomienda también ver el vídeo sobre la aplicación ToDo con SwiftUI.

2. Una vez vistas estas demostraciones, debes leer los siguientes
apartados del tema de teoría:

    - [Introducción](https://malozano.github.io/apuntes-spm-ios/teoria/notificaciones/notificaciones.html#introduccion):
      Conceptos básicos de notificaciones en iOS. Notificaciones
      locales y remotas. NotificationCenter.

    - [Preparación de las notificaciones](https://malozano.github.io/apuntes-spm-ios/teoria/notificaciones/notificaciones.html#preparacion-de-las-notificaciones):
  Petición de permiso a los usuarios.
  
    - [Notificaciones
  locales y Demo](https://malozano.github.io/apuntes-spm-ios/teoria/notificaciones/notificaciones.html#notificaciones-locales_1):
  Creación de notificaciones. Demo con el app Notificaciones.

    - [Acciones, Manejo de notificaciones y
      Demo](https://malozano.github.io/apuntes-spm-ios/teoria/notificaciones/notificaciones.html#acciones):
      Acciones en las notificaciones. Manejadores de las notificaciones.

## 2. Parte obligatoria (hasta 7 puntos) ##

Descarga las apps [Notificaciones](https://github.com/malozano/apuntes-spm-ios/raw/master/apps/Notificaciones.zip)
y [NotificacionesPush](https://github.com/malozano/apuntes-spm-ios/raw/master/apps/NotificacionesPush.zip),
   revisa su código y sus permisos y pruébalas. La primera puedes probarla en el
   simulador. La segunda deberás probarla ejecutándola en un
   dispositivo real y enviando notificaciones remotas tal y como hemos
   hecho en la demo.

- Modifica la app ToDo para que genere notificaciones locales. En
  la app ToDo puedes hacerlo con un botón en la pantalla con el
  número de tareas terminadas. Deberás generar una notificación en el
  intervalo de 10 segundos que contenga alguna imagen y acciones. Y
  visualizar la acción que el usuario ha realizado sobre la
  notificación, lanzando una
  [alerta](https://developer.apple.com/reference/uikit/uialertcontroller)
  la siguiente vez que se abra la app que informe de la acción
  escogida.

> Puedes optar por hacer el ejercicio sobre la versión de ToDo basada en _storyboards_ o la basada en _SwiftUI_. En caso de elegir esta segunda opción, se recomienda leer la sección de teoría sobre [SwiftUI](https://malozano.github.io/apuntes-spm-ios/teoria/swiftui/swiftui.html) como ayuda.
  
## 3. Parte opcional (3 puntos) ##

Lee los siguientes apartados del tema de teoría:

- [Notificaciones
  remotas](https://malozano.github.io/apuntes-spm-ios/teoria/notificaciones/notificaciones.html#notificaciones-remotas-push):
  Arquitectura de las notificaciones remotas. Registro del
  dispositivo. Payload de la notificación.

- Ejercicio **para los que no tienen dispositivo físico iOS**: Implementa una notificación basada en el
  calendario, en la que dejes al usuario seleccionar la
  hora y minuto en la que aparezca una notificación informando del
  número de tareas terminadas.
  
- Ejercicio **para los que tienen dispositivo físico iOS**: Añade la
  posiblidad de añadir una nueva tarea en la lista de tareas
  pendientes mediante una notificación silenciosa enviada con una
  notificación push. Sigue primero el
  [ejercicio](https://malozano.github.io/apuntes-spm-ios/teoria/notificaciones/notificaciones.html#ejercicio)
  del tema para probar la aplicación NotificacionesPush y el envío de
  notificaciones desde el terminal con un JWS. Deberás utilizar un
  dispositivo real para realizar las pruebas.

## 4. Entrega ##

Firma la app resultante, exporta el fichero IPA y entrégalo en
Moodle, junto con el proyecto comprimido y un documento PDF con una
breve descripción de las funcionalidades añadidas.

