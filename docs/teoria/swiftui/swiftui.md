# SwiftUI 

La arquitectura de una aplicación basada en SwiftUI presenta diferencias significativas respecto a una basada en Storyboards. Esto nos obliga a reorganizar los componentes utilizados para la gestión del modelo y de los servicios a los que accede nuestra aplicación. En esta sesión extra nos centraremos en estudiar los mecanismos que nos ofrece SwiftUI para gestionar estos objetos.

## Objetos observables

En la sesión introductoria sobre SwiftUI se trataron las variable `@State`. Vimos cómo los cambios en estas variables se reflejan automáticamente en la interfaz. Esto es así con tipos de datos básicos, pero si queremos tener este mismo comportamiento con nuestros propios objetos deberemos marcarlos con el _property wrapper_ `@Observable`.

Por ejemplo, en una aplicación de gestión de tareas definimos una estructura `ToDoItem` para representar los datos de cada tarea, y una clase `ToDoModel` para gestionar el modelo:


```swift
struct ToDoItem : Identifiable {
    var id = UUID()
    var nombreItem: String
    var completado: Bool = false
    var fechaFinalizacion: Date? = nil   
}

@Observable
class ToDoModel: NSObject {

    var toDoItems: [ToDoItem] = []
    
    override init() {
        super.init()        
    }
            
    func addItem(item: ToDoItem) {
        toDoItems.append(item)
    }
}
```

Destacamos que la clase `ToDoModel` está marcada como `@Obserbable`. De esta forma, en nuestra aplicación podríamos crear el modelo como una variable `@State`, y los cambios que se produzcan en la lista de tareas (creación, modificación o borrado de tareas) se reflejarán de forma automática en la interfaz.

> La clase del modelo es un lugar apropiado para introducir el código de acceso a iCloud y CloudKit. Por ejemplo, desde `init` podemos recuperar todos los registros de la base de datos de CloudKit, y en `addItem` además de añadir el _item_ en la lista local, podemos añadir el nuevo registro en CloudKit. Podemos también utilizar esta clase para gestionar el contador de tareas finalizadas, y almacenar y recuperar su valor del almacen de parejas clave-valor de iCloud. 

También cabe destacar el haber declarado la estructura `ToDoItem` como `Identifiable`. Este protocolo nos obliga a declarar una propiedad `id` que debe ser única. El cumplir con dicho protocolo nos permitirá iterar por elementos de dicho tipo utilizando `ForEach` sin tener que indicar un identificador de forma explífica, como se muestra a continuación al crear la lista:

```swift
struct ToDoList: View {
    @State private var model = ToDoModel()
    
    var body: some View {        
        NavigationView {
            List() {
                ForEach(model.toDoItems) { item in
                    ToDoRow(item: item)
                }
            }
        }
    }
}

struct ToDoRow: View {
    var item: ToDoItem
    
    var body: some View {
        HStack {
            Text(item.nombreItem)
            Spacer()
            if item.completado {
                Image(systemName: "checkmark")
            }
        }
    }
}
```

> Para la creación de la imagen se utiliza un nombre del sistema, que nos da acceso a una amplia galería de iconos. Se puede explorar esta galería descargando la aplicación [SF Symbols](https://developer.apple.com/design/human-interface-guidelines/sf-symbols).


## Objetos del entorno

En el punto anterior hemos visto cómo crear la clase para gestionar el modelo de nuestra aplicación y cómo hacerla `@Observable` e incluirla como variable `@State` de nuestra clase. Este tipo de variables se deben declarar como privadas de la vista en la que se crean, sin embargo, el modelo es un objeto al que necesitaremos tener acceso desde un gran número de vistas. 

En estos casos es conveniente introducir este objeto en el **entorno** de la aplicación, de forma que podamos acceder a él desde cualquier subvista. Para ello, instanciaremos el modelo en la vista raíz como variable `@State`, y al crear la vista `ToDoList` lo introduciremos en el entorno con `environment`:

```swift
struct ToDoSwiftUIApp: App {
    @State private var model = ToDoModel()
    
    var body: some Scene {
        WindowGroup {
            ToDoList()
                .environment(model)
        }
    }
}
```

De esta forma, tanto `ToDoList` como todas sus vistas descendientes tendrán acceso al modelo inyectándolo con `@Environment`, como se muestra a continuación:

```swift
struct ToDoList: View {
    @Environment(ToDoModel.self) private var model
    
    var body: some View {        
        NavigationView {
            List() {
                ForEach(model.toDoItems) { item in
                    ToDoRow(item: item)
                }
            }
        }
    }
}
```

> Es importante resaltar que una variable se declara como `@State` solo cuando estamos en la vista que la crea (en este caso es en la clase principal de la aplicación `ToDoSwiftUIApp`), y siempre se debe definir como privada. 

## _Binding_ con propiedades del modelo

Será habitual necesitar modificar propiedades del modelo en alguna vista. Por ejemplo, en nuestro caso nos puede interesar modificar la propiedad `completado` de las tareas de la lista. Para ello, deberemos hacer las propiedades de dicho objeto `@Bindables`.

Si nuestro objeto ha sido recuperado como objeto del entorno, podemos hacerlo `@Bindable` de la siguiente forma:

```swift 
struct ToDoList: View {    
    @Environment(ToDoModel.self) private var model
    
    var body: some View {
        @Bindable var model = model
        
        NavigationView {
            List() {
                ForEach($model.toDoItems) { $item in
                    ToDoRow(item: item)
                        .contentShape(Rectangle())
                        .onTapGesture {
                            item.completado.toggle()
                            item.fechaFinalizacion = Date()
                        }
                }
            }
        }
    }
}
```

Podemos observar que re-declaramos el modelo dentro de nuestra vista para hacerlo `@Bindable`. De esta forma, podemos utilizar _binding_ para que al obtener cada _item_ de la lista (observar el uso de `$` dentro del `ForEach`), este quede vinculado con su estructura dentro del modelo y de esta forma los cambios en la interfaz se reflejen en los datos del modelo.

> Destacamos también en este código el uso de `contentShape`. Al utilizar `onTapGesture`, por defecto solo registrará el evento cuando pulsemos sobre un lugar de la fila que tenga contenido (texto o símbolo de _check_). Haciendo que `contentShape` sea rectangular, conseguimos que todo el rectángulo que ocupa la fila sea sensible a los eventos _tap_.


## Valores del entorno proporcionados por el sistema

Hemos visto cómo incluir nuestros objetos en el entorno de la aplicación. Además, podemos encontrar en el entorno diferentes valores que nos proporciona el sistema, y que nos pueden ser de gran utilidad para nuestra aplicación. 

Por ejemplo, si inyectamos el valor `\.scenePhase` tendremos información sobre el estado en el que se encuentra la aplicación (activa, inactiva o en _background_). Podemos utilizar `onChange` para observar cuándo cambia esta propiedad, como se muestra a continuación:

```swift
struct ToDoList: View {
    
    @Environment(ToDoModel.self) private var model
    @Environment(\.scenePhase) var scenePhase
    
    var body: some View {
        @Bindable var model = model
        
        NavigationView {
            List() {
                ForEach($model.toDoItems) { $item in
                    ToDoRow(item: item)
                        .contentShape(Rectangle())
                        .onTapGesture {
                            item.completado.toggle()
                            item.fechaFinalizacion = Date()
                        }
                }
            }
            .onChange(of: scenePhase) {
                if(scenePhase == .active) {
                    print("Aplicacion activa")
                } else if(scenePhase == .background) {
                    print("La aplicacion ha ido a background")
                } 
            }
        }
    }
}
```

Resulta de gran utilidad también la propiedad `dismiss`, que nos permitirá cerrar cualquier vista modal. Por ejemplo, podemos utilizar `sheet` para abrir una vista modal para añadir una nueva tarea:

```swift
struct ToDoList: View {    
    @Environment(ToDoModel.self) private var model
    @State private var isAddItemPresented : Bool = false
    
    var body: some View {
        @Bindable var model = model
        
        NavigationView {
            List() {
                ForEach($model.toDoItems) { $item in
                    ToDoRow(item: item)
                        .contentShape(Rectangle())
                        .onTapGesture {
                            item.completado.toggle()
                            item.fechaFinalizacion = Date()
                        }
                }
            }
            .toolbar {
                ToolbarItem(placement: .navigationBarTrailing) {
                    Button("Add Item", systemImage: "plus") {
                        self.isAddItemPresented = true
                    }
                }
            }
            .sheet(isPresented: $isAddItemPresented) {
                AddTask()
            }

        }
    }
}
```

Podemos ver que para controlar si la vista modal se muestra o no utilizamos una variable `@State` `isAddItemPresented`. Añadimos un botón a la barra de herramientas (`ToolbarItem`) que al pulsarse pone dicha propiedad a `true` y de esa forma muestra la vista modal. 

Podemos cerrar la vista modal simplemente poniendo la propiedad de nuevo a `false`, dentro desde dentro del código de la vista nos puede ser más sencillo simplemente utilizar `dismiss`, como se muestra a continuación:

```swift
struct AddTask: View {
    @Environment(\.dismiss) var dismiss
    @Environment(ToDoModel.self) private var model
    @State private var item: ToDoItem = ToDoItem(nombreItem: "")
    
    var body: some View {
        NavigationView {
            VStack {
                List {
                    TextField("Descripción de la tarea", text: $item.nombreItem)
                    Toggle("Completado", isOn: $item.completado)
                }
            }.toolbar() {
                ToolbarItem(placement: .confirmationAction) {
                    Button("Save") {
                        if(!item.nombreItem.isEmpty) {
                            model.addItem(item: item)
                        }
                        dismiss()
                    }
                }
                ToolbarItem(placement: .cancellationAction) {
                    Button("Cancel") {
                        dismiss()
                    }
                }
            }
        }
    }
}
```

En la clase anterior definimos la nueva tarea como variable `@State`, y una _toolbar_ con las opciones _Save_ y _Cancel_. Vemos además que inyectamos el modelo, de forma que en caso de seleccionar _Save_ añadimos el nuevo _item_ a la lista. Tanto si seleccionamos _Cancel_ como _Save_, llamaremos a `dismiss()` para cerrar la vista modal.

## _Binding_ en nuestras propias vistas

Hemos visto cómo utilizar _binding_ para que diferentes vistas de SwiftUI sean capaces de modificar nuestros datos. Puede que queramos crear una vista propia que sea capaz de hacer lo mismo, y recibir un dato mediante _binding_ que podamos editar. 

A continuación, vemos un ejemplo de vista que nos muestra los datos de una tarea y un botón que nos permite ir a una pantalla para editarla. Dado que se trata de una pantalla de edición que deberá poder modificar nuestra tarea, el _item_ se debe pasar mediante _binding_ (`$item`):

```swift
struct ViewTask: View {
    @State private var item: ToDoItem = ToDoItem(nombreItem: "")
    @State private var isEditItemPresented = false
    
    var body: some View {
        NavigationView {
            VStack {
                List {
                    Text(item.nombreItem)
                    Text(item.completado ? "Completado" : "Pendiente")
                }
                Button("Editar") {
                    isEditItemPresented = true
                }
            }.sheet(isPresented: $isEditItemPresented) {
                // Creamos vista modal para añadir una nueva tarea
                EditTask(item: $item)
            }
        }
    }
}
```

Para que esto sea posible en la pantalla de edición definimos la variable `item` como `@Binding`. De esta forma podrá recibir el dato vinculado y podrá modificar su contenido:

```swift
struct EditTask: View {
    @Environment(\.dismiss) var dismiss
    @Binding var item: ToDoItem
    
    var body: some View {
        NavigationView {
            List {
                TextField("Descripción de la tarea", text: $item.nombreItem)
                Toggle("Completado", isOn: $item.completado)
            }.toolbar() {
                ToolbarItem(placement: .cancellationAction) {
                    Button("Cerrar") {
                        dismiss()
                    }
                }
            }
        }
    }
}
```


En la sesión introductoria sobre SwiftUI se trataron las variable `@State` y el _binding_. Declaramos una variable como `@State` en el lugar donde se crea el objeto, y debemos marcarla como privada ya que nunca debería utilizarse fuera de ese ámbito.


Sin embargo, existen mecanismos para pasar ese objeto a otras vistas, que se encuentren por debajo en la jerarquía de vistas. Una de las formas es simplemente pasar dicho objeto como parámetro a otra vista. 

Esto es lo que hemos visto anteriormente 
Por ejemplo, a continuación se muestra el código de una vista para ver los datos de una tarea (su nombre y si es pública o no). Dicha clase crea la tarea (`ToDoItem`) y la declara como `@State private`


## Inclusión del AppDelegate

Hemos visto que la clase `AppDelegate` nos proporciona determinados _callbacks_, como por ejemplo `application(_:didReceiveRemoteNotification:fetchCompletionHandler:)` para recibir notificaciones remotas. 

En una aplicación SwiftUI el punto de entrada no es una clase `AppDelegate`, sino una vista que hereda de `App`. Podemos, no obstante, añadir una clase `AppDelegate` a nuestra aplicación SwiftUI. Para ello, crearemos en primer lugar dicha clase, que adopte el protocolo `UIApplicationDelegate` y definiremos los métodos que necesitemos de dicha clase: 

```swift
class AppDelegate: NSObject, UIApplicationDelegate {
    // MARK: - Delegates
    func application(_ application: UIApplication, didFinishLaunchingWithOptions
                     launchOptions: [UIApplication.LaunchOptionsKey: Any]? = nil) -> Bool {
        return true
    }

}
```

A continuación, desde nuestra vista `App` de SwiftUI inyectamos dicha clase `AppDelegate` mediante `@UIApplicationDelegateAdaptor`:

```swift
struct ToDoSwiftUIApp: App {
    @State private var model = ToDoModel()
    @UIApplicationDelegateAdaptor(AppDelegate.self) var delegate
    
    var body: some Scene {
        WindowGroup {
            ToDoList()
                .environment(model)
        }
    }
}
```

Con esto podremos contar con los métodos de dicha clase de la misma forma que en una aplicación basada en _storyboards_. 