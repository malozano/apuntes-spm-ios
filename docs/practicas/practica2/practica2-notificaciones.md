# Práctica 2: Notificaciones 

Descarga las apps [Notificaciones](https://github.com/domingogallardo/apuntes-spm-ios/raw/master/apps/Notificaciones.zip)
y [NotificacionesPush](https://github.com/domingogallardo/apuntes-spm-ios/raw/master/apps/NotificacionesPush.zip),
   revisa su código y sus permisos y pruébalas. La primera puedes probarla en el
   simulador. La segunda deberás probarla ejecutándola en un
   dispositivo real y enviando notificaciones remotas tal y como hemos
   hecho en la demo.

- Ejercicio 1: Modifica la app ToDoList para que genere
  notificaciones locales. En la app ToDoList puedes hacerlo con un
  botón en la pantalla con el número de tareas terminadas. Deberás
  generar una notificación en el intervalo de 10 segundos que contenga
  alguna imagen y acciones. Y visualizar la acción que el usuario ha
  realizado sobre la notificación, lanzando una
  [alerta](https://developer.apple.com/reference/uikit/uialertcontroller)
  la siguiente vez que se abra la app que informe de la acción
  escogida.
- Ejercicio 2 (para los que no tienen dispositivo físico iOS): Implementa una notificación basada en el
  calendario, en la que dejes al usuario seleccionar la
  hora y minuto en la que aparezca una notificación informando del
  número de tareas terminadas.
- Ejercicio 3 (para los que tienen dispositivo físico iOS): Añade la posiblidad de añadir una nueva
  tarea en la lista de tareas pendientes mediante una notificación
  silenciosa enviada con una notificación push. Utiliza el script PHP
  y [este certificado](https://github.com/domingogallardo/apuntes-spm-ios/raw/master/cert-todolist.pem) para generar la
  notificación. Deberás utilizar un dispositivo real para realizar las
  pruebas.

Firma la app resultante, exporta el fichero IPA y entrégalo en
Moodle, junto con el proyecto comprimido y un documento PDF con una
breve descripción de las funcionalidades añadidas.

