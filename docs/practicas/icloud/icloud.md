
# Práctica 4: iCloud y CloudKit

Seguimos trabajando con el proyecto `ToDo` y el _bundle ID_
`es.ua.mudsdm.ToDo` y el perfil de aprovisionamiento `Master
Moviles ToDo` (está actualizado e incluye el permiso de acceso
a CloudKit y al contenedor `iCloud.es.ua.mudsdm.ToDo`).

> Puedes optar por hacer el ejercicio sobre la versión de ToDo basada en _storyboards_ o la basada en _SwiftUI_. En caso de elegir esta segunda opción, se recomienda leer la sección de teoría sobre [SwiftUI](https://malozano.github.io/apuntes-spm-ios/teoria/swiftui/swiftui.html) como ayuda.

## 1. Antes de empezar la clase presencial ##

1. Antes de la clase presencial deberás mirar los dos vídeos con
   demostraciones que podrás encontrar en Moodle, en la sesión 4. Son
   muy útiles para entender los conceptos que se explican en más
   profundidad en teoría y para explicar las prácticas que hay que
   hacer en este tema.

2. Lee los apartados de teoría para entender el funcionamiento de
   iCloud clave-valor y los aspectos básicos de CloudKit.

## 2. Parte obligatoria (hasta 7 puntos) ##

- Actualiza en Xcode el permiso para utilizar iCloud, con clave-valor
  y con CloudKit.
- Modifica la app ToDo para que el número de ítems terminados se
  guarde en iCloud del usuario, usando iCloud clave-valor.
    

<img style="border: 1px solid;" src="imagenes/todo-clave-valor.jpeg" width="300px"/>


- Añade el código necesario para que las tareas pendientes
  se guarden y recuperen de la base de datos privada de
  CloudKit. 
  
    En el contenedor de CloudKit se ha añadido el tipo de registro
    `Tarea` con el campo `nombre` con los índices `Queryable`,
    `Searchable`, `Sortable`. 


### Pista para actualizar la tabla ###

- Los _callbacks_ en los que se reciben los resultados de las
  _queries_ son asíncronos y se procesan en hilos secundarios.
- Si actualizamos los datos de la tabla en un _callback_ de este tipo,
  la interfaz de usuario no se refrescará hasta que el usuario no
  interactúe con la tabla.
- Se puede forzar a ejecutar la actualización de los datos de la tabla en
  el hijo principal con este código en algún lugar del `ToDoTableViewController`:
  
```swift
DispatchQueue.main.async( execute: {
    self.tableView.reloadData()
})
```


## 3.  Parte opcional (3 puntos) ##


<img src="imagenes/todolist-cloudkit.png" width="300px"/>

- Utiliza la base de datos pública de CloudKit para publicar tareas compartidas
  por todos los usuarios de la app. Al añadir una tarea debes permitir
  la opción de hacerlo en la base de datos pública. Muestra el texto
  de las tareas públicas en un color diferente en el listado de
  tareas.
  
- Añade una funcionalidad en la que se recargue la
  tabla con los datos de iCloud cuando se tire de la tabla hacia
  abajo. 


## 4. Entrega ##

Entrega una carpeta comprimida con el proyecto y un pequeño documento
PDF en el que expliques las funcionalidades implementadas. 

Si el tamaño de la entrega supera los 20MB, sube la entrega a Google
Drive o similar e incluye un enlace.
