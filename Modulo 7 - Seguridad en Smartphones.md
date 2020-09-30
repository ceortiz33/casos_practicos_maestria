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

**Cadenas de caracteres dentro del archivo strings.xml **

![](/img/modulo7/img14.png)

Luego de realizar el cambio se reconstruye la aplicación con el comando.

`apktool b InsecureBankv2 -o newapp.apk`

**Compilación de la aplicación modificada**

![](/images/modulo7/img15.png)

Como se compilo nuevamente esta aplicación, para que sea aceptada por el emulador se necesita de una firma(signature) o key para que funcione correctamente y para esto se ejecutan los siguientes comandos.

`keytool -genkey -v -keystore my-release-key.kestore -alias alias_name -keyalg RSA -keysize 2048 -validity 10000`

`jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore my-release-key.keystore newapk alias_name`

Finalmente, luego de instalar la aplicación en Genymotion la aplicación cambia de la siguiente manera.

**Resultado de la modificacion en archivo strings.xml **

![](/images/modulo7/img16.png)

Como se puede ver en la imagen anterior cuando se realizó ese cambio en el archivo strings.xml en **is_admin** se le dio al usuario final la posibilidad de crear un nuevo usuario, dicha opción que solo debería tenerla un administrador.

**Análisis de vulnerabilidad según OWASP Mobile Top 10**

**M8: Code Tampering | Exploitability EASY | Prevalence COMMON | Detectability AVERAGE |Impact SEVERE**

Esta vulnerabilidad se produce cuando se realizan cambios al código mediante formularios maliciosos, o cambios directos en los binarios de la aplicación. En este caso se parcho la aplicación para que el valor del string **is_admin** sea true, de esta manera se produjo un cambio en la aplicación dando como resultado la aparición de un botón que permite crear un nuevo usuario, es decir es una característica propia de un administrador mas no el usuario común.

**Impactos Técnicos**

Un impacto técnico que podría darse es el acceso a nuevas características no autorizadas como la creación de nuevos usuarios, tratándose de una aplicación bancaria el usuario común no debería estar en capacidad de crear una nueva cuenta ya que el banco es la entidad que le proporciona una a cada usuario.
























