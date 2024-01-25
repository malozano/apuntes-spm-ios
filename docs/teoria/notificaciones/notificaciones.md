# Notificaciones 

En esta sesión veremos la forma de crear, enviar y recibir
notificaciones locales y remotas (_push_) usando el _User
Notifications Framework_ de iOS.

## Introducción ##

En iOS sólo una única aplicación puede estar activa en un momento
dado. Sin embargo, en muchas ocasiones las apps operan en un entorno
basado en el tiempo o interconectado en el que es necesario avisar al
usuario cuando sucede algún evento. 

Las **notificaciones locales y remotas** permiten a estas apps
notificar a sus usuarios cuando ocurre algún suceso de su interés.

Todo el API para gestionar las notificaciones se unifica en iOS 10 en
el [_User Notifications
Framework_](https://developer.apple.com/reference/usernotifications) y
en el [_User Notifications UI Framework
Reference_](https://developer.apple.com/reference/usernotificationsui).

Además de los usos comentados, las notificaciones se utilizan también
para la comunicación entre nuestra app y el recién introducido _Apple
Watch_. Se puede consultar la [página de
recursos](https://developer.apple.com/documentation/watchos-apps/notifications/) de Apple
sobre el _WatchOS Apps_ para más información.

<img src="imagenes/glances.png" />

En la sesión de hoy vamos a ver la última versión del API de
notificaciones, introducida en iOS 11.

### Apariencia de las notificaciones ###

Tanto las notificaciones locales como las remotas pueden aparecer
como:

- Un aviso (_alert_) o tira (_banner_) en la parte superior de la
pantalla. Los avisos obligan al usuario a realizar una
interacción, las tiras aparecen y desaparecen.

<img src="imagenes/banner.png" width="300px"/>

- Un globo (_badge_) en el icono de la app.

<img src="imagenes/badge.png" width="100px"/>

- Un sonido que acompaña la alerta, _banner_ o _badge_.


### Dónde aparecen las notificaciones ###

Dependiendo de si el dispositivo está en uso o bloqueado, las
notificaciones se comportan de distinta forma.

Si el dispositivo está en uso, las notificaciones aparece en la parte superior.

<img src="imagenes/alerta.png" width="300px"/> 

Si el dispositivo está bloqueado aparecen en la pantalla de bloqueo.

<img src="imagenes/notificacion-pantalla-bloqueo.png" width="300px"/> 

En cualquier caso se guardan en el **centro de notificaciones** (se abre deslizando hacia abajo desde
la parte superior de la pantalla).

<img src="imagenes/notificacion-centro-notificaciones.png" width="300px"/> 

El usuario puede configurar la aceptación de notificaciones y su
apariencia en los ajustes (_Ajustes > Notificaciones_).


### Interacción en las notificaciones ###

Cuando se recibe una notificación, el usuario puede ignorarla, y se
guarda en el centro de notificaciones.

O puede interactuar con ella, desplegándola y seleccionando una de las
opciones estándar (_Abrir_, _Borrar_ o _Ver_):

<img src="imagenes/acciones-notificaciones.png" width="600px"/>

- Con la opción _Abrir_ se pasa a primer plano la app a la que
  corresponde la notificación.
- Con la opción _Borrar_ se borra la notificación.
- Con la opción _Ver_ se muestra completamente la notificación y el
  usuario puede seleccionar una de las acciones incluidas en ella. Con
  el _User Notifications UI Framework_ es posible añadir imágenes,
  audio, vídeo e interfaces de usuario a las notificaciones y permitir
  al usuario interaccionar en la propia notificación sin abrir la app.

<img src="imagenes/notificaciones-ui.png" width="300px"/>


### Notificaciones locales ###

Las notificaciones remotas y locales satisfacen distintas
necesidades de diseño.

Una **notificación local** es planificada y enviada por la propia app,
cuando está en funcionamiento o cuando está en _background_ recogiendo
datos de un servidor y recibe alguna información interesante.

<img src="imagenes/notificacion-local.png" width="350px" />

Dos ejemplos de uso:

- En una app que gestiona una lista de tareas por hacer, en la que
  cada ítem tiene una fecha y hora en el que debe ser completado.

- En una app que recibe en _background_ información de cotizaciones en
  bolsa y en la que hay un cambio considerable en la cotización de una
  empresa que el usuario ha marcado.

### Notificaciones remotas ###

Una **notificación remota**, también llamada _notificación push_,
llega del exterior del dispositivo. Se origina en un servidor remoto
gestionado por el desarrollador de la app (denominado proveedor de
la aplicación) y se envía al dispositivo del usuario a través del
_Apple Push Notification service_ (APNs).
  
<img src="imagenes/notificacion-remota.png" width="350px" />

Dos ejemplos de uso:

- En una aplicación de mensajería (estilo WhatsApp) se notifica cuando
  el usuario recibe un nuevo mensaje.

- En un reproductor de podcasts el servidor avisa de que hay un nuevo
  episodio disponible para ser reproducido.

### Para qué se usan las notificaciones ###

Las **notificaciones locales** se usan principalmente para gestionar
alarmas, recordatorios y eventos de una forma sencilla, sin tener que
usar un API más complicada como el _EventKit Framework_ que conlleva
el uso de apps como Calendario, Alarmas o Recordatorios.

Las **notificaciones remotas** se utilizan para:

- Avisar al usuario de que han sucedido determinados eventos.
- Notificar a la app para que descargue contenido nuevo para que
  esté disponible la próxima vez que el usuario la utilice.

<img src="imagenes/notif-content-available.png" width="500px"/>


### Notification service app extensions ###

Las **_app extensions_** de tipo _notification service_ son
extensiones que permiten modificar el contenido de las notificaciones
remotas antes de ser entregadas al usuario.

Por ejemplo, se pueden usar para:

- Implementar encriptación _end-to-end_ de las notificaciones.
- Modificar el contenido de la notificación, adaptándolo a algún
  contexto modificado en la app.
- Descargar del servidor imágenes o media adicionales.

Las veremos más adelante, en la sesión dedicada a las extensiones.


### Ciclo de vida de la app ###

Si pulsamos en una notificación y la app está en segundo plano no
ejecutándose, la app vuelve a primer plano.

Veremos que al volver a primer plano la app se ejecuta una función de
_callback_ asociada a la notificación, por lo que podremos modificar
la interfaz de usuario para adecuarla a la notificación que ha pulsado
el usuario (por ejemplo, si se trata de una app como Twitter, ir al
tweet correspondiente a la notificación).

<img src="imagenes/high_level_flow.png" width="450px"/>

<strong>Estados del ciclo de vida de la app</strong>

<table>
  <tr>
    <td><strong><em>Estado</em></strong></td> 
    <td><strong><em>Descripción</em></strong></td>
  </tr>
  <tr>
  <td><strong>No corriendo</strong></td> 
    <td>La app no ha sido lanzada o fue terminada por el usuario o por el sistema.</td>
  </tr>
  <tr>
    <td><strong>Inactiva</strong></td> 
    <td>La app está corriendo en primer plano pero no está recibiendo eventos (puede estar ejecutando código, sin embargo). Una app permanece en este estado brevemente, mientras realiza una transición a otro estado.</td>
  </tr>
  <tr>
    <td><strong>Activa</strong></td> 
    <td>La app está corriendo en primer plano y recibiendo eventos.</td>
  </tr>
  <tr>
    <td><strong>Background</strong></td> 

    <td>La app está ejecutando código pero no es visible en
        pantalla. Cuando el usuario sale de una app, el sistema mueve
        la app al estado de <em>background</em> antes de
        suspenderla. En otros momentos, el sistema puede lanzar una
        aplicación en <em>background</em> (o despertar una app suspendida) y
        darle tiempo para manejar ciertas tareas específicas. Por
        ejemplo, el sistema puede despertar una app para que procese
        descargas en <em>background</em>, o responda a notificaciones
        remotas. Una app en estado <em>background</em> debe hacer el mínimo
        trabajo posible y devolver rápidamente el control al sistema.</td>

  </tr>
  <tr>
    <td><strong>Suspendida</strong></td> 
    <td>La app está en memoria pero no ejecuta código. El sistema suspende apps que están en <em>background</em> y no tienen tareas pendientes que completar. El sistema puede eliminar apps suspendidas en cualquier momento sin despertarlas, para hacer sitio para otras apps.</td>
  </tr>
</table>


### API de notificaciones  ###

A partir de iOS 10 se unifican todas las funciones en el framework
  [UserNotifications](https://developer.apple.com/reference/usernotifications).  

Las clases y protocolos de ese framework permiten:

- Mismo código para notificaciones locales y remotas
- Métodos delegados simplificados
- Mejor gestión de las notificaciones
- Opción para presentar la notificación en el app
- Planificación y manejo de notificaciones en extensiones


### `UNUserNotificationCenter` ###

La clase
[`UNUserNotificationCenter`](https://developer.apple.com/reference/usernotifications/unusernotificationcenter)
es la clase principal de UserNotifications Framework. Define un
_singleton_  que es el objeto encargado
de planificar y gestionar todo lo relacionado con notificaciones.

Este objeto es el que se debe utilizar para todos los siguientes
tipos de tareas:

- Pedir autorización para mostrar las notificaciones.
- Declarar los tipos de notificación y las acciones que soporta la app.
- Planificar el envío de notificaciones a la app.
- Gestionar notificaciones específicas de la app mostradas en el Centro de Notificaciones.
- Obtener los ajustes relacionados con notificaciones de la app.
- Contener un delegado en el que se define la función de _callback_ a
  la que el sistema llama cuando el usuario activa la notificación.

### Notifications UI Framework ###

Es posible también incluir en la notificación animaciones y gráficos dinámicos usando el nuevo _framework_
[Notifications UI Framework](https://developer.apple.com/reference/usernotificationsui).
No tenemos tiempo de verlo en clase, puedes consultar la sesión de WWDC 2016 [Advanced Notifications](https://developer.apple.com/videos/play/wwdc2016/708/).

<img src="imagenes/notification-ui-framework.png" width="300px"/>

## Preparación de las notificaciones ##

### Registro de los tipos de notificación ###

Las apps que usan notificaciones locales o remotas deben registrar los
tipos de notificaciones que intentan enviar al usuario. Lo normal es
hacerlo antes de que la aplicación termine de lanzarse, en el método
`application:didFinishLaunchingWithOptions:` del delegado de la
aplicación.

El usuario debe aceptar el tipo de notificación: globos, alertas o
sonidos. Inicialmente le aparecerá una alerta en el que permite
aceptar o rechazar todos los tipos.

Después en cualquier momento puede modificar esta aceptación en los
ajustes de la aplicación (_Ajustes > Notificaciones_).

Por ejemplo, en el siguiente código se solicita autorización para mostrar avisos, sonidos y
globos:

```swift
func application(_ application: UIApplication, 
                 didFinishLaunchingWithOptions launchOptions: 
                     [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
    UNUserNotificationCenter.current()
        .requestAuthorization(options: [.alert, .sound, .badge])
        { (granted, error) in print(granted)}
    return true
}
```


Se utiliza el método [`requestAuthorization(options:completionHandler:)`](https://developer.apple.com/reference/usernotifications/unusernotificationcenter/1649527-requestauthorization) del singleton `UserNotificationCenter` (accesible con el método de  tipo
  [`current()`](https://developer.apple.com/reference/usernotifications/unusernotificationcenter/1649510-current)).

Se pasa como parámetro los tipos de notificación que solicitamos usar
(tira, sonido o aviso) y un _completion handler_ que se ejecuta tras
la aceptación o negación de los servicios por parte del usuario.

La primera vez que la app solicita autorización, aparece un aviso y el
usuario puede aceptar o denegar la autorización.

<img src="imagenes/alerta-notificaciones.png" width="300px"/>

Después de la petición inicial, el sistema recuerda la respuesta del
usuario y la devuelve en cualquier nueva petición.


### Obtener los ajustes definidos por el usuario ###

El método
[`getNotificationSettings(completionHandler:)`](https://developer.apple.com/reference/usernotifications/unusernotificationcenter/1649524-getnotificationsettings)
pide al sistema los ajustes y ejecuta asíncronamente un _completion
handler_ que recibe un objeto `UNNotificationSettings` como
parámetro.

```swift
func applicationWillEnterForeground(_ application: UIApplication) {
    print("Voy a pedir los settigs")
    UNUserNotificationCenter.current().
        getNotificationSettings(completionHandler:
            {(settings: UNNotificationSettings) in
                if (settings.alertSetting == UNNotificationSetting.enabled) {
                    print("Alert enabled")
                } else {
                    print("Alert not enabled")
                }
                if (settings.badgeSetting == UNNotificationSetting.enabled) {
                    print("Badge enabled")
                } else {
                    print("Badge not enabled")
                }})
}
```


## Notificaciones locales ##

Las notificaciones locales son creadas por la propia app y el sistema
es responsable de lanzarlas en la fecha y hora planificada. La app no
tiene que estar en marcha para que esto suceda.

Con una notificación local se puede hacer lo mismo que con una remota:
mostrar alertas, ejecutar sonidos o añadir globos al icono del app.

Se usan principalmente en apps con conductas basadas en temporizadores
y en apps sencillas de calendarios o de listas de to-do. Una app que
está ejecutándose en background también puede planificar una
notificación para informar al usuario de que ha llegado un mensaje, un
chat o se ha actualizado algún estado.


### Creación de notificaciones ###

La creación de una nueva notificación se realiza con la
clase
  [`UNNotificationRequest`](https://developer.apple.com/reference/usernotifications/unnotificationrequest),
  indicando los siguientes elementos:

- Identificador (`identifier: String`) que identifica de forma única la
petición de notificación.
- Contenido de la notificación (`content: UNNotificationContent`)
- Condiciones que disparan la notificación (` trigger: UNNotificationTrigger?`)

<img src="imagenes/fases-notificacion.png" width="600px"/>


### Contenido de la notificación ###

El contenido de la notificación se define con la clase
[UNMutableNotificationContent](https://developer.apple.com/documentation/usernotifications/unmutablenotificationcontent). Podemos
actualizar sus propiedades para especificar:

- `title: String`: Breve descripción de la razón del aviso
- `subtitle: String`: Descripción secundaria 
- ` body: String`: El mensaje mostrado en el aviso 
- `badge: NSNumber?`: El número a mostrar en el globo de la app
- `sound: UNNotificationSound?`: El sonido cuando se entrega la notificación 
- `launchImageName: String`: El nombre de la imagen de lanzamiento
   a mostrar cuando la app se lanza en respuesta a la notificación
- `var userInfo: [AnyHashable : Any]`: Un diccionario de información asociada con la notificación
- `var attachments: [UNNotificationAttachment]`: Un array de adjuntos a mostrar con la notificación

Por ejemplo, la siguiente notificación:

<img src="imagenes/atributos-notificacion.png" width="500px"/>

se especifica con el siguiente código:

```swift
let content = UNMutableNotificationContent()
content.title = "Introduction to Notifications"
content.subtitle = "Session 707"
content.body = "Woah! These new notifications look amazing! Don’t you agree?"
content.badge = 1
```


### Media attachments  ###

Es posible adjuntar a una notificación imágenes, vídeo o
audio. Los _attachments_ deben ser ficheros en el disco y el formato del
fichero debe ser uno de los tipos soportados:

- Audio: MP3, MPEG4
- Imagen: JPEG, GIF, PNG
- Vídeo: MPEG, MPEG2, MPEG4, AVI

Se debe crear un objeto de tipo
  [`UNNotificationAttachment`](https://developer.apple.com/reference/usernotifications/unnotificationattachment).
  
<img src="imagenes/attachment-notificacion.png" width="600px"/>

El siguiente código presenta una extensión de `UNNotificationAttachment` que permite crear un _attachment_ de tipo imagen a partir de una `UIImage`:

```swift
extension UNNotificationAttachment {
    static func create(identifier: String, image: UIImage, options: [NSObject : AnyObject]?) -> UNNotificationAttachment? {
        let fileManager = FileManager.default
        let tmpSubFolderName = ProcessInfo.processInfo.globallyUniqueString
        let tmpSubFolderURL = URL(fileURLWithPath: NSTemporaryDirectory()).appendingPathComponent(tmpSubFolderName, isDirectory: true)
        do {
            try fileManager.createDirectory(at: tmpSubFolderURL, withIntermediateDirectories: true, attributes: nil)
            let imageFileIdentifier = identifier+".png"
            let fileURL = tmpSubFolderURL.appendingPathComponent(imageFileIdentifier)
            guard let imageData = UIImagePNGRepresentation(image) else {
                return nil
            }
            try imageData.write(to: fileURL)
            let imageAttachment = try UNNotificationAttachment.init(identifier: imageFileIdentifier, url: fileURL, options: options)
            return imageAttachment
        } catch {
            print("error " + error.localizedDescription)
        }
        return nil
    }
}
```


Una vez definida la extensión, podemos incluir el _attachment_ en la notificación:

```swift
if let attachment = UNNotificationAttachment.
                           create(identifier: "prueba",
                                  image: UIImage(named: "gatito.png")!,
                                  options: nil) {
   content.attachments = [attachment]
}
```

### Condiciones de disparo de la notificación ###

Otro de los elementos que hay que indicar en una notificación local
son las condiciones de disparo.

<img src="imagenes/triggers.png" width="500px" />

- **Intervalo de tiempo**: dentro determinado intervalo de
  tiempo ("dentro de 2 minutos a partir de ahora"). Repeticiones en un intervalo determinado ("repite la
  notificación cada hora a partir de ahora").
- **Calendario**: en una determinada fecha ("a las 8:00 del día 20 de
  abril") y también periódicos ("cada lunes a las 18:00").
- **Localización**: cuando entre o salga de una determinada región
  geográfica ("cuando salga de casa" o "cuando llegue al supermercado").

Para codificar las condiciones de disparo debemos usar una de las
subclases de la clase abstracta
[`UNNotificationTrigger`](https://developer.apple.com/documentation/usernotifications/unnotificationtrigger):

- [`UNTimeIntervalNotificationTrigger`](https://developer.apple.com/documentation/usernotifications/untimeintervalnotificationtrigger): Para entregar una
  notificación local en cierto momento relativo a al momento
  actual. Se debe especificar el número de segundos que debe pasar
  antes de que la notificación se lance. También se puede definir
  un intervalo de repetición.

    ```swift
    // Fire in 30 minutes (60 seconds times 30)
    let trigger = UNTimeIntervalNotificationTrigger(timeInterval: (30*60), repeats: false)
    ```

- [`UNCalendarNotificationTrigger`](https://developer.apple.com/documentation/usernotifications/uncalendarnotificationtrigger): Para especificar una fecha y
  hora concreta en la que lanzar una notificación. Por ejemplo,
  para crear un _trigger_ que lanza notificaciones todas las
  mañanas a las 8:30:
      
      ```swift
      let date = DateComponents()
      date.hour = 8
      date.minute = 30 
      let trigger = UNCalendarNotificationTrigger(dateMatching: date, repeats: true)
      ```
    
- [`UNLocationNotificationTrigger`](https://developer.apple.com/documentation/usernotifications/unlocationnotificationtrigger): Para entregar una notificación
  cuando el dispositivo entra o abandona una zona geográfica
  específica.

      ```swift
      let center = CLLocationCoordinate2D(latitude: 37.335400, longitude: -122.009201)
      let region = CLCircularRegion(center: center, radius: 2000.0, identifier: "Headquarters")
      region.notifyOnEntry = true
      region.notifyOnExit = false
      let trigger = UNLocationNotificationTrigger(region: region, repeats: false)
      ```


### Creación de la notificación local ###

Una vez definido el **contenido** y las **condiciones de disparo** se puede
crear la notificación local usando el método `add` del centro de
notificaciones compartido:

```swift
let content = UNMutableNotificationContent()
content.title = "Introducción a Notificaciones"
content.body = "Hablemos sobre notificaciones!"
content.sound = UNNotificationSound.default()
let trigger = UNTimeIntervalNotificationTrigger(timeInterval: 10, repeats: false)
let requestIdentifier = "peticionEjemplo"
let request = UNNotificationRequest(identifier: requestIdentifier,
                                    content: content,
                                    trigger: trigger)
UNUserNotificationCenter.current().add(request) {
            (error) in print ("Error \(error)")}
```



----

## Demo

Vamos a probar el código visto hasta ahora en una app ejemplo.

### Ejemplo de app: `Notificaciones` ###

Descargamos la app
[Notificaciones](https://github.com/domingogallardo/apuntes-spm-ios/raw/master/apps/Notificaciones.zip)
en la que podemos lanzar notificaciones locales.

Las notificaciones locales se pueden probar en el simulador.

<img src="imagenes/app-notificaciones-1.png" width="280px"/>

<img src="imagenes/app-notificaciones-2.png" width="280px"/>

<img src="imagenes/app-notificaciones-3.png" width="280px"/>

- Mostramos el funcionamiento de la app y los mensajes que aparecen
  por la salida estándar.
- Mostramos el código del registro de notificaciones en el método
  `application(_:didFinishLaunchingWithOptions:)` de la clase `AppDelegate`.
- Mostramos el código de registro y creación de la notificación en el mismo
  método.
- Mostramos el código de los métodos manejadores del ciclo de vida de
  la app y el de obtención de los _settings_ de notificaciones.

----

## Acciones ##

<img src="imagenes/acciones-notificacion.png" width="300px"/>

Es posible incorporar en la notificación distintos tipos de
acciones:

- Botones con títulos customizables
- Entrada de texto

Las acciones se crean con la clase [`UNNotificationAction`](https://developer.apple.com/reference/usernotifications/untextinputnotificationaction).

Para conseguir una entrada de texto hay que crear un objeto de tipo
[`UNTextInputNotificationAction`](https://developer.apple.com/reference/usernotifications/untextinputnotificationaction).

El conjunto de acciones de una notificación deben agruparse en una categoría
[`UNNotificationCategory`](https://developer.apple.com/reference/usernotifications/unnotificationcategory)
que se registra en el `UNUserNotificationCenter` asociada a un
identificador. Una vez creada la categoría con el conjunto de acciones
podemos crear una notificación que contenga estas acciones
inicializando la propiedad `categoryIdentifier` de la notificación con
la cadena apropiada.

La acción en la que el usuario pulsa en la notificación se denomina
**acción por defecto**, y es la cadena: `com.apple.UNNotificationDefaultActionIdentifier`.

### Ejemplo de código de creación de una acción###

Para crear las acciones y asociarlas a una categoría:

```swift
let action1 = UNNotificationAction(identifier:"acepto", 
                                   title: "Acepto", options: [])
let action2 = UNNotificationAction(identifier:"otro", 
                                   title: "Otro día", options: [])
// Acción con entrada de texto
let action3 = UNTextInputNotificationAction(identifier: "mensaje", 
                                   title: "Mensaje", options: [],
                                   textInputButtonTitle: "Enviar",
                                   textInputPlaceholder: "Comentario")
let category = UNNotificationCategory(identifier: "invitacion", 
                                   actions: [action1, action2, action3], 
                                   intentIdentifiers: [], options: [])
UNUserNotificationCenter.current().setNotificationCategories([category])
```

Para asignar las acciones a la notificación se asigna el identificador
de la categoría.

```swift
content.categoryIdentifier = "invitacion"
```

La notificación creada tiene el siguiente aspecto:

<img src="imagenes/notificaciones-acciones.png" width="300px"/>
<img src="imagenes/acciones-input-texto.png" width="300px"/>


## Manejo de notificaciones ##

Una vez que el usuario ha pulsado una acción de la notificación o la
ha abierto nuestra app debe gestionar esa acción. Es lo que se
denomina _manejo de la notificación_. Para ello debemos implementar
los métodos del protocolo
[`UNUserNotificationCenterDelegate`](https://developer.apple.com/reference/usernotifications/unusernotificationcenterdelegate).

- `userNotificationCenter(_:didReceive:withCompletionHandler:)`: se
llama cuando la app está en segundo plano y el usuario pulsa
la notificación.

- `userNotificationCenter(_:willPresent:withCompletionHandler:)`: se
llama cuando la app está en primer plano y se recibe la
notificación. 

Estos métodos se suelen implementar en el propio `AppDelegate`, que
cumple este protocolo. Y se debe asignar al centro de notificaciones en los
métodos `application(_:willFinishLaunchingWithOptions:)` o
`application(_:didFinishLaunchingWithOptions:)`. Por ejemplo:


```swift
func application(_ application: UIApplication, 
                 didFinishLaunchingWithOptions launchOptions: 
                       [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
    ...
    UNUserNotificationCenter.current().delegate = self
    ...
}
```


### App en segundo plano ###

Cuando la app está en segundo plano (en background) las
notificaciones se reciben y muestran en el sistema.

El usuario puede pulsar en la notificación o interactuar con las acciones
de la notificación y pulsar una de ellas. En ambos casos se llama al
método mencionado anteriormente [`userNotificationCenter(_:didReceive
response:withCompletionHandler:)`](https://developer.apple.com/documentation/usernotifications/unusernotificationcenterdelegate/1649501-usernotificationcenter)
pasando en la variable `response` la información de la opción
seleccionada por el usuario. Esta variable es del tipo
[`UNNotificationResponse`](https://developer.apple.com/documentation/usernotifications/unnotificationresponse)
y en el atributo `actionIdentifier` lleva una cadena con la acción
seleccionada por el usuario. Si el usuario ha pulsado directamente la
notificación para abrir la app, la cadena es
`com.apple.UNNotificationDefaultActionIdentifier`.

Si el usuario ha escrito un mensaje en la acción de la notificación,
la respuesta que llega es del tipo `UNTextInputNotificationResponse` y
podemos acceder al texto del usuario en su atributo `userText`.

Podemos recuperar información completa contenida en la notificación
(un objeto de tipo `UNNotificationRequest`) accediendo al atributo
`notification.request` de la respuesta recibida. Si se ha añadido
información asociada a la notificación la podemos obtener en el
atributo `userInfo` (`response.notification.request.content.userInfo`).

Por último, también podemos acceder al view controller raíz de la
aplicación para modificar algún elemento de la interfaz de usuario
relacionada con la notificación que ha pulsado el usuario (ver el
ejemplo al final del código).

```swift
func userNotificationCenter(_ center: UNUserNotificationCenter, 
                            didReceive response: UNNotificationResponse, 
                            withCompletionHandler completionHandler: @escaping () -> Void) {
    print("En userNotificationCenter didReceive response")
    if let textInput = response as? UNTextInputNotificationResponse {
        print("Repuesta del usuario: \(textInput.userText)")
    } else {
        print("Acción escogida: \(response.actionIdentifier)")
    }
    let userInfo = response.notification.request.content.userInfo
    let mensaje = userInfo["Mensaje"] as! String
    print("Mensaje: \(mensaje)")
        
    // Actualizamos la variables de estado relacionada con la notificación
    // y modificamos la interfaz de usuario accediendo al rootViewController
        
    vecesPulsadaNotificacion += 1
    let viewController = self.window?.rootViewController as! ViewController
    viewController.actualiza(numVecesPulsadaNotificacion: vecesPulsadaNotificacion)
    completionHandler()
}
```

Si el usuario ha seleccionado una acción la aplicación no pasa a
primer plano. Sin embargo, si el usuario ha decidido abrir la
notificación, la aplicación pasa a primer plano, ejecutándose el
método de ciclo de vida
[`applicationWillEnterForeground`](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623076-applicationwillenterforeground)
del `UIApplicationDelegate`.

Si la notificación recibida es una **notificación remota** se llama al
método `application(_:didReceiveRemoteNotification:fetchCompletionHandler:)`
del `UIApplicationDelegate` (lo veremos más adelante).


### App en primer plano ###

Para trabajar con la notificación cuando la app está en primer plano
se define en el protocolo la función
`userNotificationCenter(_:willPresent:withCompletionHandler:)`.

El sistema llama a esta función cuando se recibe una notificación y la
app está en primer plano.

Por defecto, la notificación no se muestra al usuario. Si queremos que
la notificación aparezca debemos llamar al _completionHandler_ pasando
como parámetro un array con las opciones de visualización que
deseamos.

Un ejemplo de código:

```swift
func userNotificationCenter(_ center: UNUserNotificationCenter,
                            willPresent notification: UNNotification,
                            withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
    print("En userNotificationCenter willPresent notification")
    let userInfo = notification.request.content.userInfo
    let mensaje = userInfo["Mensaje"] as! String
    print("Mensaje: \(mensaje)")
    completionHandler([.alert, .sound])
}
```

En el ejemplo anterior, al pasar al _completionHandler_ un array que
contiene `.alert`la notificación se mostrará al usuario. El usuario
podrá interactuar con ella de la forma que hemos visto anteriormente.

----

## Demo

Probamos y examinamos el código de la aplicación de prueba que incluye
acciones en la notificación y que define las funciones de gestión de
las notificaciones.

<img src="imagenes/acciones-input-texto.png" width="300px"/>

Probamos a seleccionar distintas acciones y comprobar qué mensaje
aparece por la salida estándar:

<img src="imagenes/salida-notificaciones.png" width="400px"/>


----

## Notificaciones remotas (_push_) ##


### Objetivos de las notificaciones remotas ###

Una notificación remota permite enviar información interesante
relacionada con la app directamente al usuario. Para ello la app debe
contar con un servicio que será el responsable de enviar esa
información.

Por ejemplo, una app que sea un periódico puede tener un servicio que
envíe una notificación al usuario cuando sucede una noticia relevante.

También es posible enviar una notificación invisible que llega a la
app para que descargue nueva información en _background_ y la
muestre instantáneamente la siguiente vez que el usuario acceda a la
app. 

El envío de notificaciones se hace a través del servicio de Apple APNs
(_Apple Push Notification service_).

<img src="imagenes/server-side-notification.png" width="230px"/>
<img src="imagenes/server-side-silent-notification.png" width="215px"/>


### Arquitectura de las notificaciones remotas ###


El servicio Apple Push Notification service (APNs) es la pieza central
de las notificaciones remotas. Es un servicio robusto y altamente
eficiente para propagar información a dispositivos iOS y OS X.

<img src="imagenes/apns-server.png" width="700px"/>


Cada dispositivo establece una conexión acreditada y encriptada con
el servicio y recibe notificaciones sobre esta conexión persistente.

Si llega una notificación para una app cuando el dispositivo está
fuera de cobertura, el APNs guarda la notificación hasta que el
dispositivo vuelve a estar disponible.

Las notificaciones se originan en servidores (_proveedores_) propios
del desarrollador. Los proveedores se conectan con el APNs y reciben
datos de sus apps clientes. Cuando llegan nuevos datos para un app,
los proveedores preparan y envían notificaciones a través de los
canales al APNs, que se encarga de enviarlas a los dispositivos.


### Arquitectura de seguridad ###

No queremos que nuestras notificaciones (con datos personales)
puedan aparecer en otros dispositivos.

<img src="imagenes/remote-notif-multiple.png" width="400px"/>

El servicio de notificaciones remota de Apple (APNs) define unas
condiciones de seguridad bastante estrictas tanto entre dispositivo
y servicio como entre proveedor (nuestro servidor y el servicio.

- Seguridad en la **conexión Proveedor-APNs**
    - Basada en JWT (JSON web tokens) o basada en un certificado.
- Seguridad en la **conexión APNs-Dispositivo**
    - Basada en un _token de dispositivo_ (único para cada dispositivo
      y encriptado con su clave privada) que envía el APNs al
      dispositivo y que debe estar presente en cada petición del
      proveedor al APNs.


### Servidores proveedores ###

Las notificaciones remotas se deben originar en un servidor
proveedor nuestro que debe conectarse con el APNs usando la API
definida por Apple basada en un protocolo HTTP/2 y TLS.

Es posible montar un servidor propio usando librerías ya
existentes. Por ejemplo, en Java existe la librería
[Pushy](https://github.com/relayrides/pushy). Es recomendable
consultar la documentación de Apple [Setting Up a Remote Notification
Server](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server)
y [Sending Notification Requests to APNs](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns).

La mayoría de servicios PaaS proporcionan conexiones con el APNs y librerías que facilitan el envío de notificaciones:

- [Firebase Cloud Messaging for iOS](https://developers.google.com/cloud-messaging/ios/start?ver=swift)
- [Amazon Web Services](http://docs.aws.amazon.com/sns/latest/dg/mobile-push-apns.html)
- [Microsoft Azure](https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-ios-get-started/)

Una opción sencilla, que usaremos en la práctica, es lanzar la
notificación [desde el terminal](https://developer.apple.com/documentation/usernotifications/sending_push_notifications_using_command-line_tools).

En cualquier caso la conexión al APNs debe estar encriptada: o bien un certificado proporcionado por Apple o bien un token, usando JWT (JSON
Web Token). Esto último es lo que haremos en la demostración.

- [Establishing a Certificate-Based Connection to APNs](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/establishing_a_certificate-based_connection_to_apns)
- [Establishing a Token-Based Connection to APNs](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/establishing_a_token-based_connection_to_apns)

Para la demostración y la práctica enviaremos la notificación al APNs
usando el terminal y una autenticación con JWT. Cada notificación se
enviará junto con un JWT firmado con una clave privada generada en el
portal de desarrollo.


### Secuencia de registro del dispositivo ###

Sin considerar aspectos de seguridad y codificación, los pasos que
se siguen al registrarse un dispositivo con el método de la clase
`Application`
[registerForRemoteNotifications()](https://developer.apple.com/reference/uikit/uiapplication/1623078-registerforremotenotifications)
son los siguientes:

<img src="imagenes/registration-sequence.png" width="500px"/>

1. El dispositivo establece una conexión SSL con el APNs.
2. El APNs le envía un _token_ único asociado con el dispositivo.
3. El dispositivo le envía el _token_ al app.
4. El app envía el _token_ a su servidor (_Provider_) para que lo
   utilice a partir de ese momento en cada petición de notificación
   realizada al APNs.


### Token del dispositivo ###

Cada dispositivo iOS tiene un certificado y una clave privada
criptográfica, proporcionada por el sistema operativo en la
activación inicial y almacenada en el llavero del dispositivo.

Este certificado sirve para establecer una conexión segura basada en
TLS con el APNs. Con la conexión TLS activa, las apps en el dispositivo pueden
registrarse con APNs para recibir un token específico para recibir
notificaciones remotas.

<img src="imagenes/token-generation.png" width="400px"/>

El APNs genera el token, que contiene la información del dispositivo,
lo encripta utilizando una clave asociada al token y lo envía al
dispositivo. El sistema entrega el token encriptado a la app,
llamando al método del delegado
[`application:didRegisterForRemoteNotificationsWithDeviceToken:`](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1622958-application).

Una vez recibido el token, el app debe enviarlo al proveedor (en
formato binario o hexadecimal) para que lo utilice para enviar
notificaciones al dispositivo.

Cuando el servidor envía una petición de notificación al APNs,
se debe incluir el token del dispositivo.

El APNs desencripta el token para asegurarse de la validez de la
petición y determina el dispositivo de destino.

Si el APNs determina que el emisor y el receptor son legítimos,
envía la notificación al dispositivo identificado.


<img src="imagenes/token-trust.png" width="500px"/>


### Contenido de la notificación ###

Una vez definido el mecanismo de seguridad en el envío de las
notificaciones, veamos cómo se define el **contenido** de la
notificación.

El mensaje enviado al APNs se denomina _payload_ y debe cumplir unas
condiciones estrictas definidas en la
[documentación de
Apple](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification). El
tamaño máximo está limitado a 4096 bytes y debe tener el formato de un objeto JSON diccionario (parejas clave,
valor).

Un ejemplo en JSON:

```json
{
    "aps" : {
        "alert" : {
            "title" : "Introducción a las notificaciones",
            "subtitle" : "Sesión 707",
            "body" : "La nueva API de notificaciones es genial!!!",
            "category" : "mensaje",
        },
        "badge" : 1
    },
}
```


El diccionario debe contener otro diccionario identificado por la
clave `aps`. Este diccionario contiene una o más propiedades que
especifican los siguientes tipos de notificación:
    - Mensaje de alerta a mostrar al usuario
    - Numero a añadir en el globo del icono de la app
    - Sonido a tocar

El diccionario `aps` también puede tener la clave `content-available`
con un valor de 1. Eso significa que la notificación será una
notificación silenciosa que hará que el sistema despierte la app y la
ponga en _background_ para que pueda conectarse al servidor o hacer
alguna tarea de background. En este caso es conveniente no mostrar al
usuario ninguna notificación. El contenido nuevo se verá la siguiente
vez que se abra la app.

El resto del diccionario contendrá parejas clave-valor con
información _custom_.

La información JSON se convierte en un diccionario que se pasa como
parámetro `userInfo` en el método
[`didReceiveRemoteNotification`](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623013-application)
del delegado del app.

Otros ejemplos de _payload_.

Número en el _badge_ y _custom keys_:

```json
{
    "aps" : {
        "alert" : "You got your emails.",
        "badge" : 9,
        "sound" : "bingbong.aiff"
    },
    "acme1" : "bar",
    "acme2" : 42
}
```

Notificación silenciosa:

```json
{
    "aps" : {
        "content-available" : 1
    },
    "acme1" : "bar",
    "acme2" : 42
}
```

Notificación con cadenas localizadas:

```json
{
    "aps" : {
        "alert" : {
            "loc-key" : "GAME_PLAY_REQUEST_FORMAT",
            "loc-args" : [ "Jenna", "Frank"]
        },
        "sound" : "chime.aiff"
    },
    "acme" : "foo"
}
```

Notificación con acciones:

```json
{
   "aps" : {
      "alert” : {
         “body” : "Acme message received from Johnny Appleseed”,
         “action-loc-key” : “VIEW”,
         "actions" : [
            {
               “id" : “delete",
               "title" : "Delete"
            },
            {
               “id" : “reply-to”,
               "loc-key" : “REPLYTO”,
               "loc-args" : [“Jane"]
            }
         ]
      }
      "badge" : 3,
      "sound" : “chime.aiff"
   },
   "acme-account" : "jane.appleseed@apple.com",
   "acme-message" : "message123456"
}
```



## Gestión de las notificaciones remotas en la app ##

### Capacidad de notificación remota ###

La app debe tener el permiso de usar las notificaciones remotas. Debe
usar un perfil de aprovisionamiento con un App ID que otorgue ese
permiso. 

Se puede hacer desde Xcode o desde el la web de desarrollador. En la
demostración lo haremos desde la web del programa de desarrollo de la
universidad. 

<img src="imagenes/push-capability.png" width="700px"/>


### Registro de las notificaciones ###

Para que una app trabaje con notificaciones remotas lo primero que
debe hacerse, al igual que con las notificaciones locales, es pedir
permiso al usuario. La forma de hacerlo es idéntica a las de las
notificaciones locales, usando el método
[`requestAuthorization(options:completionHandler:)`](https://developer.apple.com/reference/usernotifications/unusernotificationcenter/1649527-requestauthorization). 

Una vez hecho esto, hay que conseguir el token asociado al dispositivo
y la app registrándose en el Apple Push Notification service
(APNs). Lo hace el método
[`registerForRemoteNotifications`](https://developer.apple.com/documentation/uikit/uiapplication/1623078-registerforremotenotifications)
del objeto application. 

Se trata de un método asíncrono. Si el registro en el servicio tiene
éxito, la app llama al método
[`application(_:didRegisterForRemoteNotificationsWithDeviceToken:)`](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1622958-application)
del delegado de la aplicación pasando el token asignado que habrá que
incluir en las notificaciones que enviemos al dispositivo.

Ejemplo de código de registro de las notificaciones remotas:

```swift
func application(_ application: UIApplication, 
                 didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
    UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge])
    { (granted, error) in print(granted)}
    application.registerForRemoteNotifications()
    UNUserNotificationCenter.current().delegate = self
    return true
}
```

Ejemplo de obtención del token e impresión en la consola:

```swift
func application(_ application: UIApplication, 
                 didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
    var token = ""
    for i in 0..<deviceToken.count {
        token = token + String(format: "%02.2hhx", arguments: [deviceToken[i]])
    }
    print(token)
}
    
func application(_ application: UIApplication, 
                 didFailToRegisterForRemoteNotificationsWithError error: Error) {
    print("Failed to register:", error)
}
```

### Recepción de las notificaciones en la app ###

La gestión de las notificaciones recibidas es idéntica a la ya vista en
notificaciones locales, usándose exactamente los mismos manejadores,
con la excepción de la posibilidad de gestionar notificaciones remotas
cuando la app está en segundo plano.

Recordemos los manejadores que ya vimos en las notificaciones locales:

- Si la app está en primer plano se llama al metodo
  `userNotificationCenter(willPresent:withCompletionHandler:)` del
  `UNUserNotificationCenterDelegate` cuando llega la notificación.
- Si la app está en segundo plano y el usuario pulsa en la
  notificación o en una de sus acciones se llama a
  `userNotificationCenter(_:didReceive:withCompletionHandler:)` del
  `UNUserNotificationCenterDelegate`. 

La diferencia de las notificaciones remotas es la posibilidad de
definir el manejador
[`application(_:didReceiveRemoteNotification:fetchCompletionHandler:)`](https://developer.apple.com/documentation/uikit/uiapplicationdelegate/1623013-application)
del delegado del app, que se activa cuando llega una notificación
remota que contiene la clave `content-available` con el valor `1`:

```
{
   "aps" : {
      "content-available" : 1
   },
   "acme1" : "bar",
   "acme2" : 42
}
```


Para que se llame al método se debe activar la capability `Background Modes >
Remote Notifications` en Xcode.

<img src="imagenes/background-modes.png" width="700px"/>

El método se llama tanto si la app está en primer plano como si está
en segundo plano. De hecho, es recomendable usarlo únicamente para
gestionar _notificaciones silenciosas_ **que no se muestran al usuario**
sino que se usan para que la app pueda recuperar información del
servidor que se mostrará la siguiente vez que el usuario lance la app.

Por ello habría que enviar un _payload_ como el que hemos visto
anteriormente. Con _custom keys_ pero en el que el diccionario `aps`
no contenga ninguna clave que disparare una interacción con el
usuario.

### Ejemplo de app ###

Ejemplo de app que usaremos para demostrar las notificaciones remotas:

<img src="imagenes/notificaciones-tab-controller.png" width="300px"/>

Código de gestión de la notificación cuando la **app está en primer plano**:


```swift
func userNotificationCenter(_ center: UNUserNotificationCenter, 
                         willPresent notification: UNNotification, 
                         withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
    print("Recibida notificación primer plano")
    let userInfo = notification.request.content.userInfo
    createNewNewsItem(text: message(userInfo: userInfo) +
                      " (userNotificationCenter willPresent)")
    // No mostramos la notificación
    completionHandler([])
}
```


Código de gestión de la notificación cuando ha sido **accionada por el usuario**:

```swift
func userNotificationCenter(_ center: UNUserNotificationCenter, 
                        didReceive response: UNNotificationResponse, 
                        withCompletionHandler completionHandler: @escaping () -> Void) {
    print("Usuario ha pulsado una notificación")
    let userInfo = response.notification.request.content.userInfo
    createNewNewsItem(text: message(userInfo: userInfo) + " (userNotificationCenter didReceive)")
    completionHandler()
}
```


Código de gestión de una **notificación silenciosa**:

```swift
func application(_ application: UIApplication, 
                 didReceiveRemoteNotification userInfo: [AnyHashable : Any], 
                 fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
    print("Recibida notificación remota en background")
    createNewNewsItem(text: message(userInfo: userInfo) +
                      " (UIApplication didReceiveRemoteNotification)")
    completionHandler(UIBackgroundFetchResult.newData)
}
```

La función `message(userInfo:)` devuelve un `String` con el mensaje
contenido en la notificación:

```swift
func message(userInfo: [AnyHashable : Any]) -> String {
    // Buscamos el mensaje en las claves custom
    if let message = userInfo["mensaje"] as? String {
        return message
    } else {
        // Cogemos como mensaje el valor o el título de la alerta
        let aps = userInfo["aps"] as! [String: AnyObject]
        if let message = aps["alert"] as? String {
            return message
        } else if let dic = aps["alert"] as? [String: AnyObject] {
            return dic["title"] as! String
        } else {
            return ""
        }
    }
}
```

----

## Ejercicio ##

### Pasos necesarios para el ejercicio ###

En la demo vamos a mostrar cómo se envían y reciben notificaciones
remotas. Ejecutaremos una app que va a recibir las notificaciones
(NotificacionesPush) en un dispositivo real.

Podremos enviar notificaciones a este dispositivo real
utilizando un script que tendremos que configurar con:

- JWT codificado que contiene el _Team Id_ y el _Key ID_ proporcionado
  por Apple. Para la codificación necesitaremos una clave de
  autenticación también proporcionada por Apple con un fichero de
  texto con la extensión `.p8`.
- _Bundle ID_ incluido en un AppID con el permiso de notificaciones
  push.
- Token del dispositivo.


### Nuevo App ID en el _member center_ (admin) ###

Un administrador del equipo UA debe crear una App ID con el nombre
explícito de la app que se va a poner en producción y con la
autorización para las notificaciones push.

<img src="imagenes/app-id-notificaciones-1.png" width="700px"/>


### Creación de la clave de encriptación y la Key ID de Apple (admin)

Para poder encriptar el token JWT y poder usarlo para enviar la
notificación es necesario crear en el portal de desarrollo de Apple
una clave de encriptación para acceder al Servicio de Notificaciones
Push de Apple (APNs). Estos pasos solo los puede realizar un
administrador del equipo de la universidad.

Primero se pulsa en la opción de añadir una nueva clave:

<img src="imagenes/key-notificacion-push.png" width="600px"/>

Y después se crea la clave, proporcionando un nombre de
identificación (en nuestro caso `Notificaciones Push Master Moviles`):

<img src="imagenes/key-notificacion-push-2.png" width="700px"/>


Y por último confirmamos la nueva clave pulsando el botón `Register`:

<img src="imagenes/key-notificacion-push-3.png" width="600px"/>

Una vez confirmada la clave, podemos descargarnos el fichero con la
clave privada (solo una vez) y se crea un identificador de clave (_Key
ID_) que se guarda en el portal del desarrollador y podremos consultar
cuando sea necesario.

<img src="imagenes/key-notificacion-push-4.png" width="700px"/>

El fichero que se descarga es un fichero de texto con la extensión
`.p8` que contiene la clave privada con la que podremos encriptar el
token JWT. Lo debes guardar en un lugar seguro, porque solo puedes
descargarlo una vez.

Una vez creada la clave, podrás consultar su información en el
apartado `Keys` del portal de desarrollador:

<img src="imagenes/key-notificacion-push-5.png" width="600px"/>


### Probamos la app `NotificacionesPush` y obtenemos el token del dispositivo

Necesitamos obtener el token del dispositivo y de la app que va a
recibir la notificación. Para ello debemos ejecutar la app en un
dispositivo físico (no funciona en el simulador).

Descargamos el proyecto NotificacionesPush desde
[este
enlace](https://github.com/domingogallardo/apuntes-spm-ios/raw/master/apps/NotificacionesPush.zip). Contiene
la app y los scripts para enviar las notificaciones al APNs. 

La app debe estar firmada con el perfil de aprovisionamiento creado
y deben estar configuradas las _capabilities_ para activar las
notificaciones push:

<img src="imagenes/push-notifications-capabilities-xcode.png" width="900px"/>

Ejecutamos el app en un dispositivo físico en el que recibiremos las
notificaciones remotas, ya que éstas no funcionan en el simulador.

Anotamos el token del dispositivo que aparece en la consola al
ejecutar la app por primera vez.

<img src="imagenes/token.png" width="800px"/>


### Probamos a enviar notificaciones remotas al dispositivo ###

En el directorio `Scripts` del fichero descargado se encuentra el
fichero `.p8` con la clave privada y los scripts `encode_jwt.sh` y
`send_push_notification.sh`.

1. Nos aseguramos de que el script `encode_jwt.sh` incluye:
    - el _Team ID_ del equipo de la UA, disponible en el portal del desarrollador 
    - el _Key ID_, disponible también en el portal del desarrollador
      (lo puede consultar el profesor, administrador del equipo de la UA)
    - el nombre del fichero `.p8` incluido en el directorio

2. Ejecutamos el comando `encode_jwt.sh` y obtenemos el JWT
   codificado:
   
```
% ./encode_jwt.sh
eyAiYWxnIjogIkVTMjUwIhwgImtpZCI6ICJXM0g2WjlBNFI0IiB9.
eyAiaXNzIjogIjNTOTUyQUdINDYiLCAiaWF0IjogMTY0ODA1ODc2N
SB9.MEACIAvaPw6dP6d7ljQatmO5HsxK0J9NDVpd_xFBc-N4acLAAiAL
Eo7pNb1k4_awSDicbjsz7dC1LkiGRgMgYXbh8Os13z
```

3. Este JWT tiene una duración de 1 hora. Si pasa ese tiempo deberemos
   volver a ejecutar el comando para generar otro.
   
4. Copiamos el JWT y lo incluimos en el fichero
   `send_push_notification.sh`. También debemos incluir el bundle ID
   del AppID (`es.ua.mastermoviles.NotificacionesPush`) y el token del dispositivo.

5. La notificación que se envía es una notificación con una
   alerta. Podemos cambiar el _payload_ de la notificación para probar
   distintas configuraciones.

6. Llamamos al script para lanzar una notificación remota al dispositivo:

```
% ./send_push_notification.sh 

*   Trying 17.188.168.147:443...
* Connected to api.sandbox.push.apple.com (17.188.168.147) port 443 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* successfully set certificate verify locations:
*  CAfile: /etc/ssl/cert.pem
*  CApath: none
* (304) (OUT), TLS handshake, Client hello (1):
* (304) (IN), TLS handshake, Server hello (2):
* (304) (IN), TLS handshake, Unknown (8):
* (304) (IN), TLS handshake, Request CERT (13):
* (304) (IN), TLS handshake, Certificate (11):
* (304) (IN), TLS handshake, CERT verify (15):
* (304) (IN), TLS handshake, Finished (20):
* (304) (OUT), TLS handshake, Certificate (11):
* (304) (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / AEAD-CHACHA20-POLY1305-SHA256
* ALPN, server accepted to use h2
* Server certificate:
*  subject: CN=api.development.push.apple.com; OU=management:idms.group.533599; O=Apple Inc.; ST=California; C=US
*  start date: Dec 10 00:29:46 2021 GMT
*  expire date: Jan  9 00:29:45 2023 GMT
*  subjectAltName: host "api.sandbox.push.apple.com" matched cert's "api.sandbox.push.apple.com"
*  issuer: CN=Apple Public Server RSA CA 12 - G1; O=Apple Inc.; ST=California; C=US
*  SSL certificate verify ok.
* Using HTTP2, server supports multiplexing
* Connection state changed (HTTP/2 confirmed)
* Copying HTTP/2 data in stream buffer to connection buffer after upgrade: len=0
* Using Stream ID: 1 (easy handle 0x15700e600)
> POST /3/device/a7aece1640f3e3b787f3f88c33523eb1231fcd27ebe3c22170c472b02447af38 HTTP/2
> Host: api.sandbox.push.apple.com
> user-agent: curl/7.79.1
> accept: */*
> authorization: bearer eyAiYWxnIjogIkVTMjU2IiwgImtpZCI6ICJXM0g2WjlBNFI0IiB9.eyAiaXNzIjogIjNTOTUyQUdINDYiLCAiaWF0IjogMTY0ODA1ODc2NSB9.MEQCIAvaPw6dP6d7ljQatmO5HsxK0J9NDVpd_xFBc-N4acLAAiALEo7pNb1k4_awSDicbjsz7dC1LkiGRgMgYXbh8Of13w
> apns-topic: es.ua.mastermoviles.NotificacionesPush
> content-length: 198
> content-type: application/x-www-form-urlencoded
> 
* We are completely uploaded and fine
* Connection state changed (MAX_CONCURRENT_STREAMS == 1)!
* Connection state changed (MAX_CONCURRENT_STREAMS == 1000)!
< HTTP/2 200 
< apns-id: 07C0F2E2-7CE0-71EC-D0D6-E7089220F778
< 
* Connection #0 to host api.sandbox.push.apple.com left intact
```

Y la notificación debe llegar correctamente al dispositivo:

<img style="margin-left:20px" src="imagenes/notificacion-device.png" width="400px"/>

### Notificación silenciosa ###

Para enviar una notificación silenciosa (no se muestra al usuario,
pero llega a la app) hay que añadir la cabecera `apns-push-type:
background` y construir un payload que tenga el atributo
`content-available` a 1. En el script están comentadas las dos líneas
que habría que añadir:

```bash
curl -v \
  --http2 \
  --header "authorization: bearer $JWT" \
  --header "apns-topic: ${BUNDLEID}" \
  --header "apns-push-type: background" \
  --data '{"aps" : {"content-available" : 1}, "mensaje" : "Holaaaa"}' \
  "${URL}"
```


## Bibliografía

- [Framework UserNotifications](https://developer.apple.com/documentation/usernotifications)
- [Asking Permission to Use Notifications](https://developer.apple.com/documentation/usernotifications/asking_permission_to_use_notifications)
- [Scheduling a Notification Locally from Your App](https://developer.apple.com/documentation/usernotifications/scheduling_a_notification_locally_from_your_app)
- [Handling Notifications and Notification-Related Actions](https://developer.apple.com/documentation/usernotifications/handling_notifications_and_notification-related_actions)
- [Registering Your App with APNs](https://developer.apple.com/documentation/usernotifications/registering_your_app_with_apns)
- [Generating a Remote
  Notification](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
- [Pushing Updates to Your App Silently](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/pushing_updates_to_your_app_silently)
- [Setting Up a Remote Notification Server](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server)
- [Establishing a Certificate-Based Connection to APNs](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/establishing_a_certificate-based_connection_to_apns)
- [Establishing a Token-Based Connection to APNs](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/establishing_a_token-based_connection_to_apns)
