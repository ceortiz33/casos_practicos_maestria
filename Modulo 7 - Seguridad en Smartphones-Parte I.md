# Modulo 7 

## Enunciado

A lo largo de los contenidos se ha hablado de OWASP, análisis estático y análisis dinámico, vulnerabilidades, desarrollo seguro, etc. Los Smartphones no quedan libres de ataques. OWASP define un top 10 de riesgos en aplicaciones para dispositivos móviles

## Instrucciones

A partir de los contenidos estudiados y tras leer el enunciado anterior, analiza al menos tres aplicaciones (.apk) en busca de vulnerabilidades.

**Sugerencia**

+ Una apk no oficial, con vulnerabilidades deliberadamente introducidas, y que se publican en internet para prácticas de entrenamiento.
+ Una descargada de algún repositorio no oficial de apks.
+ Una descargada desde una tienda oficial.

Para el análisis se deben utilizar tanto herramientas stand-alone como online, de manera que produzcan resultados complementarios y completen un análisis exhaustivo de las aplicaciones.

Para las herramientas stand-alone pueden usarse las herramientas de la suite Androl4b: https://github.com/sh4hin/Androl4b

En cualquier caso, una vez seleccionadas las herramientas, se puede organizar el trabajo de análisis siguiendo las siguientes tareas:

1. Recolección de información (definir alcance y secciones a evaluar).
2. Análisis estático (observar recursos de la app, código fuente, ficheros de configuración, etc.).
3. Análisis dinámico (ejecutar la app y monitorizar la actividad).

Al finalizar las fases anteriores deberás discernir si existen algunas de las vulnerabilidades mas conocidas en estos dispositivos, como pueden ser: [CONCLUSION]

+ Hijacking y spoofing de intents.
+ Sticky broadcast tampering.
+ Almacenamiento inseguro.
+ Tráfico inseguro.
+ SQL injection.
+ Exceso de privilegios

Se deben clasificar las vulnerabilidades encontradas según OWASP-Mobile Top-10, y una valoración subjetiva de los riesgos asociados.

**Sugerencia**

Para ello se puede realizar un análisis en dos fases:

## Primera fase: análisis estático

+ Determinar el uso de ofuscadores de código.
+ Determinar si hay llamadas a funciones potencialmente insegura, detectando funciones que puedan ser peligrosas (y que históricamente se han relacionado con vulnerabilidades).
+ Analizar el fichero del manifiesto:
+ Declaraciones de intents, services, receivers, activities, content-providers.
+ Intercomunicación entre componentes, propiedad “exported”.
+ Permisos de componentes. En relación al tipo de app, ¿tiene permisos excesivos? ¿Tiene algún permiso que pueda ser sospechoso?

**Ejemplo**

+ Permiso: READ_SMS (usado en estafas de suscripción a servicios de mensajería, por ejemplo).
+ Receivers del tipo BOOT_COMPLETED. Los cuales podrían usarse para lanzar código malicioso cada vez que el terminal arranque.
+ Versión de SDK usada.
+ Propiedad “allowBackup” (permitiría realizar un backup de la aplicación mediante ADB, siempre que la propiedad de debugging USB esté habilitada).
+ Propiedad del “Flag debuggable” (¿está activada?).

## Segunda Fase: análisis dinámico

+ Análisis de las comunicaciones.
  + Mediante el uso de Burp Suite (como proxy) / wireShark o con el propio MobSF (mediante su módulo de análisis dinámico que permite visualizar las comunicaciones).          
  + Detección de fugas de información si no hay cifrado (Check MiTM).
+ Análisis del comportamiento de la aplicación. Interacción con la app para generar logs y comunicaciones si las hubiera.
+ Almacenamiento inseguro (también detectable a partir de los permisos en análisis estático).
  + Archivos almacenados en tarjetas externas pueden ser leídos por cualquier app.
  + sharedPrefs: puede ser extraído. Evitar fuga de información a partir de los recursos alojados en él (chequear rutas /data/data/<package>/: ).
  + Content-providers (gestionan el uso compartido de información entre componentes: ficheros, DB sqlite, servicios externos).
      
# Insecurebankv2(App de entrenamiento)

**Descripción de la aplicación**

Insecurebankv2 es una aplicación creada específicamente para entrenar a los desarrolladores o analistas de vulnerabilidades acerca de los problemas mas comunes encontrados en una aplicación y los peligros que conlleva no tener buenas practicas de programación durante el desarrollo de una aplicación móvil. 

Insecurebankv2 como su nombre lo indica es una aplicación que simula una aplicación bancaria, tiene procesos como transferir dinero, consultar datos, cambiar contraseña. Al ser una aplicación bancaria esta debe tener la mayor seguridad posible con los datos proporcionados por los clientes, es decir que no vayan en texto plano y no sean propensos a ser interceptados.

**Herramientas y Alcance**

Durante el análisis estático se buscará en el código fuente posibles fugas de información, texto hardcodeado con URLs, contraseñas, encriptación, código vulnerable, permisos excesivos y demás algoritmos que permitan a un atacante saltarse las protecciones de la aplicación. En esta etapa se usarán herramientas como apktool tanto para decompilar como para compilar la apk, el código que se produce de este proceso es Smali

El código en Java es más legible de leer y para este proposito se utiliza dex2jar que crea un fichero tipo java a partir de la apk, este archivo contiene todas las clases y elementos de la apk. Para visualizar el contenido de este archivo .jar se usa JD-GUI que no es más que una interfaz gráfica para código JAVA.

Durante el análisis dinámico se usarán herramientas como Burpsuite y Wireshark para ver el tráfico que atraviesa a través de la red, si este lleva cifrado o si los datos aparecen en texto plano o si existiese algún mecanismo de protección en la aplicación como SSL Pinning.

El entorno para el análisis de vulnerabilidades se hará en la máquina virtual Santoku que es una distribución dedicada al análisis de vulnerabilidades en Android y a Genymotion que es un emulador de un teléfono móvil.

**Maquina virtual Santoku en ejecucion**

![](/images/modulo7/img1.png)

**Emulador Genymotion en ejecucion**

![](/images/modulo7/img2.png)

Se descarga la apk del repositorio de Github y se procede a su instalación en el emulador con el comando.

`adb connect 192.168.100.97`

**Conexion establecida con Genymotion**

![](/images/modulo7/img3.png)

Una vez se ha enlazado al emulador con la aplicación, al abrirla aparece lo siguiente

**Insecurebankv2 instalada en Genymotion**

![](/images/modulo7/img4.png)

Se decompila la aplicación usando dex2jar y de esta manera obtener un archivo .jar que servirá para poder leer el código fuente

**Obtencion del fichero .jar**

![](/images/modulo7/img5.png)

**Codigo fuente de aplicación mediante interfaz grafica JD-GUI**

![](/images/modulo7/img6.png)

El manifiesto de Android puede ser obtenido usando apktool.

`apktool d Insecurebankv2.apk`

**Proceso de decompilacion de la aplicación usando apktool**

![](/images/modulo7/img7.png)

**Android manifest de Insecurebankv2**

![](/images/modulo7/img8.png)

## Evaluación de permisos

**INTERNET:** Es uno de los permisos mas comunes y como su nombre indica provee acceso a internet a la aplicación, su uso no representa un riesgo. Es necesario ya que la aplicación se conecta a Internet.

**WRITE_EXTERNAL_STORAGE:** Permite a una aplicación escribir en el almacenamiento externo, esta catalogada como peligroso, a partir de la API 19 este permiso ya no es requerido para leer o escribir archivos en los directorios especificos de la aplicación. En el caso de usarse este permiso se debe validar si no queda ningun dato de algun usuario almacenado dentro de la memoria SD especialmente tratandose de una aplicación bancaria.

**SEND_SMS:** Este permiso permite a la aplicación enviar mensajes de texto, es considerado como peligroso y no puede ser utilizado por la aplicación hasta que el usuario  lo envie a la lista blanca. Para la recuperacion de contrasenas y transferencias es normal usar mensajeria sms o correo electronico, sin embargo si no esta correctamente sanitizado puede enviar informacion adicional que puede conllevar a la filtracion de informacion.

**USE_CREDENTIALS:** La aplicacion crea un token al momento de usar las credenciales para ingresar a la sesion.

**GET_ACCOUNTS:** Permite el acceso a las cuentas en el servicio de cuentas, este permiso es considerado peligroso. En el caso de esta aplicación no usa ninguna API de terceros como puede ser el caso de las cuentas de Google o Facebook para que pueda ser considerado para la autenticacion por esta razon su uso no es recomendado porque no se sabe que tipo de tratamiento tendran los datos de los usuarios.

**READ_PROFILE:** Permite leer el perfil del usuario, es un permiso que fue removido en la API 23, se considera peligroso.

**READ_CONTACTS:** Permite leer los contactos que tiene guardado en el dispositivo, Este permiso es considerado como peligroso, este permiso podria ser utilizado comunmente en alguna aplicación de mensajeria donde aquí si se necesita de los usuarios para sincronizacion, pero en el caso de una aplicación bancaria donde la relacion es entidad bancaria y usuario final, es un permiso innecesario.

**READ_PHONE_STATE:** Permite acceso de solo lectura al estado del telefono incluyendo informacion de la red, estado de las llamadas en curso. Es considerado como peligroso y solo se puede acceder una vez que el usuario lo ha mandado a la lista blanca luego de permitirlo. Es un permiso innecesario porque proporciona datos sensible que no deberian ser utilizados para el funcionamiento de la aplicación bancaria.

**READ_EXTERNAL_STORAGE:** Permite leer el almacenamiento externo, este permiso se otorga implicitamente a raiz de declarar el permiso WRITE_EXTERNAL_STORAGE. Es considerado como peligroso.

**READ_CALL_LOG:** Permite a la aplicación leer el registro de llamadas, es considerado como peligroso. Este permiso es innecesario ya que una aplicación bancaria no require del registro de llamadas para funcionar y resultaria en una invasion a la privacidad de los usuarios.

**ACCESS_NETWORK_STATE:** Permite el acceso a la informacion acerca de las redes, esta catalogada como normal, es uno de los permisos mas comunes.

**ACCESS_COARSE_LOCATION:** Permite a la aplicación obtener una ubicación aprox. del usuario. Es considerado como peligroso, este permiso podría ser de utilidad si lo que se quiere tener un registro desde donde se están haciendo las transferencias y de igual manera que otros permisos debe ser permitido por el usuario. 


**Evaluación de exposición de la aplicación con Drozer**

El parámetro android:exported debe estar siempre en falso a menos que sea necesario, cuando este parámetro es true puede provocar varios fallos de seguridad y exposición innecesaria de información. Sin embargo, en el caso de que no haya otra opción como en el caso de un servicio de terceros cuyo requerimiento sea que se exponga ese servicio se debería dar permisos personalizados para que solamente ciertas activities tengan acceso a la aplicación y no cualquier agente externo.

**Nombre del paquete de Insecurebankv2**

![](/images/modulo7/img9.png)

**Superficie de ataque de la aplicación Insecurebankv2**

![](/images/modulo7/img10.png)

**Activity expuestas de la aplicación Insecurebankv2**

![](/images/modulo7/img11.png)

**Broascast receiver expuesto en la aplicación Insecurebankv2**

![](/images/modulo7/img12.png)

**Content providers expuestos en la aplicación Insecurebankv2**

![](/images/modulo7/img13.png)

De acuerdo al resultado de Drozer hay 5 activities, 1 content provider y 1 broadcast receiver que estan expuestos, esto no necesariamente indica vulnerabilidad solo que estan expuesto a la manipulacion de terceros si no estan validados correctamente, los nombres de las clases de cada una de estos componentes es un buen punto de partida al momento de analizar una aplicación, sin embargo no son los unicos puntos a tomar en cuenta, tambien se debe realizar un analisis manual y del funcionamiento de la aplicación para descartar todas las posibilidades.

## Analisis Estatico

**Parchando una aplicacion android**

Una vez decompilada la aplicación se busca entre los varios directorios al directorio /InsecureBankv2/res/values, dentro de este se localiza el archivo strings.xml y se modifica el parametro en lugar de no por yes.

**Cadenas de caracteres dentro del archivo strings.xml**

![](/images/modulo7/img14.png)

Luego de realizar el cambio se reconstruye la aplicación con el comando.

`apktool b InsecureBankv2 -o newapp.apk`

**Compilación de la aplicación modificada**

![](/images/modulo7/img15.png)

Como se compilo nuevamente esta aplicación, para que sea aceptada por el emulador se necesita de una firma(signature) o key para que funcione correctamente y para esto se ejecutan los siguientes comandos.

`keytool -genkey -v -keystore my-release-key.keystore -alias alias_name -keyalg RSA -keysize 2048 -validity 10000`

`jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore my-release-key.keystore newapk alias_name`

Finalmente, luego de instalar la aplicación en Genymotion la aplicación cambia de la siguiente manera.

**Resultado de la modificacion en archivo strings.xml**

![](/images/modulo7/img16.png)

Como se puede ver en la imagen anterior cuando se realizó ese cambio en el archivo strings.xml en **is_admin** se le dio al usuario final la posibilidad de crear un nuevo usuario, dicha opción que solo debería tenerla un administrador.

**Análisis de vulnerabilidad según OWASP Mobile Top 10**

**M8: Code Tampering | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE |Impact SEVERE**

Esta vulnerabilidad se produce cuando se realizan cambios al código mediante formularios maliciosos, o cambios directos en los binarios de la aplicación. En este caso se parcho la aplicación para que el valor del string **is_admin** sea true, de esta manera se produjo un cambio en la aplicación dando como resultado la aparición de un botón que permite crear un nuevo usuario, es decir es una característica propia de un administrador mas no el usuario común.

**Impactos Técnicos**

Un impacto técnico que podría darse es el acceso a nuevas características no autorizadas como la creación de nuevos usuarios, tratándose de una aplicación bancaria el usuario común no debería estar en capacidad de crear una nueva cuenta ya que el banco es la entidad que le proporciona una a cada usuario.

**Impactos de Negocio**

Un impacto de negocio que podría darse es la perdida reputacional debido a la piratería, si un atacante esta en capacidad de crear cuentas para muchos usuarios, este podría crear una red de comercialización solicitando un pago para crear las cuentas sin necesidad de pasar por la entidad bancaria.

**M9: Reverse Engineering | Exploitability EASY | Prevalence COMMON | Detectability EASY | Impact MODERATE**

Esta es una de las vulnerabilidades mas comunes que existen para las aplicaciones, debido a la naturaleza de Android es sencillo obtener el código fuente de una aplicación y analizar sus componentes, en este caso se produce una modificación en la tabla de strings misma que se obtuvo cuando se uso apktool para decompilar la aplicación.

**Impactos Técnicos**

Un impacto técnico que podría darse es ganar inteligencia para realizar modificación de código, ya que si no se hubiera realizado la ingeniería inversa y obtenido la tabla de strings no se sabría que valor es el que había que modificar.

**Impactos de Negocio**

Como consecuencia de la ingeniería inversa se pueden producir los siguientes perjuicios a la entidad bancaria como daño reputacional por no tener una aplicación robusta que impida la creación de nuevos usuarios.

**Bypass de Root Detection mediante Smali**

Una aplicacion apk no es más que un archive zip, por lo tanto, una aplicación puede ser extraída usando unzip.

`unzip Insecurebankv2.apk`

En ocasiones este método puede revelar credenciales ocultas que puedan aparecer en la aplicación.

**Proceso de extracción de Insecurebankv2.apk usando unzip**

![](/images/modulo7/img17.png)

**Archivos generados luego de descomprimir con unzip**

![](/images/modulo7/img18.png)

Como siguiente paso, se utiliza dex2jar en el archivo classes.dex para generar un archivo .jar para que pueda ser leído por JD-GUI. Dentro de JD-GUI se busca la clase PostLogin y dentro de ella se encuentra el método showRootStatus()

**Clase PostLogin mostrado en JD-GUI**

![](/images/modulo7/img19.png)

Se decompila la aplicación usando apktool, una de las particularidades de apktool es que genera código Smali que puede ser de gran utilidad al momento de realizar ingeniería inversa a aplicaciones móviles.

`apktool d Insecurebankv2.apk `

**Decompilado de la aplicación usando apktool**

![](/images/modulo7/img20.png)

Al explorar un poco dentro de los directorios se encuentra el fichero PostLogin.smali dentro de /smali/com/android/Postlogin.smali

**Codigo Smali generado por apktool**

![](/images/modulo7/img21.png)

Para abrir estos archivos se necesita de un editor de texto, en este caso se utilizará nano para modificar y less para visualizar.

**Contenido del metodo showRootStatus en codigo Smali**

![](/images/modulo7/img22.png)

En la siguiente parte de la captura se muestra un segmento del código que indica una condición a cumplir y como resultado el dispositivo muestre “Device is rooted”

**Condición de evaluación de root detection encontrada en método showRootStatus()**

![](/images/modulo7/img23.png)

En este segmento de código hay una condición que indica que si no se cumple se produce un salto a cond_2, donde cond_2 muestra un string “Device not Rooted!!”

Para realizar el bypass de la validación se debe hallar una forma que siempre salte al bloque de la cond_2, para esto se escribe la siguiente línea.

**Cambio realizado en archivo PostLogin.smali**

![](/images/modulo7/img24.png)

Luego de hacer el cambio se tiene que recompilar la aplicación, de igual manera con apktool pero con la opción b, como siguiente paso se debe firmar la aplicación para que esta no de problemas al momento de instalarla en el dispositivo.

**Proceso de firma de la aplicación I**

![](/images/modulo7/img25.png)

**Proceso de firma de la aplicación II**

![](/images/modulo7/img26.png)

Una vez que la aplicación modificada sea firmada se instala en Genymotion. Se ingresa con las credenciales dinesh/Dinesh@123$ y como resultado se observa un cambio en el mensaje de “rooted device” por el de “device not rooted”.

**Ejecucion de la aplicación luego de cambiar PostLogin.smali**

![](/images/modulo7/img27.png)

**Análisis de vulnerabilidad según OWASP Mobile Top 10**

**M1: Improper Platform Usage | Exploitability EASY | Prevalence COMMON |Detectability AVERAGE | Impact SEVERE**

Esta categoria contempla el mal uso de las caracterisiticas de la plataforma o fallo en los mecanicmos de seguridad. En esta vulnerabilidad se realiza el bypass de la deteccion de root que es un mecanismo de seguridad que impide funcionar a ciertas aplicaciones cuando los dispositivos estan rooteados. 

**Impactos tecnicos**

Los impactos técnicos para esta vulnerabilidad implican un impacto técnico basado en la guía de OWASP Top Ten. En este caso sería una mayor exposición de la aplicación al verse vulnerados sus mecanismos de seguridad.

**Impactos de negocio**

Un impacto de negocio de esta vulnerabilidad seria el mal uso de los usuarios, al saltarse las restricciones de seguridad y por lo tanto danando la reputacion institucional de la entidad bancaria.

**M9: Reverse Engineering | Exploitability EASY | Prevalence COMMON | Detectability EASY | Impact MODERATE**

Esta es una de las vulnerabilidades más comunes que existen para las aplicaciones, debido a la naturaleza de Android es sencillo obtener el código fuente de una aplicación y analizar sus componentes, en este caso se produce una modificación en el código Smali misma que se obtuvo cuando se usó apktool para decompilar la aplicación.

**Impactos Técnicos**

Un impacto técnico que podría darse es ganar inteligencia para realizar modificación de código, ya que si no se hubiera realizado la ingeniería inversa y obtenido los archivos .smali no se sabría que clases son las que representan un mayor valor para que un atacante pueda explotarlas y de esta manera conocer que parámetro debe modificar.

**Impactos de Negocio**

Como consecuencia de la ingeniería inversa se pueden producir los siguientes perjuicios a la entidad bancaria como daño reputacional por no tener una aplicación lo suficientemente protegida que limite el acceso a las funcionalidades de seguridad como lo es la detección de root.

**Developer backdoor**

Dentro de la clase DoLogin existe una condicion que permite obtener acceso a la aplicación sin necesidad de ingresar usuario y contrasena, como se puede observar en el siguiente segmento de codigo. Si el usuario es igual a “devadmin” entonces se puede realizar un bypass de las credenciales.

**Usuario devadmin localizado dentro de la clase DoLogin**

![](/images/modulo7/img28.png)

**Bypass de autenticacion en Insecurebankv2**

![](/images/modulo7/img29.png)

**Análisis de vulnerabilidad según OWASP Mobile Top 10**

**M4: Insecure Authentication | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

Esta vulnerabilidad se produce cuando se realiza un bypass de la autenticacion admitiendo solicitudes del backend, en este caso se produce un bypass de la autenticacion porque se encuentra un usuario que tiene privilegios tales que solo ingresando el usuario se ingresa a la sesion del usuario.

**Impactos tecnicos**

Teniendo acceso con el usuario devadmin, la aplicación no es capaz de llevar un registro de la identidad del usuario que esta haciendo la peticion por lo tanto hace que sea mas dificil de detectar el origen.

**Impactos de negocio**

Entre los impactos de negocio estan el acceso no autorizado a informacion, robo de informacion y dano reputacional. Un atacante luego de haber hecho el bypass tiene via libre para realizar transferencias de diversas cuentas sin poder ser localizado, ademas de perjudicar tanto a los clientes como a la entidad financiera que perderia credibilidad por no ser capaz de llevar a cabo mecanismos de seguridad que protejan  a la aplicación.

**M6: Insecure Authorization | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

Esta vulnerabilidad se produce cuando existen esquemas pobres de autorizacion , por lo que si un usuario consigue hacerse con privilegios mas altos podria acceder a funcionalidades ocultas, en este caso se produce algo similar debido a que el usuario encontrado en la clase DoLogin tiene estas caracteristicas. 

**Impactos tecnicos**

Tiene un impacto similiar a la autenticacion pobre, malos sistemas de validacion de sistemas de autorizacion pueden producir funcionalidades con excesivos privilegios como es el caso del usuario devadmin.

**Impactos de negocio**

Para una aplicación bancaria como insecurebankv2 que sus clientes o un atacante tengan acceso a la cuenta sin necesidad de una validacion representa una amenaza para la entidad bancaria ya que podria fraude  y un perjuicio a la reputacion por no asegurar bien los sistemas de proteccion de la aplicación.

**M9: Reverse Engineering | Exploitability EASY | Prevalence COMMON | Detectability EASY | Impact MODERATE**

Esta es una de las vulnerabilidades más comunes que existen para las aplicaciones, debido a la naturaleza de Android es sencillo obtener el código fuente de una aplicación y analizar sus componentes, en este caso se obtuvo información referente a la autenticación y autorización de la aplicación obteniendo un usuario que tiene privilegios para ingresar sin necesidad de una contraseña.

**Impactos Técnicos**

Un impacto técnico que podría darse es ganar inteligencia para poder ingresar a la sesión ya que si no se hubiera realizado la ingeniería inversa y obtenido el usuario devadmin no se podría ingresar.

**Impactos de Negocio**

Como consecuencia de la ingeniería inversa se pueden producir los siguientes perjuicios a la entidad bancaria como daño reputacional por no tener una aplicación lo suficientemente protegida que valide que no haya strings o parámetros dentro del código que permitan el acceso a la aplicación de manera indebida.

**Exploiting Android Backup Functionality**

Dentro del archivo del manifiesto de Android se puede observar que la aplicación es debugeable, esto declarado mediante android:debuggable=”true”

**Manifiesto de Android mostrando que la aplicación es debugeable**

![](/images/modulo7/img30.png)

Se accede a la aplicación con las credenciales dinesh/Dinesh@123$ y a continuación se ejecuta el siguiente comando para hacer un backup de la aplicación.

`adb backup -apk -shared com.android.insecurebankv2`

**Backup de la aplicación mediante adb.**

![](/images/modulo7/img31.png)

Como indica en la imagen anterior se necesita una confirmación por parte de la aplicación 

**Confirmacion del Backup en la aplicación.**

![](/images/modulo7/img32.png)

Luego se da un click en Back up my data. Si todo ha sido correcto entonces aparecerá un nuevo archivo dentro del directorio en la máquina virtual. El archivo que se crea es backup.ab

**Archivo de backup de Insecurebankv2**

![](/images/modulo7/img33.png)

Para poder abrir este archivo se necesita pasarlo a otro formato de compresión como .tar o .zip y luego extraer usando el formato de archivo adecuado.

**Proceso de Extraccion del archivo de backup**

![](/images/modulo7/img34.png)

Dentro del directorio /home/santoku/Desktop/pruebas_dir/InsecureBankv2/apps/com.android.insecurebankv2/sp

Se hallan varios archivos interesantes como:

+	com.android.insecurebankv2_preferences.xml 
+	mySharedPreferences.xml

En el archivo com.android.insecurebankv2_preferences.xml se encuentra la dirección y el puerto desde al que se conecta la aplicación.

**Informacion de la red del usuario de la aplicación**

![](/images/modulo7/img35.png)

mySharedPreferences.xml contiene las credenciales de usuario y contrasenia de la sesion de login al abrir la aplicación.

**Credenciales de usuario y contrasena de la aplicación.**

![](/images/modulo7/img36.png)

En este caso las credenciales están cifradas, sin embargo, puede darse el caso que no sea así. esta aplicación es vulnerable debido a que el cifrado que usa a pesar de ser fuerte, tiene key hardcodeada es decir en texto plano dentro del código.

La clase CryptoClass.class  muestra la key “This is the super secret key 123”, corresponde a un cifrado aes256 utiliza CBC y PKCCSpadding.

**Clave en texto plano del cifrado AES256**

![](/images/modulo7/img37.png)

Usando un sitio web con la key y la clave cifrada se obtiene el siguiente resultado 

**Contraseña desencriptado usando la clave en texto plano de la clase CryptoClass**

![](/images/modulo7/img38.png)

**Análisis de vulnerabilidad según OWASP Mobile Top 10**

**M1: Improper Platform Usage | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

Esta categoria contempla el mal uso de las caracterisiticas de la plataforma o fallo en los mecanismos de seguridad. En esta vulnerabilidad se realiza el backup de la aplicación que es un mecanismo que no deberia estar activo en produccion por motivos de seguridad ya que podrian haber credenciales ocultas y demas informacion sensible.

**Impactos tecnicos**

Los impactos técnicos para esta vulnerabilidad implican un impacto técnico basado en la guía de OWASP Top Ten. En este caso sería una mayor exposición de la aplicación al tener el modo debug activo y eso implica que el atacante tiene un mayor control de la aplicación para testear y buscar fallos.

**Impactos de negocio**

Un impacto de negocio de esta vulnerabilidad seria el dano reputacional ya que da a entender que sus aplicaciones no son lo suficiente seguras y no cuentan con mecanismo propios de una entidad bancaria, el modo debug activo implica un mayor exposicion y por lo tanto si esta aplicación viene de una tienda de aplicaciones como Play Store muchos usuarios serian los afectados por este fallo.

**M5: Insufficient Cryptography | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

Esta vulnerabilidad implica que la encriptacion ha sido utilizada de manera inapropiada haciendola muy debil y perdiendo su proposito que es dificultar el trabajo a los atacantes de obtener las credenciales en texto plano, en este caso ocurre debido  a que a pesar de que la aplicación cuenta con una encriptacion AES256 considerada como segura, pero expone la key  en texto plano dentro de la misma clase.

**Impactos tecnicos**

Esta vulnerabilidad resulta en la filtracion de informacion sensible debido a la poca proteccion de dicha informacion y de esta manera ganando acceso a los procesos de autenticacion de la aplicación.

**Impactos de negocio**

Los impactos que tiene esta vulnerabilidad son los siguientes: Robo de Informacion debido al cifrado tan debil que tienen los datos de los clientes, Violacion de privacidad es decir si un atacante tiene las credenciales  de un cliente puede conocer que transacciones ha realizado, cuanto ha transferido, depositado, etc. Ademas se produciria un dano reputacional a la entidad bancaria por no tener las protecciones adecuadas de los datos de sus clientes.

**M7: Poor Code Quality | Exploitability DIFFICULT | Prevalence COMMON | Detectability DIFFICULT | Impact MODERATE**

Se produce por codigo no intencional que si bien no representa una vulnerabilidad como tal si puede llegar repercutir en otras vulnerabilidades, como en este caso donde la key para desencriptar estaba en texto plano en la misma clase donde se realiza la encriptacion esto anula la proteccion que brindaba esta encriptacion.

**Impactos Tecnicos**

En este caso la encriptacion se ve anulada debido a que la key esta en texto plano y eso facilita la recuperacion de la contrasena.

**Impactos de negocio**

Como consecuencia de tener la key en texto plano el atacante puede robar informacion de los clientes que esten almacenados en la base de datos ya que su encriptacion es facil de romper, el dano reputacional de la entidad bancaria se veria comprometido al saber que no sigue buenas practicas que la industria le imponen

**M9: Reverse Engineering | Exploitability EASY | Prevalence COMMON | Detectability EASY | Impact MODERATE**

Esta es una de las vulnerabilidades más comunes que existen para las aplicaciones, debido a la naturaleza de Android es sencillo obtener el código fuente de una aplicación y analizar sus componentes, en este caso se obtuvo información referente a la key que permite desencriptar la aplicación.

**Impactos Técnicos**

Un impacto técnico que podría darse es ganar inteligencia para poder romper la encriptacion ya que si no se hubiera realizado ingeniería inversa no se sabría de la key que puede descifrar las contraseñas.

**Impactos de Negocio**

Como consecuencia de la ingeniería inversa se pueden producir los siguientes perjuicios a la entidad bancaria como daño reputacional por no tener una aplicación lo suficientemente protegida que valide que no haya credenciales en texto plano dentro de las clases de la aplicación y permita descifrar las contraseñas.

**M4: Insecure Authentication | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

Esta vulnerabilidad se produce cuando se realiza un bypass de la autenticacion admitiendo solicitudes del backend, en este caso se produce indirectamente porque la encriptacion se rompe al tener la key en texto plano, y con las credenciales encontradas luego de realizar el backup dan como resultado el usuario y contrasena. Con estos datos ya se puede ingresar como si fuera un usuario legitimo y suplantar su identidad.

**Impactos tecnicos**

Teniendo las credenciales el atacante puede entrar en la aplicación y suplantar la identidad de un usuario legitimo, la identidad del usuario no podria ser registrada correctamente ya que tecnicamente es el mismo usuario que esta haciendo esas transacciones.

**Impactos de negocio**

Entre los impactos de negocio estan el acceso no autorizado a informacion, suplantacion de identidad, robo de informacion y dano reputacional. Un atacante luego de tener las credenciales de un usuario tiene control total para realizar transferencias de diversas cuentas sin poder ser localizado, ademas de perjudicar tanto a los clientes como a la entidad financiera que perderia credibilidad por no ser capaz de llevar a cabo mecanismos de seguridad que protejan  a la aplicación.

## Análisis Dinámico

**Bypass Autenthication via adb**

Con el siguiente comando se puede realizar un bypass del login sin necesidad de ingresar el usuario ni la contraseña.

`adb shell am -n com.android.insecurebankv2/com.android.insecurebankv2.PostLogin`

**Bypass de la clase PostLogin**

![](/images/modulo7/img39.png)

**Análisis de vulnerabilidad según OWASP Mobile Top 10**

**M4: Insecure Authentication | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

Esta vulnerabilidad se produce cuando se realiza un bypass de la autenticacion admitiendo solicitudes del backend, se produce porque la activity PostLogin esta exportada como true y no tiene validaciones de seguridad que impidan el acceso desde otros sitios o aplicaciones, por lo tanto usando adb se puede saltar la validacion de la autenticacion sin necesidad de ingresar credencial alguna.

**Impactos tecnicos**

No se puede llevar a cabo un registro de los usuarios conectados por el motivo que se salta la validacion de autenticacion sin ingresar alguna credencial.

**Impactos de negocio**

Entre los impactos de negocio estan el acceso no autorizado a informacion y dano reputacional. Un atacante puede acceder como cliente del banco y solicitar transferencias a su nombre. La entidad financiera perderia credibilidad por no ser capaz de llevar a cabo mecanismos de seguridad que protejan  a la aplicación.

**Android Debugging using jdwp**

JDWP es un debugger antiguo para analizar código Java, se puede utilizar con adb usando el comando adb jdwp, para comenzar se toma nota de todos los índices que aparecen en la pantalla sin abrir la aplicación en Genymotion.

**IDs de las aplicaciones definidas aleatoriamente por jdwp**

![](/images/modulo7/img40.png)

Ahora se ejecuta el mismo comando pero ahora abriendo InsecureBankv2 en Genymotion  

**Nuevo indice en jdwp luego de ejecutar nuevamente el comando anterior**

![](/images/modulo7/img41.png)

Comparando las dos imágenes anteriores aparece el ID 2530, mismo que corresponde al ID que se le ha asignado a Insecurebankv2.

Con este nuevo ID se ejecuta de manera remota jdwp, para esto se necesita de una nueva sesión de escucha al puerto 12345 y luego ejecutar este puerto en el servidor local con jdb usando los siguientes comandos.

`adb forward tcp:12345 jdwp:2530`
`jdb -attach localhost:12345`

Con el debugger en funcionamiento ahora se procede a listar las clases con el comando

`> classes`

**Ejecucion de jdwp en la consola**

![](/images/modulo7/img42.png)

Dentro del listado aparecerán varias opciones relacionadas a bases de datos, librerías de java, entre otras. Ahora se ingresan las credenciales de usuario dinesh y la contraseña Dinesh@123$ que están descritas en la documentación de la aplicación.

Nuevamente en la shell de jdb se escribe el siguiente comando:

`> Methods com.android.insecurebankv2.PostLogin`

**Metodos de la clase PostLogin**

![](/images/modulo7/img43.png)

Uno de los metodos que parecen interesantes para  comprometer es showRootStatus() que como su nombre lo indica podria mostrar una deteccion de root.

**Método showRootStatus() dentro de la clase PostLogin.**

![](/images/modulo7/img44.png)

Para hacer un breakpoint en ese metodo se ejecuta el comando

`> stop in com.android.insecurebankv2.PostLogin.showRootStatus()`

**Breakpoint en el metodo PostLogin.**

![](/images/modulo7/img45.png)

Para moverse por  cada linea se escribe *step* y para ver las variables *locales* se escribe locals

Al llegar a la line=88 bc1=16 se localiza la variable isrooted=true

**Variable isrooted localizada dentro del metodo showRootStatus()**

![](/images/modulo7/img46.png)

Se cambia el valor de esta variable de **true** a **false**

**Variable modificada en el metodo showRootStatus()**

![](/images/modulo7/img47.png)

Para continuar con la ejecucion, se escribe run produciendo el siguiente resultado.

**Bypass de Root Detection en Insecurebankv2**

![](/images/modulo7/img48.png)

Como se pudo observar mediante el debugger de java, jdb, se pudo realizar el bypass de la detección de root. Esto es posible debido a que en el manifiesto de la aplicación está disponible la opción de debug, si se quisiera deshabilitar el debug, esta opción debería tener el valor de false.

**Análisis de vulnerabilidad según OWASP Mobile Top 10**

**M1: Improper Platform Usage | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

Esta categoria contempla el mal uso de las caracterisiticas de la plataforma o fallo en los mecanismos de seguridad. En esta vulnerabilidad se realiza el backup de la aplicación que es un mecanismo que no deberia estar activo en produccion por motivos de seguridad ya que podrian haber credenciales ocultas y demas informacion sensible. Para ello en este caso se emplea jdwp que es un debugger de java para detectar los cambios en la aplicación.

**Impactos tecnicos**

Los impactos técnicos para esta vulnerabilidad implican un impacto técnico basado en la guía de OWASP Top Ten. En este caso sería una mayor exposición de la aplicación al tener el modo debug activo y eso implica que el atacante tiene un mayor control de la aplicación para testear y buscar fallos como es el caso de esta aplicación donde con jdwp se encontró una clase con un método vulnerable y al cambiar una variable se logra superar el root detection.

**Impactos de negocio**

Un impacto de negocio de esta vulnerabilidad seria el dano reputacional ya que da a entender que sus aplicaciones no son lo suficiente seguras y no cuentan con mecanismo propios de una entidad bancaria, el modo debug activo implica un mayor exposicion y por lo tanto si esta aplicación viene de una tienda de aplicaciones como Play Store muchos usuarios serian los afectados por este fallo.

**M10: Extraneous Functionality | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

Esta vulnerabilidad involucra la búsqueda de funcionalidades extrañas en la aplicación para poder explotarlas dentro de los propios sistemas. En este caso se focaliza en la configuración propia de la aplicación que al iniciarse genera un nuevo ID, luego el debugger utiliza el nuevo ID para conectarse y a partir de ahí buscar clases interesantes como la clase PostLogin y posteriormente su método showRootStatus().


**Impactos técnicos.**

Esta vulnerabilidad expone como funciona los sistemas de backend debido a esto se permite saltar las protecciones del root detection.

**Impactos de negocio.**

Se produce un daño reputacional a la entidad bancaria debido los atacantes pueden saltarse la validación del modo root, dando a entender que los controles de seguridad son débiles en esta aplicación y provocando desconfianza de sus clientes.

**Explotación de la cache del teclado de Android**

Esta vulnerabilidad se produce cuando la aplicación almacena los nombres de usuario en cache, para que estos sean recordados, esto para el usuario común puede estar bien, sin embargo, el problema ocurre cuando un atacante intercepta la base de datos sql o en este caso sqlite3, donde como mínimo deberían estar cifrados los datos del usuario para no ser manipulados o facilitar la explotación por fuerza bruta de las credenciales.

**Usuarios sugeridos almacenados en la cache de la aplicación.**

![](/images/modulo7/img49.png)

Se escoge la opción *Add to dictionary*

Se descarga la base de datos con el siguiente comando.

`adb pull /data/data/com.android.providers.userdictionary/databases/user_dict.db`

**Extraccion de la base de datos de la aplicación.**

![](/images/modulo7/img50.png)

En la base de datos se accede usando sqlite3 mediante el siguiente comando para leer todo el contenido de la tabla words

**Acceso a la base de datos de la aplicación.**

![](/images/modulo7/img51.png)

Esto se considera una vulnerabilidad debido a que el texto en cache se esta almacenando en texto plano. Si bien la cache de la aplicación aplica al usuario, el mismo método podría aplicarse en la contraseña y ese seria un caso mas grave para una aplicación bancaria.

**Análisis de vulnerabilidad según OWASP Mobile Top 10**

**M2: Insecure Data Storage | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

Esta vulnerabilidad se produce debido a que las credenciales o información sensible de lo usuarios es filtrada. En este caso la base de datos user_dict.db almacena las credenciales de los usuarios en texto plano

**Impactos Técnicos**

Como se filtran credenciales de los usuarios logeados en la aplicación esto puede derivar en la extracción de la información mediante apps modificadas o producir una suplantación de identidad de no solo uno sino varios usuarios.

**Impactos de negocio.**

Una aplicación de este tipo manejada por una entidad bancaria no puede tener un tratamiento descuidado en cuanto a la administración de los datos de sus clientes, no es aconsejable almacenar este tipo de credenciales usando el almacenamiento externo, pero en caso de hacerlo por lo menos deberían estar encriptados los datos para no exponer la integridad de datos personales de clientes. Esto repercutiría en un daño reputacional para la entidad bancaria.

**M10: Extraneous Functionality | Exploitability EASY| Prevalence COMMON| Detectability AVERAGE| Impact SEVERE**

Esta vulnerabilidad involucra la búsqueda de funcionalidades extrañas en la aplicación para poder explotarlas dentro de los propios sistemas. En este caso se focaliza en la cache propia de la aplicación cuando se quiere guardar un nombre de usuario de uso frecuente, pero al no estar configurada apropiadamente se pueden filtrar estos datos desde la base de datos.

**Impactos técnicos.**

Esta vulnerabilidad expone como la base de datos donde se almacenan las credenciales de usuario y un proceso similar podría darse para la contraseña, por lo tanto, puede ser usado para ganar mayores privilegios de manera no autorizada.

**Impactos de negocio.**

Se produce un daño reputacional a la entidad bancaria debido los datos de los clientes no son almacenados de forma segura y provocando la desconfianza. Además, un atacante podría filtrar las bases de datos para ganar acceso no autorizado a las cuentas e información personal de los usuarios.

**Android broadcast receiver**

**Android broadcast receiver exportado como true**

![](/images/modulo7/img52.png)

Estos son los parámetros asociados a MyBroadcastReceiver, como se puede observar, estos corresponden a la recuperación de clave via SMS

**Clase ChangePassword haciendo un llamado al broadcastReceiver theBroadcast**

![](/images/modulo7/img53.png)

**Parametros necesarios para enviar la confirmacion de la contrasena**

![](/images/modulo7/img54.png)

Con los parámetros anteriores se puede construir un exploit usando adb shell:

`am broadcast -a theBroadcast -n com.android.insecurebankv2/com.android.insecurebankv2.MyBroadCastReceiver  --es phonenumber 5554 - -es newpass Dinesh@123!`

**Mensaje de confirmacion de cambio de contrasena.**

![](/images/modulo7/img55.png)

Se envia un mensaje de texto donde se actualiza la contrasenia de Dinesh@123$ a Dinesh@123!, el problema ocurre cuando al momento de cambiar la contrasena en el mensaje se revela la contrasena que se esta utilizando en este momento ya que al intentar logear con la ‘nueva contrasena’ nos muestra un error diciendo que es la clave incorrecta, suponiendo que un atacante filtre estos mensajes podria obtener la contrasena  para ingresar sin problemas y  tomar control de la cuenta bancaria del usuario dinesh.

**Análisis de vulnerabilidad según OWASP Mobile Top 10**

**M1: Improper Platform Usage | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

Esta categoria contempla el mal uso de las caracterisiticas de la plataforma o fallo en los mecanismos de seguridad. En esta vulnerabilidad se realiza el cambio de contrasena via SMS, pero al hacer dicho cambio se filtran credenciales en el mismo mensaje produciendo varios problemas como una autenticacion insegura y autorizacion insegura. 

**Impactos tecnicos**

Los impactos técnicos para esta vulnerabilidad implican un impacto técnico basado en la guía de OWASP Top Ten. En este caso sería una mayor exposición de las credenciales del usuario mediante SMS debido a que además de la nueva contraseña filtra la contraseña anterior, cuando la contraseña es vacía el cambio en la contraseña no ocurre es decir que se puede filtrar la contraseña sin haber hecho un cambio y de esta manera acceder como usuario legítimo.

**Impactos de negocio**

Un impacto de negocio de esta vulnerabilidad seria el dano reputacional ya que si bien existen mecanismos para cambiar la contrasena via SMS se da mas informacion de lo debido, ademas se envian la contrasena en texto plano en un supuesto que el dispositivo es robado o se pierde, dichas contrasenas estan a la vista de cualquier persona; lo normal seria utilizar algun token o a traves de una cuenta de correo electronico para luego realizar el cambio.

**M4: Insecure Authentication | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

Esta vulnerabilidad se produce cuando porque las credenciales se filtran mediante un SMS la nueva contrasena y la contrasena anterior y de esta manera se logra obtener las credenciales adecuadas para realizar un acceso exitoso como un usuario legitimo.

**Impactos tecnicos**

Con las credenciales filtradas via SMS se puede acceder como un usuario legitimo y por lo tanto se puede suplantar la identidad, si bien solo se filtra la contrasena se podria realizar algun ataque de fuerza bruta o concatenar con alguna vulnerabilidad anterior donde se filtraban los nombres de usuario almacenados en las bases de datos, de esta forma dificulta detectar el origen.

**Impactos de negocio**

Entre los impactos de negocio estan el acceso no autorizado a informacion, robo de informacion y dano reputacional. Un atacante luego de haber hecho el bypass tiene via libre para realizar transferencias de diversas cuentas sin poder ser localizado, ademas de perjudicar tanto a los clientes como a la entidad financiera que perderia credibilidad por no ser capaz de llevar a cabo mecanismos de seguridad que protejan  a la aplicación.

**M6: Insecure Authorization | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

Esta vulnerabilidad se produce cuando existen esquemas pobres de autorizacion , por lo que si un usuario consigue hacerse con privilegios mas altos podria acceder a funcionalidades ocultas, en este caso se produce un problema con la autorizacion porque generalmente los usuarios necesitan proporcionar muchos datos para solicitar un cambio de contrasena, en el caso de las aplicaciones moviles se suele usar una token y luego de ingresarlo en la sesion de inicio es cuando se produce el cambio de contrasena, nunca se deberia enviar la contrasena en texto plano. 

**Impactos tecnicos**

Una mala gestion en la autorizacion da privilegios elevados a cualquier atacate que esta en busqueda de funcionalidades que no se han validado correctamente, esta aplicación da mucha informacion para un cambio de contrasena y va en texto plano lo que lo hace aun mas vulnerable.

**Impactos de negocio**

Para una aplicación bancaria como insecurebankv2 que sus clientes o un atacante tengan acceso a la cuenta sin necesidad de una validacion de identidad de sus datos o con un token, o contrasena temporal implica que los sistemas de proteccion son debiles y que un atacante o cualquier persona que sustraiga un telefono puede tener acceso a datos personales de clientes y esto representa un problema en primer lugar para los usuarios y por ende a la credibilidad de la entidad bancaria ya que podria darse un fraude.

**M9: Reverse Engineering | Exploitability EASY | Prevalence COMMON | Detectability EASY | Impact MODERATE**

Esta es una de las vulnerabilidades más comunes que existen para las aplicaciones, debido a la naturaleza de Android es sencillo obtener el código fuente de una aplicación y analizar sus componentes en la clase MyBroadcastReceiverClass en donde se indican que componentes pueden construir el exploit para realizar este ataque como lo son “phonenumber” y “newpassword”.

**Impactos Técnicos**

Un impacto técnico que podría darse es ganar inteligencia sobre el comportamiento del broadcast receiver ya que tiene exported con valor true, el mecanismo esta expuesto y no es validado correctamente produciendo que se puede acceder de forma externa con adb o aplicación de terceros.

**Impactos de Negocio**

Como consecuencia de la ingeniería inversa se pueden producir los siguientes perjuicios a la entidad bancaria como daño reputacional, y fraude debido a la obtención de las contraseñas de los usuarios sin una validación apropiada y sin una autorización, haciendo posible que se hagan transferencias de múltiples cuentas a la de algún atacante.

**Explotando Android Content Provider**

En la siguiente captura se muestra el permiso de la activity TrackUserContentProvider

**Content Provider TrackUserContentProvider**

![](/images/modulo7/img56.png)

En esta captura se muestra el parámetro asociado al ContentProvider de la captura anterior

**URL hardcodeada  en la clase TrackUserContentProvider**

![](/images/modulo7/img57.png)

En la shell de Android  y se ingresa el siguiente comando:

`adb shell content query - -uri content://com.android.insecurebankv2.TrackUserContentProvider/trackerusers`

**Registro de los usuarios que han accedido a la aplicación.**

![](/images/modulo7/img58.png)

Como se puede observar se muestra un registro de los accesos a la aplicación en texto plano sin encriptar.

**Análisis de vulnerabilidad según OWASP Mobile Top 10**

**M2: Insecure Data Storage | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

Esta vulnerabilidad se produce debido a que las credenciales o información sensible de los usuarios es filtrada. En este el content provider muestra un registro de los usuarios que han accedido a la aplicación.

**Impactos Técnicos**

Como se filtran credenciales de los usuarios logeados en la aplicación esto puede derivar en la extracción de la información y puede ser útil para encadenarla con otra vulnerabilidad.

**Impactos de negocio**

Una aplicación de este tipo manejada por una entidad bancaria no se debería llevar un registro de los usuarios que han accedido en un content provider de la misma aplicación porque esos datos quedan guardados, además van en texto plano lo que lo hace aún más inseguro, no se puede tener un tratamiento descuidado en cuanto a la administración de los datos de sus clientes

**M9: Reverse Engineering | Exploitability EASY | Prevalence COMMON | Detectability EASY | Impact MODERATE**

Esta es una de las vulnerabilidades más comunes que existen para las aplicaciones, debido a la naturaleza de Android es sencillo obtener el código fuente de una aplicación y analizar sus componentes en la clase TrackUserContentProvider, los content providers son conocidos por ser componentes asociados a la inyección sql, que es donde se almacenan algunas de los datos que se transmiten en la aplicación.

**Impactos Técnicos**

Un impacto técnico que podría darse es ganar inteligencia sobre el comportamiento del content provider, estos componentes por defecto están exportados como true a menos que se especifique lo contrario. Se filtran datos de la base de datos a la que apunta la URL en la clase TrackUserContentProvider.

**Impactos de Negocio**

Como consecuencia de la ingeniería inversa se pueden producir los siguientes perjuicios a la entidad bancaria como daño reputacional por no tener un procedimiento adecuado en cuanto al tratamiento de los datos, pero además por el almacenamiento inseguro de las credenciales de usuario. 

**Explotando Android Pasteboard**

Se accede a la aplicación con credenciales validas(dinesh/Dinesh@123$) y luego se ingresa en la opción de Tranferencias(Transfer). Seleccionamos la cuenta y damos clic en el icono de copiar texto.

**Portapapeles de la aplicación InsecureBankv2**

![](/images/modulo7/img59.png)

En la terminal se busca procesos que se esten ejecutando en insecurebankv2 para esto se abre se ejecuta adb  Shell y se filtra por nombre.

`adb shell ps | grep insecure`

**Proceso correspondiente a Insecurebankv2**

![](/images/modulo7/img60.png)

Para ver el numero de cuenta que se paso al clipboard al copiar el texto, se utilzara el siguiente comando.

`adb shell su u0_a57 service call clipboard 2 s16 com.android.insecurebankv2`

**Texto almacenado en texto plano en el servicio de clipboard**

![](/images/modulo7/img61.png)

Como se puede observar esta funcionalidad es vulnerable porque muestra el numero de cuenta del cliente en texto plano.

**Análisis de vulnerabilidad según OWASP Mobile Top 10**

**M2: Insecure Data Storage | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

Esta vulnerabilidad se produce debido a que la cuenta del usuario es enviada en texto plano mediante el servicio de clipboard, siendo esta cuenta un dato importante si se asocia con alguna de las otras vulnerabilidades donde se obtiene el usuario y la contraseña.

**Impactos Técnicos**

El portapapeles o clipboard está expuesto y almacena cualquier dato sin encriptar ni validar, en este caso se guardó la cuenta bancaria del usuario, pero podrían haber sido otro tipo de credenciales válidas para una transacción

**Impactos de negocio**

Una aplicación de este tipo manejada por una entidad bancaria debería tener cuidado con los servicios proporcionados a la aplicación, si bien el portapapeles es totalmente valido de usar, estas funcionalidades pueden estar expuestas a vulnerabilidades por lo que no es aconsejable su uso, exponer datos personales importantes causa dano reputacional y desconfianza en los usuarios.

**M10: Extraneous Functionality | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

Esta vulnerabilidad involucra la búsqueda de funcionalidades extrañas en la aplicación para poder explotarlas dentro de los propios sistemas. En este caso se focaliza en el portapapeles para copiar datos en la aplicación que de no estar configurada apropiadamente se pueden filtrar esos datos.

**Impactos técnicos.**

Esta vulnerabilidad expone al servicio de portapapeles donde los datos se almacenan en texto plano, que son datos importantes que podrían ser usados por un atacante al momento de intentar acceder a la aplicación.

**Impactos de negocio.**

Se produce un daño reputacional a la entidad bancaria debido a la no confidencialidad de los datos de los clientes, no son almacenados de forma segura y provocando la desconfianza.

**Insecure Logging**

Se ejecuta el siguiente comando en la shell de Android

`adb logcat`

Logcat es una funcionalidad de Android que lleva un registro de todos los procesos que se ejecutan dentro de la aplicación, si se ingresa a la aplicación con las credenciales válidas se muestra los siguiente.

**Credenciales en texto plano mostradas en logcat**

![](/images/modulo7/img62.png)

Si se cambia la contrasena por una nueva dando clic al boton en Change Password.

**Cambio de contrasena en Insecurebankv2**

![](/images/modulo7/img63.png)

En el registro de logs aparecen nuevos cambios realizados en la aplicación, en este caso una actualizacion de contrasena

**Nueva contrasena filtrada en logcat.**

![](/images/modulo7/img64.png)

El cambio de contrasenia se produce via sms desde el celular 1555218135, ademas de ir en texto plano.

**Análisis de vulnerabilidad según OWASP Mobile Top 10**

**M4: Insecure Authentication | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

Esta vulnerabilidad se produce cuando porque las credenciales se filtran mediante los logs que aparecen en logcat junto al usuario y contrasena en texto plano, esto facilita a un atacante el acceso a las cuentas bancarias.

**Impactos tecnicos**

Si bien en esta vulnerabilidad ya hay que estar logeado lo que preocupa es que las credenciales no son administradas correctamente, en el caso de que un atacante de alguna manera logre filtrar las comunicaciones o tenga a la mano un dispositivo, se puede ayudar de logcat para comprobar si la contrasena esta encriptada o si esta en texto plano, se podria realizar ataques de fuerza bruta o emplear alguna de las vulnerabilidades anteriormente mencionadas.

**Impactos de negocio**

Entre los impactos de negocio estan el acceso no autorizado a informacion, robo de informacion y dano reputacional. La seguridad con la que se maneja las credenciales de sus clientes es debil, no solo mostrando la cuenta de usuario pero tambien la contrasena, el tratamiento con el que se esta tratando estos datos es insuficiente y podria ser filtrado por algun atacante.

**Proxying Android Traffic on Device**

Se inicia Burp Suite y se configuran las opciones para el nuevo proxy con el puerto 9999 y todas las interfaces.

**Configuracion del proxy en el puerto 9999**

![](/images/modulo7/img65.png)

Se habilita la opción “Support invisible  proxying” del apartado Request handling

**Invisible proxying habilitado**

![](/images/modulo7/img66.png)

Ahora se debe configurar las opciones del proxy al navegador Firefox.

**Configuracion del proxy en Firefox**

![](/images/modulo7/img67.png)

Una vez configurado se accede la url http://burp

**Acceso a BurpSuite mediante el navegador para posterior descarga del certificado.**

![](/images/modulo7/img68.png)

Se descarga el certificado al hacer click “CA Certificate” y se renombra como un archivo .crt

**Descarga del certificado de Burp Suite**

![](/images/modulo7/img69.png)

En el dispositivo se habilita las opciones de desarrollador, esto se logra normalmente dando clic 7 veces en la información del dispositivo. Con el modo desarrollador activado se activa USB debugging.

**USB debugging activado**

![](/images/modulo7/img70.png)

Con la ayuda de adb push se transferiere el certificado de burpsuite al dispositivo.

**Transferencia de certificado a la aplicación**

![](/images/modulo7/img71.png)

La opción “Install from SD Card” se localiza en Security que es una de las opciones de configuración.

**Instalacion del certificado de BurpSuite.**

![](/images/modulo7/img72.png)

Se ubica el certificado de Burp Suite y se selecciona.

**Certificado de Burpsuite localizado**

![](/images/modulo7/img73.png)

Se escoge un nombre y se acepta.

**Confirmacion de certificado.**

![](/images/modulo7/img74.png)

Si aparece una ventana solicitando un patron o  PIN se asigna cualquier valor y se continua el proceso.

**Advertencia de seguridad donde se avisa que se requiere de un PIN o patron.**

![](/images/modulo7/img75.png)

**Credenciales filtradas en Burpsuite.**

![](/images/modulo7/img76.png)

**Cambio de contrasena mediante el Repeater de Burpsuite.**

![](/images/modulo7/img77.png)

**M3: Insecure Communication | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE | Impact SEVERE**

En esta vulnerabilidad se considera como son intercambiados los datos entre el servidor y el cliente, la comunicación debe usar SSL/TLS durante la autenticación para no incurrir en datos transmitidos en texto plano como en este caso donde las credenciales usan http que muestra las credenciales en texto plano.

**Impactos técnicos**

Esta vulnerabilidad expone la data del usuario y puede llevar al robo de la cuenta si un atacante llegase a interceptar una cuenta de administrador entonces todos los demás clientes serian expuestos, además las configuraciones débiles de SSL facilitan los ataques MITM y phishing.

**Impactos de negocio**

El robo de credenciales por mala configuración de las comunicaciones puede provocar el fraude y robo de las mismas, la entidad bancaria estaría mas propensa a reclamos y llamadas por el mal uso de la aplicación por ser susceptible a este tipo de ataques de terceros.

## Conclusion

Luego de analizar la aplicación Insecurebank se encontraron múltiples vulnerabilidades comunes desde permisos peligrosos y excesivos para una aplicación bancaria como SEND_SMS, READ_CALL_LOG, READ_PHONE_STATE que solicitan mucha más información de la requerida, la versión del sdk permitida va entre la 15 hasta las 22, allowBackup y debuggable están activadas dando mayores facilidades a un atacante a obtener datos que normalmente no estarían a la vista de los usuarios.

Insecurebankv2 al ser una aplicación de entrenamiento es creada a propósito como una aplicación bancaria vulnerable, existe inyección SQL cuando se aprovecha el intent TrackUserContentProvider, almacenamiento inseguro debido a sus credenciales en texto plano o con cifrados débiles, sticky broadcast tampering al aprovecharse del receiver theBroadcast para enviar un mensaje de texto produciendo el cambio de contraseña y dando información adicional sobre la contraseña anterior que se cambió, además al analizar el trafico con un proxy como Burp Suite, las credenciales usaban un cifrado en las comunicaciones débil y por lo tanto aparecían en texto plano. 











