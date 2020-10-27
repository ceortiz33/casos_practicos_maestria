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

Como parte de los servicios de AWS esta utiliza cifrados compatibles definidos en la API como SHA-256 Y MD5, Un caso en particular donde se emplea el cifrado SHA-256 es en **com.amazonaws.auth.AbstractAWSSigner**,para tratamiento interno de MD5 se usa `MessageDigest.getInstance(MD5)` en **com.amazonaws.services.s3.internal.MD5DigestCalculatingInputStream** 

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

**Análisis de vulnerabilidad según OWASP Mobile Top 10**

**M5: Insufficient Cryptography | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

De acuerdo a la informacion de OWASP TOP 10 esta vulnerabilidad se produce cuando se implementan mecanismos de encriptacion que son debiles y pueden ser desencriptado por el adversario. En la guia Mobile Security Testing Guide se menciona que no es recomendable utilizar cifrados como MD5 Y SHA-1 ya que a dia de hoy son considerados vulnerables si se utilizan para cifrar datos sensibles, en el caso de esta funcion se implementa el algoritmo SHA-1 al archivo SharedPreferences que es donde normalmente se almacenan las bases de datos y otros datos personales.

**Impacto Tecnico**

El uso de cifrados considerados como debiles permite a un atacante obtener la informacion que deberia ser protegida mediante la implementacion de dicho algoritmo.

**Impacto de Negocio**

Los impactos que tiene esta vulnerabilidad son los siguientes: Robo de Informacion debido al cifrado debil en el archivo SharedPreferences que contiene informacion de los estudiantes.

### Deteccion de dispositivo rooteado

Deteccion si la aplicacion Superuser.apk esta instalada en el dispositivo en la clase d.g.c.h.d.j.g

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

### Deteccion de Emulador

Deteccion de emulador en clase d.e.x.c0.d 

```java
String str2 = "0";
jSONArray.put(str2);
if (Build.FINGERPRINT.startsWith("generic") || Build.FINGERPRINT.startsWith("unknown") || Build.MODEL.contains("google_sdk") || Build.MODEL.contains("Emulator") || Build.MODEL.contains("Android SDK built for x86") || Build.MANUFACTURER.contains("Genymotion") || (Build.BRAND.startsWith("generic") && Build.DEVICE.startsWith("generic")) || "google_sdk".equals(Build.PRODUCT)) {
    str2 = "1";
}
jSONArray.put(str2);
Locale j = z.j();
jSONArray.put(j.getLanguage() + "_" + j.getCountry());
String jSONArray2 = jSONArray.toString();

```

### Otras fuentes de informacion

Mayor informacion se puede obtener usando el script en python **androwarn.py** que permite recopilar informacion de la aplicacion para un analisis rapido, adicionalmente a los datos ya obtenidos muestra lo siguiente.

`python androwarn.py -i C:\Users\corti\Documents\apks\brainly.apk -v 3 -r html`

La aplicacion toma datos relacionados al proveedor de servicio, codigo de pais, el tipo de dispositivo, numero registrado, estado de la SIM card.

![](/images/modulo7.2/img34.png)


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

**API SWRVE**

Esta API permite hacer varias llamadas de tipo REST para enviar la data de eventos desde la app.

**API Branch**

Branch es una API que se encarga de registrar varias metricas de la aplicacion con propositos de mejora en su servicio de marketing, ademas se encarga que los enlaces a los que apunta la aplicacion verdaderamente se dirijan a donde deben apuntar. En el caso de esta API su key estaba disponible en el archivo strings.xml pero no su **SECRET** por lo tanto no se pudo ingresar con el payload.

`https://api2.branch.io/v1/app/KEY_HERE?branch_secret=SECRET_HERE`

**API de Google**

De acuerdo a la documentacion cuando se utilicen claves de API en las aplicaciones, se debe garantizar que esten seguras durante el almacenamiento y la transmisión. Si se expone estas credenciales de forma pública se pone en riesgo la cuenta y se pueden generar acciones inesperados en ella. 

Realizando una busqueda sobre como explotar estas API se encontro algunos payloads en https://github.com/streaak/keyhacks#Google-Maps-API-key y probandolos se mostraron resultados indicando que la API estaba restringida para su acceso. Con el servicio que se obtuvo un resultado diferente y se obtuvo mas metadata fue con **Roads**


**Servicio Geocoding y demas servicios**

![](/images/modulo7.2/img44.png) 

**Servicio Roads**

![](/images/modulo7.2/img43.png)


**Análisis de vulnerabilidad según OWASP Mobile Top 10**

**M1: Improper Platform Usage Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

Este ataque corresponde a los mismos vectores de ataque que el OWASP TOP 10 tradicional, en donde una API expuesta puede servir como vector de ataque.

**Impactos tecnicos**

De acuerdo a la guia de seguridad de APIs de Google se menciona que exponer las APIS en el codigo se considera un riesgo ya que les da la posibilidad a los atacantes de ingresar a la informacion que se gestiona usando dicha API si la aplicacio no esta protegida, en este caso la API estaba restringida y no se pudo observar mayor detalle, en el servicio Roads se expone metadata acerca del uso de la plataforma y muestra el id del proyecto, el problema es que el archivo de configuracion no se encontraba en la aplicacion y no se pudo explotar esta API sin tener conocimiento de la cuenta de gmail con la que habia registrado la API google_api_key

**Impactos de negocio**

En el caso que se haya conseguido acceso a la API esto provocaria que los datos almacenados en la API sean expuestos a terceras personas.


**/res/values/public.xml**

Valores referenciados de strings en la aplicacion

**/res/xml/file_paths**

[CHECKEAR COMO ACCEDER A ESTE DIRECTORIO]

![](/images/modulo7.2/img20.png)

**/res/xml/network_security_config.xml**

![](/images/modulo7.2/img21.png)

En esta configuracion se permite el trafico en texto plano para varios subdominios en la aplicacion.

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

**External Storage**

El almacenamiento externo puede ser accesado mediante **/storage/emulated/0**, estos archivos son de uso publico por lo que no se recomienda almacenar datos sensibles aqui, adicionalmente los datos almacenados pueden ser filtrados mediante el uso de los content providers si llegasen a estar expuestos. Brainly no tiene content providers expuestos por lo que no es posible filtrar esta informacion ni abusar de posibles SQL Injection.

**Analisis de almacenamiento con Objection**

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
  
+ google_app_measurement.db 

**/storage/emulated/0/Android/data/co.brainly/cache** -> no se encontro nada

**/storage/emulated/0/Android/obb/co.brainly** -> no se encontro nada

### Otras Funciones Interesantes

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

### Exploiting Backup

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

**Análisis de vulnerabilidad según OWASP Mobile Top 10**

**M10: Extraneous Functionality | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

Esta vulnerabilidad involucra la busqueda de funcionalidades extranas en la aplicacion para poder explotarlas dentro de los propios sistemas. Tener la opcion de AllowBackup:true en sistemas de produccion no es recomendable ya que si se llegasen a almacenar credenciales o informacion importante dentro de la aplicacion da la posibilidad a un atacante o a cualquier persona tener acceso a los datos de la aplicacion.

**Impactos tecnicos.**

Esta vulnerabilidad expone los datos almacenados en la aplicacion que puedan ser descargados usando una copia de seguridad o backup. Si bien no se hallaron datos que comprometan al usuario en las bases de datos obtenidas en el backup esta configuracion no es la adecuada en produccion porque permite el acceso a la informacion a cualquier persona.

**Impactos de negocio**

Brainly es una aplicacion de preguntas de varias asignaturas y con un publico objetivo variado esto debido a que abarca a usuarios de escuela, colegio y universidades. El lema de la empresa es "llevada por estudiantes apoyados por los padres", filtrar crendenciales de menores de edad ya de por si es grave. La empresa sufriria un dano reputacional e incluso demandas por parte de los padres si se llegase a conocer algun caso de filtracion de datos por terceras personas.

## Analisis Dinamico

### Bypassing Protections

**Bypass de Emulator detection**

Para lograr evitar esta validacion se debe usar **apktool** en el archivo *smali* de la clase d.e.x.c0.d

`apktool d brainly.apk`

![](/images/modulo7.2/img35.png)

Con la ayuda de un bloc de notas o cualquier editor de texto cambiamos los strings "generic" por "generic-info" y asi para todos los casos.

![](/images/modulo7.2/img36.png)

**Bypass de Root detection**

Aprovechando que ya se cuenta con los archivo smali se realiza un cambio en la clase d.g.c.h.d.j.g y se modifica los strings relacionados con root como es el caso de "/system/app/Superuser.apk" por cualquier string, usando la metodologia anterior se cambia su valor por "/system/app/Superuser-info.apk"

![](/images/modulo7.2/img37.png)

La nueva apk se guardara en la carpeta **dist**, esta apk no esta firmada por lo tanto no funcionara. Se realiza el proceso ya mencionado anteriormente en la otra aplicacion.

`keytool -genkey -v -keystore my-release-key.keystore -alias alias_name -keyalg RSA -keysize 2048 -validity 10000`

`jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore my-release-key.keystore brainly.apk alias_name`

### Analisis de Trafico con Burp Suite

Al evaluar la sesion de login de la aplicacion se observa que el trafico en texto plano se permite para esta api y por lo tanto son visibles cuando son interceptadas.

![](/images/modulo7.2/img38.png)

Un compartamiento en especial que se observa cuando se captura el trafico de la aplicacion es el uso de un captcha, a pesar que la aplicacion no emplea el mecanismo SSL Pinning como proteccion y las peticiones si son capturadas por el proxy, el captcha bloquea cuando se realizan varios intentos recurrentes en las peticiones, dando por terminada la aplicacion y mostrando una pantalla en blanco o en su defecto.

![](/images/modulo7.2/img39.png)

![](/images/modulo7.2/img41.png)

texto de las preguntas??/

![](/images/modulo7.2/img40.png)

### Analisis adb logcat

En el caso de logcat no se pudo obtener las credenciales  mediante los logs ya que todo se maneja a traves de la conexion a Gmail y generalmente esta API de terceros es un poco mas segura. En la imagen se muestra como se establece la conexion a Gmail con la cuenta de prueba **pruebamobile47@gmail.com** y luego se establece la sesion. 

![](/images/modulo7.2/img42.png)

### Analisis de DeepLinks

Durante este analisis se realiza una busqueda de funciones vulnerables en la activity donde se declaran los deeplinks en este caso **StartActivity**

+ `setJavaScriptEnabled(true)` puede producir XSS al llamar la aplicacion mediante un deeplink
+ `getQueryParameter('parameter')` puede producir algunos vectores de ataque como XSS, LFI.
+ `Runtime.getRuntime().exec()` puede producir RCE
+ `android:pathPattern ="/.*/"` puede producir ataques XSS

No se encontro un vector de entrada donde al ingresar con el siguiente comando se filtraran credenciales o contenido que no debe estar al alcance de los usuarios.

`adb shell am start -W -a android.intent.action.VIEW -d "http://brainly.pl/zadanie" co.brainly`

### Explotacion de componentes expuestos

**Activities exportadas**

Las siguientes activities estan exportadas como **true** para evaluar si en estas activities se expone algun dato vulnerable se usa el siguiente comando

`adb shell am start --component <package>/<activity>`

![](/images/modulo7.2/img29.png)

Las activities a pesar de estar exportadas estas apuntan a la activity principal y no exponen ningun dato.

**Broadcast Receivers exportados**

El primer servicio expuesto es com.google.firebase.iid.FirebaseInstaceIdReceiver, aqui se detalla lo siguiente si el primer intent existe entonces llama a un elemento parcelable con id "wrapped_intent", y luego al intent2 comprueba si el string es igual a google.com/iid.

![](/images/modulo7.2/img45.png)

Con el siguiente payload en Drozer se logra obtener una respuesta del servicio  aunque no expone datos sensibles.

`run app.broadcast.send --action  com.google.android.c2dm.intent.RECEIVE --component com.google.firebase.iid com.google.firebase.iid.FirebaseInstanceIdReceiver --extra parcelable wrapped_intent intent  --extra string from google.com/iid`

![](/images/modulo7.2/img46.png)

El segundo servicio expuesto es una implementacion de la API swrve que esta a la espera de las notificaciones, cuando ocurra un evento envia una notificacion a la aplicacion.

`run app.broadcast.send --component com.swrve.sdk com.swrve.sdk.SwrveNotificationEngageReceiver`

![](/images/modulo7.2/img47.png)

**Services exportados**




### Referencias

1. **Bypass Emulator Detection** https://www.juanurs.com/Bypassing-Android-Anti-Emulation-Part-I/ ,https://www.juanurs.com/Bypassing-Android-Anti-Emulation-Part-II/, https://www.juanurs.com/Bypassing-Android-Anti-Emulation-Part-III/
2. **Testing API Keys** https://github.com/streaak/keyhacks


