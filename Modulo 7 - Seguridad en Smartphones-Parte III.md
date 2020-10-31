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

**RECORD_AUDIO:** Permite a la aplicacion acceder al audio de la aplicacion. Este permiso es considerado como peligroso y es innecesario, en la aplicacion se utiliza la voz para indicar un producto que desee, a criterio personal no considero que sea la manera mas adecuada cuando ya se tiene la opcion de tomar foto del producto.

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

```xml
android:name="com.sic.android.wuerth.wuerthapp.WuerthApplication" android:allowBackup="true" android:supportsRtl="true" android:networkSecurityConfig="@xml/network_security_config" android:roundIcon="@mipmap/ic_launcher_round"
```

El backup de la aplicacion esta habilitado por lo tanto el usuario tiene acceso a esta configuracion que normalmente no deberia estar disponible en produccion. La configuracion de red permite limitar el uso a ciertos dominios o certificados, motivo por el cual tambien se realizara una busqueda de la informacion proporcionada en este archivo.


**Actividad Principal de la aplicacion**

```xml
<activity android:name="com.sic.android.wuerth.wuerthapp.views.StartActivity_" android:resizeableActivity="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
```

Como se puede observar la actividad principal es **com.sic.android.wuerth.wuerthapp.views.StartActivity** 

**HTTP Deeplink Scheme**

```xml
            
            <meta-data android:name="android.app.shortcuts" android:resource="@xml/shortcuts" />
            <intent-filter android:autoVerify="true">
                <data android:scheme="https" android:host="shop.recanorm.de" android:pathPattern=".*/.*\\.sku.*" />
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
            </intent-filter>
            <intent-filter android:autoVerify="true">
                <data android:scheme="https" android:host="shop.recanorm.de" android:pathPattern=".*/.*\\.cyid.*" />
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
            </intent-filter>
 ```
 
 Este es un DeepLink con varios componentes, para formar la URL se combinan scheme, host, Path y PathPrefix un ejemplo seria `https://shop.recanorm.de/.*.*\\.sku.*` 

**Custom Deep Link Schema**

 ```xml        
            <intent-filter>
                <data android:scheme="reca" />
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
            </intent-filter>
        </activity>
```
Este Deep link unicamente tiene un schema permitido **reca://**


**File Providers**

```xml
        <uses-library android:name="org.apache.http.legacy" android:required="false" />
        <provider android:name="android.support.v4.content.FileProvider" android:exported="false" android:authorities="project.apriljune.recanorm.fileprovider" android:grantUriPermissions="true">
            <meta-data android:name="android.support.FILE_PROVIDER_PATHS" android:resource="@xml/filepaths" />
        </provider>
```

**Activities**

```xml
        <activity android:name="com.sic.android.wuerth.wuerthapp.views.mainnavigation.MainActivity_" />
        <activity android:name="com.sic.android.wuerth.wuerthapp.views.branch.BranchNearbyActivity_" />
        <activity android:name="com.sic.android.wuerth.wuerthapp.views.termsofservice.TermsOfServiceActivity_" />
        <activity android:name="com.sic.android.wuerth.wuerthapp.views.checkout.CheckoutActivity_" />
        <activity android:name="com.sic.android.wuerth.wuerthapp.views.checkout.OrderSuccessActivity_" />
        <activity android:name="com.sic.android.wuerth.wuerthapp.views.shippingaddress.ShippingAddressManagerActivity_" />
        <activity android:name="com.sic.android.wuerth.wuerthapp.views.shippingaddress.ShippingAddressDetailActivity_" />
        <activity android:name="com.sic.android.wuerth.wuerthapp.views.settings.SettingsActivity_" />
        <activity android:name="com.sic.android.wuerth.wuerthapp.views.web.WebActivity_" />
        <activity android:name="com.sic.android.wuerth.wuerthapp.views.settings.ProductLanguageActivity_" />
        <activity android:name="com.sic.android.wuerth.wuerthapp.views.samedaycombination.SameDayCombinationActivity_" />
        <activity android:name="com.sic.android.wuerth.wuerthapp.views.summary.SummaryActivity_" />
        <activity android:name="com.sic.android.wuerth.wuerthapp.views.mainnavigation.FullscreenActivity_" />
        <activity android:theme="@style/Base.Theme.AppCompat" android:name="com.theartofdev.edmodo.cropper.CropImageActivity" />
        <activity android:name="com.sic.android.wuerth.wuerthapp.views.onboardingMigration.OnboardingMigrationParentActivity_" />
        <activity android:name="com.sic.android.wuerth.wuerthapp.helper.WuerthActivity_" />
        <activity android:theme="@style/Theme.Transparent" android:name="com.karumi.dexter.DexterActivity" android:launchMode="singleTask" />

```

**Services y Receivers**

```xml
        <meta-data android:name="android.support.VERSION" android:value="26.1.0" />
        <receiver android:name="com.sic.android.wuerth.wuerthapp.platformspecific.NotificationServiceImpl$NotificationReceiver" />
        <service android:name="com.sic.android.wuerth.wuerthapp.platformspecific.connectivity.ConnectivityChangeReceiverService" />
        <receiver android:name="com.sic.android.wuerth.wuerthapp.helper.general.GeofenceBroadcastReceiver" android:enabled="true" android:exported="true" />
        <service android:name="com.sic.android.wuerth.wuerthapp.platformspecific.GeofenceTransitionsIntentService" android:permission="android.permission.BIND_JOB_SERVICE" android:exported="true" />
        <service android:name="com.sic.android.wuerth.wuerthapp.helper.general.BranchUpdateService" />
```

**Uso de APIS**

```xml
<meta-data android:name="com.facebook.sdk.ApplicationId" android:value="@string/facebook_app_id" />
        <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="AIzaSyAio5bRtpLLsXgtoo50zB-Snf_hse36n7I" />
        <meta-data android:name="io.fabric.ApiKey" android:value="54596d37816006f22b138eb6141735fd5f5ce6a5" />
```

**Servicio de Firebase**


```xml
<provider android:name="com.google.firebase.perf.provider.FirebasePerfProvider" android:exported="false" android:authorities="project.apriljune.recanorm.firebaseperfprovider" android:initOrder="101" />
      <service android:name="com.google.firebase.components.ComponentDiscoveryService">
         <meta-data android:name="com.google.firebase.components:com.google.firebase.perf.component.FirebasePerfRegistrar" android:value="com.google.firebase.components.ComponentRegistrar" />
         <meta-data android:name="com.google.firebase.components:com.google.firebase.iid.Registrar" android:value="com.google.firebase.components.ComponentRegistrar" />
      </service>
```

**Otras integraciones con Google, Facebook y Firebase**

```xml

        <receiver android:name="com.google.android.gms.analytics.AnalyticsReceiver" android:enabled="true" android:exported="false" />
        <service android:name="com.google.android.gms.analytics.AnalyticsService" android:enabled="true" android:exported="false" />
        <service android:name="com.google.android.gms.analytics.AnalyticsJobService" android:permission="android.permission.BIND_JOB_SERVICE" android:enabled="true" android:exported="false" />
        <receiver android:name="com.google.android.gms.measurement.AppMeasurementReceiver" android:enabled="true" android:exported="false" />
        <receiver android:name="com.google.android.gms.measurement.AppMeasurementInstallReferrerReceiver" android:permission="android.permission.INSTALL_PACKAGES" android:enabled="true" android:exported="true">
            <intent-filter>
                <action android:name="com.android.vending.INSTALL_REFERRER" />
            </intent-filter>
        </receiver>
        <service android:name="com.google.android.gms.measurement.AppMeasurementService" android:enabled="true" android:exported="false" />
        <service android:name="com.google.android.gms.measurement.AppMeasurementJobService" android:permission="android.permission.BIND_JOB_SERVICE" android:enabled="true" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:permission="com.google.android.c2dm.permission.SEND" android:exported="true">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="project.apriljune.recanorm" />
            </intent-filter>
        </receiver>
        <service android:name="com.google.firebase.iid.FirebaseInstanceIdService" android:exported="true">
            <intent-filter android:priority="-500">
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT" />
            </intent-filter>
        </service>
        <activity android:theme="@android:style/Theme.Translucent.NoTitleBar" android:name="com.google.android.gms.common.api.GoogleApiActivity" android:exported="false" />
        <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
        <provider android:name="com.crashlytics.android.CrashlyticsInitProvider" android:exported="false" android:authorities="project.apriljune.recanorm.crashlyticsinitprovider" android:initOrder="100" />
        <receiver android:name="org.piwik.sdk.extra.InstallReferrerReceiver" android:exported="true">
            <intent-filter>
                <action android:name="com.android.vending.INSTALL_REFERRER" />
            </intent-filter>
        </receiver>
        <provider android:name="com.facebook.internal.FacebookInitProvider" android:exported="false" android:authorities="project.apriljune.recanorm.FacebookInitProvider" />
        <meta-data android:name="android.arch.lifecycle.VERSION" android:value="27.0.0-SNAPSHOT" />
    </application>

```

## Analisis Estatico

Con la herramienta Drozer se puede comprobar lo presentado anteriormente en MobSF donde se mecionaba que existian  que tan expuesta esta una aplicación, en este caso hay 0 activities, 4 broadcast receivers y 2 servicios que están exportados como true.

**Componentes de la aplicacion**
![](/images/modulo7.3/img5.png)

Para contrastar esta informacion se analiza la superficie de ataque con Drozer y se obtienen resultado similares con la excepcion que ahora aparece 1 activity exportada

**Superficie de exposicion**

![](/images/modulo7.3/img6.png)

**Activities exportadas para la aplicacion Reca**

![](/images/modulo7.3/img7.png)

**Broadcast receives para la aplicacion Reca**

![](/images/modulo7.3/img8.png)

**Services para la aplicacion Reca**

![](/images/modulo7.3/img9.png)

## Analisis de cifrados debiles

MobSF indica que la clase com/wuerthit/core/presenters/RecommendationsPresenterImpl.java utiliza un cifrado MD5 considerado debil por tener colisiones hash, revisando la clase mas detalladamente con jadx-gui se demuestra que efectivamente se usa el cifrado MD5 pero al usar **Find Usage** se observa que el algoritmo MD5 se aplica a `String a2 = a(this.i.a("preferences_customer_id", ""));` . El id de preferencia de consumo, no es considerado un dato sensible, los ids son considerados datos seudonimizados o informacion de identificacion no directa. Si bien es cierto se aplica un cifrado considerado debil, esto se aplica a un dato que no es considerado sensible por lo tanto no se considerara como vulnerabilidad.


```java
private static String a(String str) {
        try {
            MessageDigest instance = MessageDigest.getInstance("MD5");
            instance.update(str.getBytes(), 0, str.length());
            return new BigInteger(1, instance.digest()).toString(16);
        } catch (NoSuchAlgorithmException e2) {
            e2.printStackTrace();
            return null;
        }
    }
```

## Deteccion de dispositivo rooteado

Deteccion de root en la clase **io.fabric.sdk.android.services.common.CommonUtils**

```java
public static boolean g(Context context) {
        boolean f = f(context);
        String str = Build.TAGS;
        if ((!f && str != null && str.contains("test-keys")) || new File("/system/app/Superuser.apk").exists()) {
            return true;
        }
        File file = new File("/system/xbin/su");
        if (f || !file.exists()) {
            return false;
        }
        return true;
    }

```

## File Recon

**resources.arsc/res/values/strings.xml/**

```xml
<string name="default_web_client_id">61973707321-7o03fnukb2n72objb51l0egrqktem067.apps.googleusercontent.com</string>
<string name="facebook_app_id">1234567890123456</string>
<string name="firebase_database_url">https://recaapp-76cb9.firebaseio.com</string>
<string name="google_api_key">AIzaSyCCZ3btSUyPYkI9fd9HXU-t6tf9N5mTY2Y</string>
<string name="google_app_id">1:61973707321:android:bd409ad288883eaa</string>
<string name="google_crash_reporting_api_key">AIzaSyCCZ3btSUyPYkI9fd9HXU-t6tf9N5mTY2Y</string>
<string name="google_storage_bucket">recaapp-76cb9.appspot.com</string>
<string name="library_AndroidIconics_authorWebsite">http://mikepenz.com/</string>
<string name="library_AndroidIconics_libraryWebsite">https://github.com/mikepenz/Android-Iconics</string>
```

Como resultado de explorar el contenido del archivo strings.xml se encontro lo siguiente la url para acceder a Firebase, API Keys expuestas y un repositorio de Github de terceras personas que por el nombre de la pagina se puede deducir que se llama Mike Penz.

## Insecure Data and File Storage

**Firebase credentials**

En el archivo strings.xml se mostro la URL que corresponde a la base de datos firebase https://recaapp-76cb9.firebaseio.com . Para evaluar si la base de datos es de acceso publico se le agrega /.json a la URL.

**Base de datos no es de acceso publico**

![](/images/modulo7.3/img10.png)

La Url no es de acceso publico por lo tanto las configuraciones de seguridad de Firebase no tiene habilitado la opción ‘read permission’.

## Otras Funciones Interesantes

**Code Execution**

Si existiesen alguna de estas funciones se pueden aprovechar para ejecutar remotamente comandos. Dentro del codigo de Brainly no se encontraron.

+  Runtime.exec()
+  ProcessBuilder()
+  native code:system()

**Send SMS**

De igual manera si existiese funciones para enviar mensajes, estas funciones podrian estar dentro del codigo fuente. Brainly no tiene habilitado el permiso para enviar SMS ni tiene habilitada estas funciones.

    sendTextMessage
    sendMultipartTestMessage

**Native functions**

Algunas aplicaciones para dificultar aun mas la labor de ingenieria reversa al analista emplean funciones nativas. Brainly se basa en la ofuscacion como mecanismo de proteccion, ademas de utilizar una mezcla de librerias entre Kotlin y Java, en la busqueda de las funciones no se encontraron las funciones nativas public native, System.loadLibrary, System.load


