# Compras In-App

## Conceptos de compras In-App ##

### ¿Qué es una compra In-App?

Permite vender directamente una funcionalidad dentro de una app.

Los datos de la compra (precio, identificador) se definen en iTunes Connect.

Se implementa con el [API StoreKit](https://developer.apple.com/documentation/storekit):

- StoreKit pregunta al usuario si confirma la transacción a través del
  acceso seguro del App Store.
- La app recibe la confirmación de la compra y debe desbloquear
  dinámicamente la funcionalidad.
- La app debe guardar la información de que el usuario ha comprado esa
  nueva funcionalidad, aunque el usuario siempre puede recuperar la
  compra.

<p style="text-align:center;">
<img src="imagenes/in-app-storekit.png" width="600px"/>
</p>


### Ejemplos de uso

Las compras In-App son una de las formas de monetización más usadas
en la actualidad

Por ejemplo:

- Podemos dar una versión básica gratuita y vender funcionalidades
  adicionales premium.
- Podemos permitir la suscripción a contenidos periódicos que se pueden descargar
    - Ofertas de niveles adicionales en juegos
    - Compras de mercancías virtuales en juegos on-line


### Tipos de compras In-App - Compras

- No-consumibles
    - Ítems que permanecen disponibles de forma indefinida en todos los dispositivos del usuario.
    - Ejemplos: libros, niveles de un juego, funcionalidades premium de un app.
- Consumibles
    - Ítems que se consumen durante el tipo de ejecución del app.
    - Ejemplos: minutos de llamadas de voz sobre IP, o servicios de un sólo uso como transcripción de voz.


### Tipos de compras In-App - Suscripciones

- Suscripciones auto-renovables
    - Como los no-consumibles, las suscripciones permanecen
      disponibles en todos los dispositivos. Tienen una fecha de
      expiración, en la que el sistema renueva automáticamente la
      compra.
- Suscripciones no-renovables
    - Suscripciones en las que no se entrega contenido periódico.
    - Ejemplos: acceso a una base de datos de fotos históricas.
    - Suele acompañarse de una cuenta de usuario en un servidor.
    - La duración y la expiración de la suscripción se realizan desde
      la app (y el servidor).


### Requisitos para activar las In-App

Las compras In-App sólo pueden probarse y activarse con una cuenta
de desarrollador de pago.

Es necesario acceso a iTunes Connect para configurar las compras.

No es posible hacerlo con el equipo de la universidad.

Haremos una demo con una cuenta de desarrollador.


### Contratos

**¡Cuidado!**: Para poder probar las compras In-App hay que tener
todos los contratos en regla.

<p style="text-align:center;">
<img src="imagenes/in-app-contratos.png" width="800px"/>
</p>


### Servicios a activar en la app

<p style="text-align:center;">
<img style="border: 1px solid;" src="imagenes/in-app-capabilities.png" width="700px"/>
</p>


### _Bundle identifier_ y App Id

<p style="text-align:center;">
<img src="imagenes/in-app-bundle-name.png" width="400px"/>
<img src="imagenes/in-app-app-id.png" width="600px"/>
</p>

### Configuración In-App desde iTunes Connect

<p style="text-align:center;">
<img src="imagenes/in-app-itunes-connect.png" width="700px"/>
</p>

### Datos del app

<p style="text-align:center;">
<img style="border: 1px solid;" src="imagenes/app-itunes-connect.png" width="800px"/>
</p>


### Pantalla para añadir nuevos In-Apps

<p style="text-align:center;">
<img style="border: 1px solid;" src="imagenes/producto-in-app.png" width="900px"/>
</p>


### Seleccionar el tipo de In-App

<p style="text-align:center;">
<img style="border: 1px solid;" src="imagenes/in-app-tipo.png" width="700px"/>
</p>


### Características del In-App

- Nombre de referencia: aparece en la ventana de compra
- ID del producto: identificador del In-App para reconocerlo en el app
- Precio
- Datos para la revisión de Apple


### Características del In-App

<p style="text-align:center;">
<img src="imagenes/ejemplo-in-app.png" width="700px"/>
</p>

<p style="text-align:center;">
<img src="imagenes/ejemplo-in-app-2.png" width="700px"/>
</p>


### Usuarios de prueba 

Para probar las compras In-App debemos crear usuarios de prueba de
sandbox en iTunes Connect.

En el dispositivo de prueba hay que iniciar la sesión en el App Store
con ese usuario de prueba.

<p style="text-align:center;">
<img style="border: 1px solid;" src="imagenes/in-app-usuario-prueba.png" width="700px"/> 
</p>

<p style="text-align:center;">
<img style="border: 1px solid;" src="imagenes/in-app-app-store.jpg" width="200px"/>
<img style="border: 1px solid;" src="imagenes/in-app-app-store-2.png" width="200px"/>
</p>


## Demo ##

Vamos a ver un ejemplo de aplicación que contiene una pantalla
sorpresa cuyo acceso se activa con una compra In-App.

Está disponible en [este
enlace](https://github.com/domingogallardo/apuntes-spm-ios/raw/master/apps/EjemploInApp.zip)

<p style="text-align:center;">
<img style="border: 1px solid;" src="imagenes/in-app-prueba-1.jpg" width="170px"/>
<img class="fragment" style="border: 1px solid;" src="imagenes/in-app-prueba-2.jpg" width="170px"/>
<img class="fragment" style="border: 1px solid;" src="imagenes/in-app-prueba-3.jpg" width="170px"/>
</p>

<p style="text-align:center;">
<img class="fragment" style="border: 1px solid;" src="imagenes/in-app-prueba-4.jpg" width="170px"/>
<img class="fragment" style="border: 1px solid;" src="imagenes/in-app-prueba-5.jpg" width="170px"/>
<img style="border: 1px solid;" src="imagenes/in-app-prueba-6.jpg" width="170px"/>
</p>


## Código para implementar las compras In-App ##

### El proceso de compra en un vistazo

Debemos implementar los protocolos
[`SKProductsRequestDelegate`](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKProductsRequestDelegate/)
y
[`SKPaymentTransactionObserver`](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKPaymentTransactionObserver_Protocol/).

<p style="text-align:center;">
<img src="imagenes/in-app-vistazo.png" width="700px"/>
</p>


### Clase auxiliar InApp

Creamos un protocolo `InAppDelegate` y una clase `InApp` donde
gestionaremos la interacción con `StoreKit`:

```swift
protocol InAppDelegate {
    func compraRecibida()
}

class InApp: NSObject, SKProductsRequestDelegate, SKPaymentTransactionObserver {
    var productDetailsList: [SKProduct] = []
    var productIdentiferList: [String] = []
    var delegate: InAppDelegate?
    
    override init() {
        super.init()
        SKPaymentQueue.defaultQueue().addTransactionObserver(self)
        // Cargamos la lista de productos
        productIdentiferList.append("ejemplo3")
        let request = SKProductsRequest.init(productIdentifiers: 
                            Set(productIdentiferList))
        request.delegate = self
        request.start()
    }


// Método del delegado al que se llama cuando se han recibido los productos

func productsRequest(request: SKProductsRequest, didReceiveResponse response: SKProductsResponse) {
    print("Hemos recibido \(response.products.count) productos")
    productDetailsList = response.products
    for invalidProductId in response.invalidProductIdentifiers {
        print("Producto invalido id: \(invalidProductId)")
    }
}

// Método para lanzar la petición de compra al usuario

func lanzarPago() {
    if (self.productDetailsList.count > 0 &&
              SKPaymentQueue.canMakePayments()) {
        let producto = productDetailsList[0]
        let pago = SKPayment(product: producto)
        SKPaymentQueue.defaultQueue().addPayment(pago)
        print("Comprando...")
    } else {
        print("No existen productos")
    }
}

// Método del delegado al que se llama cuando el usuario compra el InApp
func paymentQueue(queue: SKPaymentQueue, 
                  updatedTransactions transactions: 
                                    [SKPaymentTransaction]) {
    for transaction in transactions {
        switch transaction.transactionState {
        case .Purchased:
            print("Purchased")
            delegate?.compraRecibida()
            SKPaymentQueue.defaultQueue()
                            .finishTransaction(transaction)
        case .Failed:
            print("Failed")
            print("Error de transacción: \(transaction.error?.localizedDescription)")
            SKPaymentQueue.defaultQueue()
                            .finishTransaction(transaction)
        case .Restored:
            print("Restored")
            delegate?.compraRecibida()
            SKPaymentQueue.defaultQueue()
                           .finishTransaction(transaction)
        default:
            print("Otro")
        }
    }
}
```


### Clase ViewController

En la clase `ViewController` adoptamos nuestro protocolo
`InAppDelegate` y definimos su método `compraRecibida()` al que se va
a llamar cuando se haya recibido y validado la compra.

```swift
class ViewController: UIViewController, InAppDelegate {
    @IBOutlet weak var botonSorpresa: UIButton!
    let inApp = InApp() // Instancia de la clase auxiliar InApp

    override func viewDidLoad() {
        // Nos hacemos delegados de InApp
        inApp.delegate = self

        // Escondemos el botón que da acceso a la pantalla sorpresa
        botonSorpresa.hidden = true
    
        // Comprobamos si el usuario ha comprado antes el inApp
        // Funciona si el usuario está identificado 
        if NSUserDefaults.standardUserDefaults().boolForKey("inAppComprado") {
            botonSorpresa.hidden = false
        } else {
            botonSorpresa.hidden = true
        }
        super.viewDidLoad()
    }
```


La acción asociada al botón de compra llama al método `lanzarPago` de
la instancia de nuestra clase `InApp`:

```swift
    // Acción asociada al botón de compra
    @IBAction func hazCompra(sender: UIButton) {
        print("Click botón de compra")
        inApp.lanzarPago()
    }
```
    
En el método `compraRecibida()` (del protocolo) activamos un botón que
da acceso a la pantalla sorpresa.

```swift
    // Implementación del método del protocolo InAppDelegate
    func compraRecibida() {
        botonSorpresa.hidden = false
    }
```

Acción para mostrar la pantalla sorpresa:
    
```swift
    @IBAction func sorpresa(sender: UIButton){
        performSegueWithIdentifier("Sorpresa", sender: view)
    }
}
```


## Referencias

- [Página resumen Apple Developer](https://developer.apple.com/in-app-purchase/)
- [App Store Connect - In-App purchase](https://help.apple.com/app-store-connect/#/devb57be10e7)
- [In-App Purchase Programming Guide](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
- [Receipt Validation Programming Guide](https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Introduction.html#//apple_ref/doc/uid/TP40010573-CH105-SW1)
- [StoreKit Framework Reference](https://developer.apple.com/library/mac/documentation/StoreKit/Reference/StoreKit_Collection/)
- [Technical Note: Adding In-App Purchase to your iOS and OS X Applications](https://developer.apple.com/library/ios/technotes/tn2259/_index.html#//apple_ref/doc/uid/DTS40009578)
- [Technical Note: In-App Purchase Best Practices](https://developer.apple.com/library/ios/technotes/tn2387/_index.html)

