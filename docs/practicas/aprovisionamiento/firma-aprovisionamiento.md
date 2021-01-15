# Práctica 1: Firma, aprovisionamiento y <br/> distribución de apps

En las prácticas de esta sesión deberás trabajar con distintos
aspectos relacionados con la firma, la distribución y el despliegue de
apps en dispositivos reales.

Dada la situación de enseñanza no presencial en la que nos encontramos
en estos momentos, vamos a intentar guiar la práctica lo máximo
posible, indicando paso a paso qué secciones de teoría debes leer y
qué actividades debes realizar.

Resumimos a continuación los objetivos generales de esta práctica:

1. Firmar una app con tu cuenta gratuita e instalarla en un
   dispositivo configurado como dispositivo de desarrollo.
2. Darte de alta en el equipo de desarrollo de la UA.
3. Firmar la app usando un perfil de aprovisionamiento que te permite
   instalarla en cualquier dispositivo autorizado en el perfil.
4. Distribuir esta app firmada con el perfil de aprovisionamiento del
   equipo de la UA usando Firebase.


## 1. Firma e instalación de una app en un dispositivo de desarrollo ##

1. Lee los siguientes apartados del tema de teoría:

    - [Introducción](https://domingogallardo.github.io/apuntes-spm-ios/teoria/firma-aprovisionamiento/firma-aprovisionamiento.html#introduccion):
     Resumen de lo que vamos a ver en este tema e introducción breve a
     la importancia de la seguridad en la plataforma de Apple.
    - [Cuenta de desarrollador de
     Apple](https://domingogallardo.github.io/apuntes-spm-ios/teoria/firma-aprovisionamiento/firma-aprovisionamiento.html#cuenta-de-desarrollador-de-apple):
     Explicación de los distintos tipo de programas de desarrollo en
     la plataforma Apple y características de cada uno.
    - [Demo sobre distintos tipos de programas de desarrollo de
     Apple](https://domingogallardo.github.io/apuntes-spm-ios/teoria/firma-aprovisionamiento/firma-aprovisionamiento.html#demo):
     Demostración sobre el apartado anterior. No es posible
     realizarla de forma no presencial, pero se muestran capturas de pantalla que te pueden dar
     una idea del proceso y guiarte de cara a la realización de la siguiente actividad.
   
   
2. Deberás crear un Apple ID y darte de alta como desarrollador. Si ya lo tienes, no hace falta que hagas
nada.

    Para crear un Apple ID, puedes introducir tus datos en [este
    enlace](https://appleid.apple.com/account?localang=es_es). Este
    Apple ID será el que se asociará a la cuenta de desarrollador.

    Después deberás darte de alta como desarrollador Apple con el Apple ID
    recién creado en [https://developer.apple.com/register/](https://developer.apple.com/register/).

3. Accede a tu portal de desarrollador. Será el portal del programa
   gratuito. Este programa permite acceder a las herramientas de
   desarrollo, la documentación y acceso limitado a ciertas
   capacidades (incluido probar aplicaciones en dispositivos
   conectados a Xcode).

    <img src="imagenes/apple-developer.png" width="600px"/>

    Explora las distintas opciones que permite la cuenta:

    - Documentation
    - pDownloads
    - Forums
    - Bug reporter
    - Help

4. Lee los siguientes apartados de teoría:

    - [Certificados](https://domingogallardo.github.io/apuntes-spm-ios/teoria/firma-aprovisionamiento/firma-aprovisionamiento.html#certificados): Explicación del concepto de certificado, su ubicación en Xcode y
      MacOS y su uso para firmar apps.
    - [Demostración sobre la firma y ejecución de apps](https://domingogallardo.github.io/apuntes-spm-ios/teoria/firma-aprovisionamiento/firma-aprovisionamiento.html#demo_1): No es posible
     realizarla de forma no presencial, pero se muestran capturas de pantalla que te pueden dar
     una idea del proceso y guiarte de cara a la realización de la
     siguiente actividad.
     
4. Descarga la app
   [ToDo](https://github.com/domingogallardo/apuntes-spm-ios/raw/master/apps/ToDo.zip). Y
   sigue los pasos de la demostración anterior para crear el
   certificado gratuito de desarrollador, cambiar el _bundle ID_ de la
   app a un identificador tuyo, firmar la app, comprobar el
   certificado y probar la app en tu dispositivo de desarrollo (si lo tienes).

5. Incluye tu nombre en la pantalla en la que aparece el número de
   tareas terminadas (a la que se accede pulsando en el botón _Done_).

6. Captura la pantalla de la app ejecutándose en el dispositivo
   mostrando la pantalla con tu nombre y guárdala como
   documentación. Si no tienes dispositivo, hazlo con la ejecución del
   simulador. 

## 2. Configuración de la cuenta de desarrollador ##

1. Para la inscripción en el equipo de desarrollo de la universidad
   escribe tu nombre, apellidos, dirección de e-mail en [este fichero
   Google
   Docs](https://docs.google.com/document/d/1-fgqgzKNPpo4--PGUvrsnXTe_ABA04gLcpv8rtJd9D0/edit?usp=sharing). Escribe
   también el ID del dispositivo para incorporarlo al portal
   del equipo de la UA.

2. Una vez que te añadamos al equipo de la UA recibirás en el correo
   electrónico un mensaje con un código de invitación. Pincha en él e
   introduce allí tu Apple ID.

   <img src="imagenes/member-invitation.png" width="450px"/>

3.   Una vez aceptada la invitación entra en el [portal del
   desarrollador](https://developer.apple.com/account/), comprueba que
   ya estás en el programa de la UA y prueba las distintas opciones 
   disponibles. 

## 3. Firma y despliegue de app con perfil de aprovisionamiento ##

1. Lee los siguientes apartados de teoría:

    - [Capacidades de las
      apps](https://domingogallardo.github.io/apuntes-spm-ios/teoria/firma-aprovisionamiento/firma-aprovisionamiento.html#capacidades-de-las-apps):
      Explicación de cómo configurar las apps para que puedan usar
      determinados servicios restringidos de Apple.
    - [Despliegue de apps en dispositivos de prueba](https://domingogallardo.github.io/apuntes-spm-ios/teoria/firma-aprovisionamiento/firma-aprovisionamiento.html#despliegue-de-apps-en-dispositivos-de-prueba): Explicación de
      cómo el _perfil de aprovisionamiento_ de una app permite a ésta
      utilizar servicios de Apple y ejecutarse en dispositivos de prueba.

2. Sigue los pasos de la [demo y
   ejercicio](https://domingogallardo.github.io/apuntes-spm-ios/teoria/firma-aprovisionamiento/firma-aprovisionamiento.html#demo-y-ejercicio)
   de teoría, realizando además lo siguiente:
    - Captura la pantalla de Xcode en la que se muestre cómo has
      firmado tu app con el perfil genérico (la pantalla equivalente a [esta](https://domingogallardo.github.io/apuntes-spm-ios/teoria/firma-aprovisionamiento/imagenes/perfil-generico-team.png)).
    - Comprueba en el portal del desarrollador de la UA que el
      profesor ha creado el _App ID_ y el perfil de aprovisionamiento `Master
      Moviles ToDo`. Captura las pantallas con las páginas del portal del
      desarrollador de la UA mostrándolos (incluye en las pantallas
      toda la ventana del navegador, para que aparezca tu usuario en
      la parte superior derecha).
    - Captura la pantalla de Xcode en la que se muestre cómo has
      firmado tu app con el perfil `Master Moviles ToDo` (la pantalla
      equivalente a [esta](https://domingogallardo.github.io/apuntes-spm-ios/teoria/firma-aprovisionamiento/imagenes/provisioning-profile-manual-1.png))
    - Si tienes algún dispositivo iOS, instala el fichero .ipa
      obtenido usando Apple Configurator 2. El UUID del dispositivo
      deberá estar incluido en el perfil de aprovisionamiento. Captura
      una pantalla de la aplicación `Apple Configurator 2` instalando
      la app en el dispositivo. Comprueba que la app funciona
      correctamente. 

## Entregas ##

Crea una carpeta y guarda en ella lo siguiente:

- Captura la pantalla de la app ejecutándose en el dispositivo (o en
  el simulador, si no tienes) mostrando la pantalla con tu nombre
  (paso 1.7).
- Capturas del paso 3.2.
- Binario .ipa del apartado 3.2
- Carpeta con el proyecto completo.

Comprime la carpeta y entrégala en la actividad de Moodle _Entrega 1_.


