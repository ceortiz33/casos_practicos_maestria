# Parte II

# Brainly(App de Google Play)

**Descripción de la aplicación**

Brainly es una aplicación de preguntas y respuestas, tiene tres modos principales de uso: modo teclado, modo cámara y modo micrófono. En modo teclado el usuario puede escribir su pregunta directamente y esperar que otro usuario de una respuesta, el modo cámara permite tomar fotos de las preguntas que uno quiera proponer, el modo micrófono de igual manera permite hacer una pregunta, pero mediante la voz.

El sistema que utiliza Brainly es el siguiente, un usuario pregunta y obtiene respuestas de los demás, las preguntas pueden ser calificadas con estrellas siendo 5 estrellas la calificación más alta, también pueden reaccionar si a las personas les gusto esa pregunta.

El objetivo de la plataforma es crear un repositorio de preguntas con temas variados, cada usuario empieza con un puntaje de 0 y en nivel principiante, a medida que se van superando varias preguntas el puntaje incrementa y de esta manera se sube de rango.

En la versión free no se ve una tabla de puntajes con las personas de mayor rango en la plataforma, pero si se puede ingresar a los perfiles de cada usuario y seguirlas o viceversa que esas personas te sigan. 

**Herramientas y alcance**

Para el análisis estático se usarán las siguientes herramientas, jadx en modo línea de comandos para decompilar la apk y Android Studio como entorno IDE para hacer las respectivas modificaciones como el Refactor que es de uso esencial con códigos ofuscados.

En el caso del análisis dinámico se usarán herramientas como Drozer y Frida con sus aplicaciones client y server respectivamente, además se usará Objection que es un framework que utiliza Frida como base de su funcionamiento y facilitara la enumeración de clases y método para hacer hooking.

Como emulador de Android se usa el emulador de Android Studio con un dispositivo Nexus 5x Google API aprovechando que esta versión viene rooteada a diferencia de su contraparte Nexus 5x Google Play que si bien viene con la App Store de Google no tiene acceso root.

**Brainly**

![](/images/modulo7.2/brainly.png)

## Android Manifest

**Android Permissions**

![](/images/modulo7.2/img1.png)

**INTERNET:** Es uno de los permisos mas comunes y como su nombre indica provee acceso a internet a la aplicación, su uso no representa un riesgo. Es necesario ya que la aplicación se conecta a Internet.

**WRITE_EXTERNAL_STORAGE:** Permite a una aplicación escribir en el almacenamiento externo, esta catalogada como peligroso, a partir de la API 19 este permiso ya no es requerido para leer o escribir archivos en los directorios especificos de la aplicación.

**ACCESS_NETWORK_STATE:** Permite el acceso a la informacion acerca de las redes, esta catalogada como normal, es uno de los permisos mas comunes.

**GET_ACCOUNTS:** Permite el acceso a las cuentas en el servicio de cuentas, este permiso es considerado peligroso,la aplicación al momento de ejecutarse por primera vez solicita la autenticacoin del usuario usando una cuenta de Google asi que en este caso si se justifica.

**WAKE_LOCK:** Permite el uso de PowerManager WakeLocks para previene al procesador de dormir o del oscurecimiento de la pantalla. Este permiso no representa riesgo.

**VIBRATE:** Permite acceso a la vibracion, no es considerado un permiso peligroso.

**CAMERA:** Permite a la aplicación el uso de la camara, este permiso es considerado como peligroso y si no es necesario se deberia evitar. Brainly tiene una funcionalidad que permite tomar fotos a preguntas para que luego estas sean respondidas .

**C2D_MESSAGE:** (Cloud to device messaging), envia una peticion para hacer push a los mensajes hacia los servidores C2D de Google.

**RECEIVE:** Es un permiso requerido para configurar el cliente de Google Cloud Messaging en Android.

**READ_EXTERNAL_STORAGE:** Permite leer el almacenamiento externo, este permiso se otorga implicitamente a raiz de declarar el permiso WRITE_EXTERNAL_STORAGE. Es considerado como peligroso.

**BIND_GET_INSTALLER_REFERRER_SERVICE:** Es un permiso por defecto que se genera a partir de la API de Google. 

**FOREGROUND_SERVICE:** Permite a una aplicación regular usar el servicio startForeGround. Este permiso no representa un riesgo. 

**Evaluación de exposición de la aplicación con Drozer**

Con la herramienta Drozer se puede observar que tan expuesta esta una aplicación, en este caso hay 4 activities, 2 broadcast receivers y 1 servicio que están exportados como true.

**Superficie de Exposicion**

![](/images/modulo7.2/img2.png)

**Activities exportadas como true para la aplicación Brainly**

![](/images/modulo7.2/img3.png)

**Broadcast receivers exportados como true para la aplicación Brainly**

![](/images/modulo7.2/img4.png)

**Services exportados como true para la aplicación Brainly**

![](/images/modulo7.2/img5.png)

Analisis Objection

**Rutas y Posibles contrasenas**

![](/images/modulo7.2/img6.png)

**/data/user/0/co.brainly/cache/** WebView/SafeBrowsing  -> SafeBrowsing?

**data/user/0/co.brainly/code-cache** -> no se encontro nada

**/data/user/0/co.brainly/files**

  + AppEventsLogger -> Posibles clases dedicadas a los logs de la aplicacion
  
  ![](/images/modulo7.2/appeventlogger.png)
  
  + gaClientid **c04c67c9-23a0-4726-8838-48bcc85bf78d**
  
  + google_app_measurement.db [REVISAR CON SQLITE-BROWSER]

**/storage/emulated/0/Android/data/co.brainly/cache** -> no se encontro nada

**/storage/emulated/0/Android/obb/co.brainly** -> no se encontro nada

**List Activities**

![](/images/modulo7.2/img7.png)







