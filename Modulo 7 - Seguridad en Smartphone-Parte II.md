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

Analisis Objection

**Rutas y Posibles contrasenas**

![](/images/modulo7.2/img6.png)

**/data/user/0/co.brainly/cache/** 

+ /WebView/SafeBrowsing  

A partir de la API 27 (Android 8.1) se introduce la API SafeBrowsing que permite a la aplicacion detectar URLs clasificadas como amenza por Google. Esto permite que los usuarios esten mas conscientes de los peligros ya que esta API tiene como funcionalidad mostrar una alerta de seguridad cuando se carga una URL y detiene la carga de la pagina.

+ /cache/image_cache -> Se hace mencion a la libreria **libcore.io.DiskLruCache**

![](/images/modulo7.2/journal_cache.png)

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

**List Receivers**

![](/images/modulo7.2/img8.png)

**List Services**

![](/images/modulo7.2/img9.png)

## Analisis de componentes del manifiesto de Android

![](/images/modulo7.2/img10.png)

El backup de la aplicacion esta habilitado por lo tanto el usuario tiene acceso a esta configuracion que normalmente no deberia estar disponible en produccion.
La configuracion de red permite limitar el uso a ciertos dominios o certificados, motivo por el cual tambien se realizara una busqueda de la informacion proporcionada en este archivo.

**Custom Deep Link Schema**

![](/images/modulo7.2/img11.png)

Este Deep link unicamente tiene un schema permitido **brainly://**

**HTTP Deeplink Scheme**

![](/images/modulo7.2/img12.png)

![](/images/modulo7.2/img13.png)

Este es un DeepLink con varios componentes, para formar la URL se combinan **scheme**, **host**, **Path** y **PathPrefix**  un ejemplo seria `https://brainly.co.id/tugas`

**ShareAskActivity**

![](/images/modulo7.2/img14.png)

Esta es una de las activities exportadas como true y muestra un intent-filter con **android.intent.action.SEND** que permite enviar data de una activity a otra.

**ShareSendActivity**

![](/images/modulo7.2/img16.png)

De igual manera ShareSendActivity tambien envia la data de una activity a otra. Otros elementos que se encontraron en la aplicación fue un FileProvider para almacenar archivos.

**FileProvider**

![](/images/modulo7.2/img15.png)

Brainly utiliza otras integraciones de aplicaciones de terceros como Google Firebase y SWRVE donde se muestran algunas API KEYS.[REVISAR DOCUMENTACION DE FIREBASE SOBRE EXPOSICION DE ANDROID VALUE]

**Firebase key**

![](images/modulo7.2/img17.png)

**Activities relacionadas con la sesion y feedback**

![](/images/modulo7.2/img18.png)

Otro file provider en Brainly, ademas se muestra una activity de captcha.

**Fileprovider y Captcha**

![](/images/modulo7.2/img19.png)

## Analisis Estatico

### Exported Components

Con la herramienta Drozer se puede observar que tan expuesta esta una aplicación, en este caso hay 4 activities, 2 broadcast receivers y 1 servicio que están exportados como true.

**Superficie de Exposicion**

![](/images/modulo7.2/img2.png)

**Activities exportadas como true para la aplicación Brainly**

![](/images/modulo7.2/img3.png)

**Broadcast receivers exportados como true para la aplicación Brainly**

![](/images/modulo7.2/img4.png)

**Services exportados como true para la aplicación Brainly**

![](/images/modulo7.2/img5.png)

### Analisis de cifrados debiles

`KeyGenParameterSpec` indica que la clave puede ser utilizada para encriptacion y desencriptacion, este funcion es implementada mediante la API de Amazon Web Services(**AWS**)

Funciones con cifrado debil

Como parte de los servicios de AWS esta utiliza cifrados compatibles definidos en la API como  SHA-256 Y MD5, Un caso en particular donde se emplea el cifrado SHA-256 es en **com.amazonaws.auth.AbstractAWSSigner**, para tratamiento interno de MD5 se usa `MessageDigest.getInstance(MD5)` en **com.amazonaws.services.s3.internal.MD5DigestCalculatingInputStream** 

En el caso de la API Swrve utiliza tanto el cifrado MD5 y SHA1 dentro de sus procesos internos con las funciones MessageDigest.getInstance(MD5) y MessageDigest.getInstance(sha1) respectivamente.

En la clase d.a.m.p.a se encuentra el uso del cifrado SHA-1 para generar el token, este cifrado es considerado inseguro.

```java
public a(SharedPreferences sharedPreferences, d.a.m.l.a aVar, v vVar) {
        String str;
        this.a = sharedPreferences;
        this.b = aVar;
        this.f1066d = sharedPreferences.getString("com.brainly.lt", (String) null);
        String string = sharedPreferences.getString("com.brainly.gt", (String) null);
        this.f1067e = string;
        if (string == null) {
            String string2 = vVar.a.getString("ANDROID_DEVICE_ID", (String) null);
            if (string2 == null) {
                string2 = Settings.Secure.getString(vVar.b.getContentResolver(), "android_id");
                if (TextUtils.isEmpty(string2)) {
                    string2 = Build.SERIAL;
                    if (TextUtils.isEmpty(string2) || string2.equals("unknown")) {
                        string2 = UUID.randomUUID().toString();
                    }
                }
                vVar.a.edit().putString("ANDROID_DEVICE_ID", string2).commit();
            }
            try {
                MessageDigest instance = MessageDigest.getInstance("SHA-1");   // cifrado inseguro para generar el token
                instance.update(string2.getBytes(n0.x.a.a));
                str = x.u(instance.digest());
            } catch (NoSuchAlgorithmException e2) {
                s0.a.a.f3099d.e(e2, "Problem while generating guest token", new Object[0]);
                StringBuilder sb = new StringBuilder(string2.length() + 40);
                while (sb.length() < 40) {
                    sb.append(string2);
                }
                str = sb.substring(0, 40);
            }
            this.f1067e = str;
            sharedPreferences.edit().putString("com.brainly.gt", this.f1067e).apply();
        }
```

[CHEQUEAR CON MOBILE SECURITY TESTING GUIDE ]

**Deteccion de dispositivo rooteado**

Deteccion si la aplicacion Superuser.apk esta instalada en el dispositivo.

```java
public static boolean s(Context context) {
        boolean q = q(context);
        String str = Build.TAGS;
        if ((!q && str != null && str.contains("test-keys")) || new File("/system/app/Superuser.apk").exists()) {
            return true;
        }
        File file = new File("/system/xbin/su");
        if (q || !file.exists()) {
            return false;
        }
        return true;
    }
```

[CHEQUEAR CON MOBILE SECURITY TESTING GUIDE]

**File Providers**

```xml
<provider android:name="androidx.core.content.FileProvider" android:exported="false"
   android:authorities="co.brainly.fileprovider"
   android:grantUriPermissions="true">
   <meta-data android:name="android.support.FILE_PROVIDER_PATHS" android:resource="@xml/file_paths"/>
</provider>
```

**Obtencion de datos del dispositivo**



**/res/xml/file_paths**

![](/images/modulo7.2/img23.png)

Para acceder al contenido de este file provider y crear la URI se necesita combinar **android:authorities** seguido de **name** y el nombre de la imagen formando asi la siguiente URI.

`content://co.brainly.fileprovider/images/default_image.jpg`

```xml
<provider android:name="com.brainly.tutoring.sdk.internal.common.TutoringSdkFileProvider" android:exported="false"
    android:authorities="co.brainly.tutoringsdk.fileProvider" android:grantUriPermissions="true">
    <meta-data android:name="android.support.FILE_PROVIDER_PATHS" android:resource="@xml/tutoring_sdk_provider_paths"/>
</provider>
```

**/res/xml/tutoring_sdk_provider_paths**

![](/images/modulo7.2/img24.png)

```
content://co.brainly.tutoringsdk.fileProvider/external_cache_files/default_images.jpg
content://co.brainly.tutoringsdk.fileProvider/external_files_files/default_images.jpg
content://co.brainly.tutoringsdk.fileProvider/external_files/default_images.jpg
```

Compartir un file-provider con path="." puede ser peligroso incluso si el provider no esta exportado si existiese otra vulnerabilidad.[REVISAR PARA EXPLOTACION]

### File Recon

com.google.android.material.bottomsheet.BottomSheetBehavior ??

**/res/values/strings.xml**

```xml
<string name="branch_friends_invite_link">https://brainly.app.link/qpzV02MawO</string>
<string name="branch_key">key_live_aieQuVpL7UJKTxoGT7baLblpBBbKyhSO</string>
<string name="com.crashlytics.android.build_id">56b2b0997a1a40ad89be0f645f92a7d6</string>
<string name="copy_toast_msg">Link copied to clipboard</string>
<string name="default_web_client_id">552450804349-945msqm416u8ijp5pjkuo74smrergs1q.apps.googleusercontent.com</string>
<string name="firebase_database_url">https://brainly-android.firebaseio.com</string>
<string name="gcm_defaultSenderId">552450804349</string>
<string name="google_api_key">AIzaSyB_6V8TSM6BH7MxmxVEdiK7HjnYd_00tvs</string>
<string name="google_app_id">1:552450804349:android:e135b0ade797116b</string>
<string name="google_crash_reporting_api_key">AIzaSyB_6V8TSM6BH7MxmxVEdiK7HjnYd_00tvs</string>
<string name="google_play_link">https://play.google.com/store/apps/details?id=co.brainly</string>
<string name="points_award_header">YOU ROCK!</string>
```
**/res/values/public.xml**

Valores referenciados de strings en la aplicacion

**/res/xml/file_paths**

[CHECKEAR COMO ACCEDER A ESTE DIRECTORIO]

![](/images/modulo7.2/img20.png)

**/res/xml/network_security_config.xml**

![](/images/modulo7.2/img21.png)

Trafico en texto plano habilitado.

### Insecure Data and File Storage

**Firebase credentials**

Cuando se dejan las configuraciones por defecto de las bases de datos de Firebase estas pueden provocar una filtración de datos, por lo tanto se evalua si los privilegios han sido asignados correctamente.

En el archivo strings.xml se mostro la URL que corresponde a la base de datos firebase **https://brainly-android.firebaseio.com** . Para evaluar si la base de datos es de acceso publico se le agrega **/.json** a la URL.

**Base de datos no es de acceso publico**

![](/images/modulo7.2/img22.png)

La Url no es de acceso publico por lo tanto las configuraciones de seguridad de Firebase no tiene habilitado la opción **‘read permission’**.

**Internal Storage**

Los archivos creados en el almacenamiento interno solo tiene acceso la misma app, sin embargo cuando se aplican las flags **MODE_WORLD_READABLE** Y **MODE_WORLD_WRITABLE** expone dichos archivo a diferentes aplicaciones. El almacenamiento interno se localiza en su mayoria en **/data/data/[nombre_app]/shared_prefs**.

+ swrve_prefs.xml

```xml
<?xml version='1.0' encoding='utf-8' standalone='yes' ?>
<map>
    <int name="swrve_cr_flush_delay" value="1000" />
    <string name="userId">4e28921c-9722-4fa4-816d-9d468a66b55f</string>
    <int name="swrve_cr_flush_frequency" value="60000" />
</map>
```

+ co.brainly_preferences.xml

```xml
<?xml version='1.0' encoding='utf-8' standalone='yes' ?>
<map>
    <set name="PREF_COOKIES">
        <string>datadome=Paqa3XA7S.PddkBJVxn63M7LTnV94O3MEuwGP4ka-lyr_LoCuQlRINBNR.u-VAnXUROVG7qBLeEXt5eK~P.8dRt0miznXQEokuvr..rsKE; Max-Age=31536000; Domain=.brainly.com; Path=/; SameSite=Lax</string>
    </set>
    <long name="com.facebook.appevents.SessionInfo.sessionStartTime" value="1601610705300" />
    <long name="com.facebook.appevents.SessionInfo.sessionEndTime" value="1601610872892" />
    <int name="com.facebook.appevents.SessionInfo.interruptionCount" value="3" />
    <string name="com.facebook.appevents.SessionInfo.sessionId">2ffa1bcd-8530-4d2c-8149-5cf1ed229c28</string>
</map>
```

En el codigo fuente no se encontraron estas flags por lo que no se expone a otras aplicaciones, dentro de **shared_prefs** se hallaron cookies almacenadas y el userId[REVISAR SI HAY FORMA DE EXPLOTARLO]

**External Storage**

El almacenamiento externo puede ser accesado mediante **/storage/emulated/0**, estos archivos son de uso publico por lo que no se recomienda almacenar datos sensibles aqui, adicionalmente los datos almacenados pueden ser filtrados mediante el uso de los content providers si llegasen a estar expuestos. Brainly no tiene content providers expuestos por lo que no es posible filtrar esta informacion ni abusar de posibles SQL Injection.

**Other Interesting functions**

**Code Execution**

Si existiesen alguna de estas funciones se pueden aprovechar para ejecutar remotamente comandos. Dentro del codigo de Brainly no se encontraron.

+ Runtime.exec()
+ ProcessBuilder()
+ native code:system()

**Send SMS**

De igual manera si existiese funciones para enviar mensajes, estas funciones podrian estar dentro del codigo fuente. Brainly no tiene habilitado el permiso para enviar SMS ni tiene habilitada estas funciones.

+ sendTextMessage
+ sendMultipartTestMessage

**Native functions**

Algunas aplicaciones para dificultar aun mas la labor de ingenieria reversa al analista emplean funciones nativas. Brainly se basa en la **ofuscacion** como mecanismo de proteccion, ademas de utilizar una mezcla de librerias entre **Kotlin** y **Java**, en la busqueda de las funciones no se encontraron las funciones nativas `public native, System.loadLibrary, System.load`.

**Exploiting Backup**

`adb backup -apk -shared co.brainly`

![](/images/modulo7.2/img25.png)

**Backup generado**

![](/images/modulo7.2/img26.png)

Una herramienta que facilita el backup de archivo en Android para entorno Windows es **Android Backup Extractor**, en esta herramienta se localiza el archivo **abe.jar** y se ejecuta el siguiente comando.

`java -jar abe.jar unpack backup.ab backup.tar ""`

En caso que pida una contrasena se escribe la contrasena que se pidio antes de realizar el backup en este caso fue 1111

![](/images/modulo7.2/img27.png)

Como resultado se genera una carpeta con los archivos de backup, uno de los ficheros que compete examinar es db que contiene las bases de datos almacenadas en la aplicacion.

+ google_analytics_v4.db
  + android_metadata -> en_US
  + hits2 -> Sin datos
  + properties -> valores de alguna de las propiedades
  + sqlite_sequence -> Sin datos

+ google_app_measurement.db
  + android_metadata -> 
  + app2 -> Numero de veces abierta la aplicacion
  + apps -> 
  + audience_filter_values -> ID de la audiencia
  + conditional_properties -> Sin datos
  + default_event_params -> Sin datos
  + event_filters -> Sin datos  
  + events -> Eventos capturados
  
  ![](/images/modulo7.2/img33.png)
  
  + main_event_params -> Sin datos.
  + property_filters -> parametro **gets_paywall** con audience id 6. 
  + queue -> Sin datos.
  + raw_events -> eventos capturados en bruto.
  + raw_events_metadata -> metadata fingerprint.
  + user_attributes -> Varios atributos relacionados con los datos del usuario sin llegar a filtrar usuario y contrasena.
  
  ![](/images/modulo7.2/img31.png)

+ google_app_measurement_local.db
  + android_metadata -> en_US.
  + messages -> Sin datos

+ swrve.db
  + android_metadata -> en_US
  + cache -> Varios parametros relacionados con el servicio swrve
  
  ![](/images/modulo7.2/img32.png)
  
  + events -> Sin datos 
  + notifications_authenticated -> Sin datos
  + offline campaigns -> Sin datos
  + sqlite_sequence -> 17 eventos
  + users -> Sin datos



[IDEAS]
EXPLORAR LOS DIRECTORIOS Y VER SI SE ENCUENTRA ALGUNA PASSWORD
INTENTAR CARGAR IMAGENES CON SCRIPTS MALICIOSO EN EL AVATAR
IDENTIFICAR POSIBLES VECTORES DE ENTRADA




## Analisis Dinamico

**Burp Suite**

REVISAR HISTORIAL DE HTTP
TRATAR DE EXPLOTAR ALGUNA PETICION
REALIZAR ALGUNA PREGUNTA (MIRAR EL TRAFICO QUE PASA)

**Analisis de DeepLinks**

Durante este analisis se realiza una busqueda de funciones vulnerables en la activity donde se declaran los deeplinks en este caso **StartActivity**

+ `setJavaScriptEnabled(true)` puede producir XSS al llamar la aplicacion mediante un deeplink
+ `getQueryParameter('parameter')` puede producir algunos vectores de ataque como XSS, LFI.
+ `Runtime.getRuntime().exec()` puede producir RCE
+ `android:pathPattern ="/.*/"` puede producir ataques XSS

DE MOMENTO NO SE ENCONTRO VULNERABLE LA APLICACION POR ESTA VIA.

`adb shell am start -W -a android.intent.action.VIEW -d "http://brainly.pl/zadanie" co.brainly`

**Explotacion de componentes exportados**

**Exported Activities**

Las siguientes activities estan exportadas como **true** para evaluar si en estas activities se expone algun dato vulnerable se usa el siguiente comando

`adb shell am start -n <package>/<activity>`

![](/images/modulo7.2/img29.png)

Las activities a pesar de estar exportadas estas apuntan a la activity principal y no exponen ningun dato.

**Usar Mobsf para comprobar de que va cada libreria**

**Testing WebViews**

javaScriptEnabled=true?

..

