# Parte III

# Reca(Aplicacion de mirror de apks)

**Descripción de la aplicación**

Reca es una aplicacion de venta de articulos de ferreteria en Alemania descargada de un repositorio de apks externo conocido como www.apkmonk.com. Esta aplicacion tiene varias opciones News, Catalogue, Basket, Branches, Profile.

En News aparecen los nuevos articulos que han sido agregados al catalogo, cada articulo tiene su descripcion, si se da clic en uno de estos articulos aparece mayor informacion como tambien articulos sugeridos que podrian interesarle a los clientes.En la opcion catalogo se muestra el stock disponible de articulos relacionados a ferreteria y productos quimicos.

En la opcion Basket aparecen los articulos que se han agregado al carrito para su posterior compra si asi el cliente lo desea, en Branches se muestran las sucursales donde se puede encontrar los productos y aparecen dos opciones la sede en Hamburg y la sede de Hannover, Finalmente esta la opcion de Profiles o perfiles aparece el usuario que esta visitando la aplicacion, RECA por defecto indica que no es necesario tener una cuenta para ver sus productos, sin embargo para hacer la compra si se debe ingresar un perfil.

**Herramientas y alcance**

Para el análisis estático se usarán las siguientes herramientas, jadx en modo línea de comandos para decompilar la apk y MobsF como entorno para dar un primer vistazo a la seguridad y componentes de la aplicacion.

En el caso del análisis dinámico se usarán herramientas como Drozer y Frida con sus aplicaciones client y server respectivamente, además se usará Objection que es un framework que utiliza Frida como base de su funcionamiento y facilitara la enumeración de clases y método para hacer hooking.

Como emulador de Android se usa el emulador de Android Studio con un dispositivo Nexus 5x Google API aprovechando que esta versión viene rooteada a diferencia de su contraparte Nexus 5x Google Play que si bien viene con la App Store de Google no tiene acceso root.

**Reca**

![](/images/modulo7.3/img1.png)

Para verificar los permisos y demas configuraciones basicas se usara MobSF en este caso para abirlo se utiliza http://192.168.100.4:8000, se carga la apk y comienza a analizar

![](/images/modulo7.3/img2.png)

Una vez terminado el analisis se abre otra ventana con los resultados.

**Resumen de la seguridad de la aplicacion**

![](/images/modulo7.3/img3.png)

## Android Manifest

En el manifiesto de Android se muestran los siguientes permisos.

```xml
<manifest android:versionCode="30240" android:versionName="6.13.1" android:compileSdkVersion="28" android:compileSdkVersionCodename="9" package="project.apriljune.recanorm" platformBuildVersionCode="28" platformBuildVersionName="9"
  xmlns:android="http://schemas.android.com/apk/res/android">
    <uses-sdk android:minSdkVersion="19" android:targetSdkVersion="28" />
    <uses-feature android:glEsVersion="0x00020000" android:required="true" />
    <uses-feature android:name="android.hardware.camera" android:required="false" />
    <uses-feature android:name="android.hardware.camera.autofocus" android:required="false" />
    <uses-feature android:name="android.hardware.screen.landscape" android:required="false" />
    <uses-feature android:name="android.hardware.bluetooth_le" android:required="false" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="com.google.android.providers.gsf.permission.READ_GSERVICES" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.VIBRATE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />
    <uses-permission android:name="android.permission.BLUETOOTH" />
    <uses-permission android:name="android.permission.WRITE_INTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_INTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.CHANGE_CONFIGURATION" />
    <permission android:name="project.apriljune.recanorm.gcm.permission.C2D_MESSAGE" android:protectionLevel="signature" />
    <uses-permission android:name="android.permission.USE_FINGERPRINT" />
    <uses-permission android:name="com.google.android.finsky.permission.BIND_GET_INSTALL_REFERRER_SERVICE" />
    <permission android:name="project.apriljune.recanorm.permission.C2D_MESSAGE" android:protectionLevel="signature" />
    <uses-permission android:name="project.apriljune.recanorm.permission.C2D_MESSAGE" />
```

**INTERNET:** Es uno de los permisos mas comunes y como su nombre indica provee acceso a internet a la aplicación, su uso no representa un riesgo. Es necesario ya que la aplicación se conecta a Internet.

**ACCESS_NETWORK_STATE:** Permite el acceso a la informacion acerca de las redes, esta catalogada como normal, es uno de los permisos mas comunes.

**RECORD_AUDIO:** Permite a la aplicacion acceder al audio de la aplicacion. Este permiso es considerado como peligroso y es innecesario ya que en la aplicacion en ninguna parte se utiliza la voz como medio para efectuar un pago o alguna funcionalidad similar.

**WRITE_EXTERNAL_STORAGE:** Permite a una aplicación escribir en el almacenamiento externo, esta catalogada como peligroso, a partir de la API 19 este permiso ya no es requerido para leer o escribir archivos en los directorios especificos de la aplicación.

**CAMERA:** Permite a la aplicación el uso de la camara, este permiso es considerado como peligroso y si no es necesario se deberia evitar. En RECA se utiliza la camara como medio para tomar foto de los productos y subirlos a la plataforma, su uso esta justificado aunque no es recomendable para aplicaciones de este tipo.

**ACCESS_COARSE_LOCATION:** Permite el acceso a una locacion aproximada de fuentes como la base de datos de la red para determinar una aproximacion de la ubicacion del telefono cuando esta disponible. Este permiso es considerado peligroso ya que un atacante podria filtrar datos correspondientes a la ubicacion de un usuario, este permiso se considera innecesario para esta aplicacion ya que al momento de ingresar el articulo al carrito, de igual forma hay que ingresar los datos, ademas que la aplicacion solo tiene como mercado principal Alemania.

**ACCESS_FINE_LOCATION:** Permite el acceso a fuentes de ubicacion como el GPS del celular cuando esta disponible. Este permiso es considerado peligroso y de igual manera que ACCESS_COARSE_LOCATION le da la posibilidad de extraer datos relacionados a la ubicacion a un atacante, ademas el uso de este permiso consume bateria adicional.

**VIBRATE:** Permite acceso a la vibracion, no es considerado un permiso peligroso.

**WAKE_LOCK:** Permite el uso de PowerManager WakeLocks para previene al procesador de dormir o del oscurecimiento de la pantalla. Este permiso no representa riesgo.

**RECEIVE:** Es un permiso requerido para configurar el cliente de Google Cloud Messaging en Android.

**READ_EXTERNAL_STORAGE:** Permite leer el almacenamiento externo, este permiso se otorga implicitamente a raiz de declarar el permiso WRITE_EXTERNAL_STORAGE. Es considerado como peligroso.

**BLUETOOTH_ADMIN:** Permite a la aplicacion configurar el Bluetooth del celular y descubiri y emparejar con dispositivos remotos, este permiso es considerado como peligroso y en esta aplicacion no se utiliza con ningun dispositivo para emparejar por lo tanto es inncesario su uso.

**BLUETOOTH:** Permite a una aplicacion ver la configuracion de un telefono que tiene activado el Bluetooth  y permite aceptar conexiones para emparejar dispositivos. Este permiso es innecario porque la aplicacion no utiliza ningun sensor o algun dispositivo similar para hacer uso de este permiso. Este permiso es considerado como peligroso

**WRITE_INTERNAL_STORAGE:** Permite a una aplicacion escribir en la memoria SD. Este permiso es considerado peligroso y es innecesario que una aplicacion de este tipo tenga acceso a la memoria SD

**READ_INTERNAL_STORAGE:** Este permiso ya se encuentra en estado deprecated en la documentacion actual por ser inseguro su uso.

**CHANGE_CONFIGURATION:** Permite a una aplicacion cambiar la configuracion actual como el tamano de fuente y otras opciones,Allows an application to change the current configuration, such as the locale or overall font size. Este permiso es considerado como peligroso si bien a primera vista cambiar la fuente no es algo que sea catalogado como peligroso el hecho de que la aplicacion tenga este permiso es un riesgo para el usuario.

**USE_FINGERPRINT:** Es un permiso de uso comun para algun tipo de autenticacion en la API level 28 se ha quedado deprecated y se recomienda el uso de USE_BIOMETRIC en su lugar.

**BIND_GET_INSTALLER_REFERRER_SERVICE:** Es un permiso por defecto que se genera a partir de la API de Google.

**C2D_MESSAGE:** (Cloud to device messaging), envia una peticion para hacer push a los mensajes hacia los servidores C2D de Google.

**Signer Certificate**

![](/images/modulo7.3/img4.png)

## Analisis de componentes del manifiesto de Android


**Componentes de la aplicacion**
![](/images/modulo7.3/img5.png)

















