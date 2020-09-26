# Modulo 3

El trabajo final de este modulo consiste en el análisis de vulnerabilidades que existen en la aplicación Wackopicko, esta aplicacion fue creada de manera insegura con la intencion de crear consciencia en los desarrolladores y personas interesadas en la adopcion del codigo seguro como parte de las practicas diarias en el desarrollo.

Este trabajo se divide en dos partes, la primera  consiste en el análisis automatizado utilizando un analizador web y la segunda en identificar, remediar y dar recomendaciones sobre cómo mejorar su implementacion, para hacerlo mas resistente a los ataques mas comunes debido a la falta de implementación de practicas seguras en el codigo.

Para la primera parte se usara el escaner de vulnerabilidades **OWASP ZAP**. Esta herramienta al igual que otros analizadores web como Vega, Burp, actuan como un proxy entre el servidor y el cliente; algunas de sus funciones son: 

* Filtrar solicitudes.
* Analizar directorios.
* Escaneos de vulnerabilidades.
* Entre otras.

OWASP ZAP utiliza el puerto por defecto 8080 para funcionar como proxy en localhost. Wackopicko en su version de maquina virtual o en version Docker utiliza un puerto para funcionar, una consideración a tener en cuenta es no utilizar el mismo puerto que el proxy debido a que genera un conflicto y no pueden funcionar de manera correcta. OWASP ZAP tiene la facilidad de cambiar el puerto con el que se enlaza con el navegador, esto se logro haciendo clic en **Tools > Options> Local Proxies**.

![](/images/modulo3/cambioPuerto.PNG)

Firefox es el navegador de preferencia para este analisis ya que cuenta con extensiones que facilitan la integracion con el proxy y es el navegador instalado por defecto en Kali Linux. Un add-on muy útil para modificar el estado ON/OFF del proxy en el navegador rápidamente es **Foxyproxy**. Este addon permite crear varias  configuraciones de direcciones IP y puertos, sin necesidar de ingresar a las configuraciones avanzadas del navegador, siendo conveniente cuando se quieren realizar  muchas pruebas y no perder mucho tiempo en cambiar nuevamente a la configuración por defecto del navegador.

![](/images/modulo3/foxyproxy.PNG)

## Primera Parte

Una vez aplicadas las configuraciones de proxy y las recomendaciones de puerto mencionadas anteriormente, OWASP ZAP puede comenzar a capturar las peticiones de Wackopicko. El siguiente paso es iniciar el Spider para enumerar todos los directorios, para esto se da clic derecho en la peticion capturada y luego en **Attack  >  Spider**. OWASP ZAP tiene la opcion de scanner automatizado que permite obtener vulnerabilidades que existen en la aplicacion para esto se da clic derecho y luego en **>Attack > Active Scan**. Una vez haya terminado el escaneo se mostrarán las vulnerabilidades en forma de alertas, además si se desea un reporte con los hallazgos encontrados esta herramienta dispone de una opción para hacerlo, para esto se dirigie al menu **Report** y luego la opcion **Generate HTML Report**.

![](/images/modulo3/enumeracion.PNG)

### Resultados Obtenidos

OWASP ZAP clasifica las alertas en orden de prioridad como resultado del análisis se encontraron un total de 15 alertas, 6 alertas con riesgo alto, 4 alertas de riesgo medio, 5 alertas de riesgo bajo y no se encontraron alertas de tipo informacional.

![](/images/modulo3/summaryalerts.PNG)

Para una mejor visualización del impacto de los riesgos se utilizar una representación por color, siendo rojo riesgo alto, amarillo riesgo medio y verde riesgo bajo.

![](/images/modulo3/riesgocolor.PNG)

### Remote OS Command Injection

OWASP ZAP encuentra en el directorio **/passcheck.php**. Esta vulnerabilidad corresponde a ejecución no autorizada de comandos del sistema operativo. El parámetro atacado para esta vulnerabilidad es el **password**.

![](/images/modulo3/remoteos.PNG)

### Cross Site Scripting Reflected

OWASP ZAP encuentra tres posibles vulnerabilidades reflejadas en  **/guestbook.php**, en **search.php?query=%22%3E%3Cscript%3Ealert%281%29%3B%3C%2Fscript%3E** y **/users/login.php** todas utilizando **<script>alert(1)</script>** como vector de ataque.

![](/images/modulo3/xssreflected.PNG)

### SQL Injection

OWASP ZAP encuentra esta vulnerabilidad por el método POST en el directorio **/users/login.php**, además utiliza la query **ZAP’AND ‘1’=’1–** para ganar acceso a la sesión.

![](/images/modulo3/sqlinjection.PNG)

### Remote File Inclusion

Esta vulnerabilidad permite cargar un archivo y dependiendo del contenido del mismo se puede ganar control remoto de la máquina como es el caso de las reverse shells, exploits,etc. 

![](/images/modulo3/remotefile.PNG)

### Cross Site Scripting Persistent

OWASP ZAP muestra el directorio /guestbook.php como vulnerable. A diferencia del XSS almacenado, este no se guarda en la base de datos.

![](/images/modulo3/xsspersistent.PNG)

### X-Frame Options Header Not Set

OWASP ZAP enumera todos los directorios de la pagina web y muestra aquellos que no tienen habilitado el parámetro **X-Frame-Options** con 225 coincidencias.

### Application Error Disclosure

Esta vulnerabilidad puede enviar un mensaje de error o warning y puede mostrar información sensible como la ubicacion de un archivo en particular.

### Directory Browsing

Es posible ver la lista de directorios,se podrian revelar scripts ocultos,archivos o backups.

### Buffer Overflow

Esta vulnerabilidad se caracteriza por sobreescribir espacios de memoria del proceso de fondo web.

### X-Content-Type-Options Header Missing

Esta vulnerabilidad permite a versiones anteriores de Internet Explorer y Chrome realizar MIME-sniffing

### Web Browser XSS Protection Not enabled

La protección web contra XSS no está habilitada o el parámetro X-XSS Protection esta deshabilitado.

### Absence of Anti-CSRF Tokens

No se encontraron tokens Anti-CSRF en HTML

## Segunda Parte

Para esta segunda parte se analizara de forma manual Wackopicko en base a los resultados obtenidos de OWASP ZAP. Wackopicko es una aplicación tipo red social que  permite compartir fotos e imágenes con tus amigos, se puede comentar, recomendar y comprar las fotos que se suben al sitio.

![](/images/modulo3/wackopicko.PNG)

Wackopicko tiene cinco pestañas siendo estas **Home**, **Upload**, **Recent**, **Guestbook** y por último **Login**. Explorando la página se observa que el contenido de **Home** y **Upload** no puede ser accedido sin antes iniciar sesión en login.php, esto debido a que siempre se redirecciona a **localhost:8080/users/login.php**

![](/images/modulo3/wackopickologin.PNG)

La opcion **Recent**  muestra  las imágenes recientemente cargadas a la plataforma,ademas de redireccionar al directorio **/pictures/recent.php**.

![](/images/modulo3/wackopickorecent.PNG)

La opcion **Guestbook** muestra un formulario de opiniones que permite calificar a las publicaciones.

![](/images/modulo3/wackopickoguestbook.PNG)

Una vez que se ha identificado el entorno de trabajo de Wackopicko se procede a analizar las vulnerabilidades mas criticas encontradas en el escaner ZAP.

### XSS Persistente y Reflejado

De acuerdo a los resultados del escaner en OWASP ZAP, se menciona que un archivo que presenta esta vulnerabilidad es **/guestbook.php**. 

![](/images/modulo3/guestbook.PNG)

Una de las vulnerabilidades mas conocidas es XSS,misma que se clasifica en **Reflejado**, **Almacenado** y **DOM**.Como resultado del analisis se obtuvo que  Wackopicko es vulnerable a este tipo de ataque tanto del tipo reflejado como del tipo almacenado. XSS se puede producir de varias formas entre las cuales esta la validacion incorrecta de un enlace alterado en un formulario web.

El vector de entrada mas comun que produce la mayor parte de ataques XSS es la validacion incorrecta de valores en un formulario o blog no sanitizados y por dicha razon se permite cualquier tipo de entrada de datos, esto deriva en la ejecucion de comandos generalmente del tipo Javascript.El alcance de una vulnerabilidad XSS depende de la creatividad del atacante y de que tan vulnerable sea el sitio,siendo las mas comunes el robo se sesiones y cookies.

Para comprobar esta vulnerabilidad se escribe el segmento de codigo JavaScript **<script>alert(1)</script>** en el archivo guestbook.php dando como resultado la siguiente imagen mostrando un popup con el numero 1 que fue enviado como un alert.

![](/images/modulo3/alertxss.PNG)

Para poner a prueba si se puede obtener las cookies de la sesion,se ejecuta el siguiente comando **<script>alert(document.cookie);</script>** dando como resultado lo mostrado a continuación.El resultado se guarda en el blog por lo tanto esta vulnerabilidad corresponde al XSS de tipo almacenado.

![](/images/modulo3/cookies.PNG)

De igual manera si se escribe la misma instruccion desde el buscador se obtiene lo siguiente, lo que corresponderia al XSS reflejado.

![](/images/modulo3/cookiesreflejado.PNG)

### Solucion

Dentro de los archivos de la máquina virtual de Wackopicko se abre el archivo guestbook.php y se agrega **htmlentities** a los parametros recibidos via POST, es decir **name** y **comment**.

![](/images/modulo3/guestbookentities.PNG)

Además se cambia la etiqueta html que estaba predefinida a **h( $guest[“comment”])** ya que a pesar de que se ejecutara htmlentites en el parametro **comment**, este invalidaba los cambios y la sanitización solo se producía para el **name**.

![](/images/modulo3/guestbookmodif2.PNG)

Con los cambios realizados al archivo **guestbook.php** se logra evitar que se inyecte codigo malicioso en el formulario.

![](/images/modulo3/guestbookfix.PNG)

Otro sitio vulnerable a XSS es **/pictures/search.php?query=** donde al introducir **<script>alert(1);</script>** muestra otra ventana emergente.

![](/images/modulo3/pictures.PNG)

### Solucion

Para solucionar una vulnerabilidad XSS se necesita de validación de datos,sanitizaciónde datos, escape de caracteres especiales. Con la validación de datos se puede  permitir ciertos caracteres que sean ingresados, solo numeros, letras o números y letras.Para la parte de sanitización se debe asegurar que los datos sean seguros, recordando que una vulnerabilidad de XSS tambien puede redirigir a una página insegura, se puede usar el comando **strip_tags()** para sanitizar los enlaces y se lean como texto.

Se debe evitar el uso de caracteres especiales en search.php, se puede usar tanto **htmlentities()** como **htmlspecialchars()**, la única diferencia es que htmlspecialchars traduce solo  <,>,“”,“,& a entidades HTML. En cambio **htmlentities()** traduce todos los simbolos que tengan una equivalencia en codigo HTML.

![](/images/modulo3/search.PNG)

![](/images/modulo3/search2.PNG)

Luego de hacer estos cambios ya no muestra la alerta y queda sanitizado.

![](/images/modulo3/searchfix.PNG)

### Aplicación vulnerable a SQL Injection

Una de las vulnerabilidades mas comunes que se encuentra en una sesión de login es SQL Injection, una vulnerabilidad sencilla de mitigar, sin embargo de no ser corregida permite que el cliente haga cualquier tipo de consulta, ganando acceso a contenido de la base de datos o incluso eliminando cuentas de usuario.

Para probar si esta página es vulnerable se usara el carácterde escape ‘

![](/images/modulo3/sqlinjectiontest.PNG)

Al usar este carácter el sitio devuelve el siguiente resultado, por lo tanto se da por hecho que es vulnerable a la inyección SQL.

![](/images/modulo3/sqloutput.PNG)

La inyección se hizo en el usuario por lo que se puede deducir que la consulta realizada es del tipo:

**SELECT * FROM users WHERE username = ‘$user’AND password =‘$password’**

En esta query se observa que la contraseña se codifica con SHA1 y además utiliza un salt para limitar los accesos por fuerza bruta.

Usando esta petición devuelve que el usuario o contraseña es invalido, debido a que el usuario user no existe en su base de datos.

![](/images/modulo3/usertest.PNG)

En la página oficial de Wackopicko en Github, se encuentra información sobre los usuarios permitidos. Con estos usuarios se probará nuevamente para intentar hacer un bypass de la sesión.

![](/images/modulo3/validusers.PNG)

Se ingresa con las credenciales como usuario **scanner1** y contraseña **scanner1**

![](/images/modulo3/usuarioscanner1.PNG)

Como este usuario se obtiene acceso al sitio. Con esto en mente se intentará hacer el bypass de la sesión mediante SQL injection utilizando el usuario scanner1.

![](/images/modulo3/injectionscanner1.PNG)

Si se realiza la misma query que en el caso anterior con el usuario user, se observa que muestra un error porque se esta ejecutando la validación de la contraseña con SHA1 y dado que esta no se ha proporcionado nos muestra este error.

![](/images/modulo3/injectionerror.PNG)

Para omitir la validación de la contraseña se usara los caracteres **--** que permite comentar todo lo que viene después.

![](/images/modulo3/newinjection.PNG)

La query que se esta ejecutando es:

**SELECT * FROM users WHERE username = ‘$user’--’AND password =SHA1(’$password’,`salt`)**

Aqui se produce un error debido a que la query se malinterpeta recibiendo como usuario– .Para evitar esto se deja un espacio luego de los caracteres especiales, logrando de esta manera comentar la parte delpassword de la query y ganando acceso.

![](/images/modulo3/accesosqlinjection.PNG)

### Solucion

Una solución para evitar SQL injection es utilizar la función **real_escape_string();** que permite sanitizar las peticiones del usuario añadiendo \ a cada carácter especial como ‘, “ , null bytes como \x00y y en este caso ‘\’ ‘OR’ ‘\’1’\’ de esta forma, las petición es interpretada como un string y a pesar que es aceptada no es procesada como una query SQL.De acuerdo con la documentación en PHP este comando se puede usar tanto para la versión 5 y 7 por lo que no habría problema.

![](/images/modulo3/funcionesphp.PNG)

![](/images/modulo3/loginfix.PNG)

### Remote Command Inclusion

De acuerdo a los resultados en ZAP el archivo **passcheck.php** es vulnerable a la ejecucion remota de comandos.

![](/images/modulo3/passcheck1.PNG)

Al dar clic en check con la contraseña vacía se muestra el siguiente mensaje.

![](/images/modulo3/passcheck2.PNG)

En esta imagen se muestra claramente el comando que se esta empleando y el diccionario utilizado para la comparacion,en este caso se encuentra en el archivo **/etc/dictionaries-common/words**.Aquí se pueden ingresar algunos comandos, sin embargo la consulta al tener el símbolo de $ impide que algunos comandos se ejecuten de manera normal, con el comando que se puede hacer bypass a esto es con **mkdir** y un nombre; al ser una cadena de caracteres no habrá problema alguno.

![](/images/modulo3/comandoaceptado.PNG)

Con ayuda del comando **sudo -l** se muestra que todos los comandos son permitidos por esta máquina, de la misma manera al revisar el directorio donde se encuentra dictionaries-common no se encontró ninguna restricción de comandos.

![](/images/modulo3/restriccioncomandos.PNG)

Como se mencionó anteriormente al usar mkdir seguido de un nombre de archivo, en este caso **prueba**, se concatena con el símbolo $ haciendo posible el bypass para crear este archivo sin restricción alguna. Generando como resultado el archivo prueba$

![](/images/modulo3/archivocreado.PNG)

Para evitar esta vulnerabilidad es aconsejable utilizar las funciones **escapeshellarg()** o **escapeshellcmd()**, que son funciones de PHP que permiten sanitizar  las funciones o comandos que puedan afectar al sistema, como en este caso donde se pudo crear un directorio llamado **prueba$**. La diferencia entre ambos comandos radica en que **escapeshellarg()** permite escapar los comandos como si fueran strings con comillas simples para de esta manera no sean considerados como funciones a  ejecutar, en **cambioescapeshellcmd()** escapa metacaracteres precedidos por una barra \ para evitar que se pueda burlar el comando Shell y ejecutar la inclusión de comandos.

![](/images/modulo3/commandfix1.PNG)

![](/images/modulo3/commandfix2.PNG)

### Remote File Upload

Esta vulnerabilidad permite cargar archivos que pueden comprometer o incluso causar graves daños al sistema o a la base de datos. Wackopicko es una aplicación web con función de galería de imágenes tiene funciones de compra y venta de imágenes, cargar imagen, comentar. Teniendo en cuenta este contexto lo ideal sería que sólo se pudieran cargar imágenes a la aplicación es decir imágenes con extensión **jpeg, gif, png**. Sin embargo,en este caso no es así ya que permite cargar un archivo ejecutable de tipo PHP.

En el menú Upload se puede cargar una imagen detallando los siguientes campos: **tag, file  name,  title, price, file**. En el campo file de no estar correctamente  validado permitirá cargar archivos ejecutables como **php-reverse-shell.php**.

![](/images/modulo3/cargafile1.PNG)

Como se puede observar la reverse-shell has ido cargada correctamente porlo tanto se evidencia que existe esta vulnerabilidad.

![](/images/modulo3/shellcargada.PNG)

El campo tag insertado anteriormente genera una carpeta dentro del directorio upload donde se guarda la shell.

![](/images/modulo3/remoteshell.PNG)

En la máquina en Kali se ejecuta el listener con el comando **nc -lvnp 443**. Normalmente para ejecutar la shell remota, basta con dar clic y dicha accion habilitara  la ejecucion remota,otra forma de ejecutar la shell es mediante el comando **php -f [nombre.php]**.

![](/images/modulo3/shellejecutada.PNG)

Una vez que se ejecuta la shell remota ya se tiene acceso a la máquina virtual.

![](/images/modulo3/shellcommand.PNG)

### Solucion

El archivo que contiene la configuración para la validación de archivos que se cargan en upload es **piccheck.php**, por lo tanto con unos cuantos cambios se puede  corregir esta vulnerabilidad.

![](/images/modulo3/pidcheckfix.PNG)

La variable **$name** contiene el archivo que se carga al directorio upload, por esta razon se debería validar el tipo de extensión. Para capturar la extensión del archivo se usa el siguiente comando.

`$uploaded_ext = substr($name, strrpos($name, '.') + 1);`

El archivo $FILES es un array asociativo de los elementos subidos al directorio upload. En el script mostrado en piccheck.php se extrae el tipo type, del array  asociativo de la variable userfile, con esto en mente se puede usar el mismo concepto para extraer otro parámetro del array userfile. Como primer filtro se puede establecer un límite en el tamaño de los archivos que se cargan usando el comando siguiente.

`$uploaded_size = $_FILES[‘userfile’][’size’];`

Como segundo filtro se realiza la validación del tipo de extension. Para que sea de los tipos conocidos de imagen

`if (($uploaded_ext == "jpg" || $uploaded_ext == "JPG" || $uploaded_ext == "jpeg" || $uploaded_ext =="JPEG"|| $uploaded_ext == “png’ || $uploaded_ext == “PNG” ) && ($uploaded_size < 100000)){`

A pesar de tener este tipo de validación aun existen algunas formas de evitar este filtro ya que bien se podría cambiar el nombre de extensión,o cambiar el tipo MIME para que este sea considerado como el tipo que se ejecuta, es asi que algunos archivos PHP usando el MIME type de GIF o PNG pueden evitar esta verificación.

Como tercer filtro se plantea lo siguiente, y sevalidael MIME typepara que coincida con el del tipo de imagen.

`if($_FILES[‘userfile’][‘type’]==”image/jpeg”|| $_FILES[‘userfile’][‘type’]==”image/png”){`

![](/images/modulo3/extensionfix.PNG)

### Directory Browsing

Si bien esta no es una vulnerabilidad critica como  tal, si podría derivar en otras vulnerabilidades mas graves, al dejar los directorios expuestos un atacante podría aprovecharse de algún software desactualizado o de algún servicio y explotarlo, además se compromete información sensible en caso de no ser correctamente configurado.En el caso de Wackopicko observamos que los directorios /cart, /pictures, /images, /upload, entre otros muestran el índicede archivos.

![](/images/modulo3/browsing1.PNG)

![](/images/modulo3/browsing2.PNG)

![](/images/modulo3/browsing3.PNG)

![](/images/modulo3/browsing4.PNG)

En el caso de directorio sensibles como /upload donde se puede evidenciar que se han cargado varios archivos, si se tratase de una reverse shell esta podria ser ejecutada dando clic. De la misma manera informacion relacionada con las versiones de los servicios y aplicaciones pueden ser de gran interes para un atacante.

### Solución

Una posible solucion a este problema es redireccionar estos archivos a un archivo 404.php que es básicamente una pagina que muestra un mensaje, de esta forma aunque se intente acceder a los directorios mencionados anterioremente, se redireccionará al archivo 404.php mostrando el mensaje “404 Page not Found”.

![](/images/modulo3/redirect1.PNG)

![](/images/modulo3/redirect2.PNG)

![](/images/modulo3/redirect3.PNG)

### Recomendaciones

Si bien Wackopicko es una aplicación web para practicar desarrollo seguro, esta viene instalada con PHP 5.0, actualmente ya existe la versión PHP 7.0,se debería siempre mantener al día las actualizaciones del sistema, debido a que incluyen mejoras en la seguridad, así como nuevas funcionalidades. De la misma manera cualquier   otra funcionalidad o  servicio integrados a la aplicación debería actualizarse porque a medida que pasa el tiempo van apareciendo nuevas vulnerabilidades y con ello nuevos exploits, en caso de no mantener los sistemas actualizados estos se vuelven más propensos a sufrir este tipo de ataques. En cuanto a la política de contraseñas no  utilizar la misma que el nombre de usuario, usar una longitud adecuada con variación entre letras, números y símbolos.

En la medida posible no utilizar GET para mostrar los cambios entre usuarios, imágenes o en caso de darse procurar que esté debidamente sanitizado para no comprometer  la privacidad de los usuarios.Usar el encabezado SSL, al agregar este encabezado se convierte una aplicación HTTP en HTTPS siendo esta mas segura debido a que tanto el usuario y la contraseña no pasan en texto plano evitando que pueda ser interceptada con algún sniffer.

### Conclusiones

En este trabajo se desarrollaron habilidades indispensables para el desarrollo seguro como son la manipulación de código para corregir vulnerabilidades, el uso de un analizador para identificar vulnerabilidades de manera automatizada, sin olvidar que el resultado no es 100% seguro y siempre hay que comprobar para evitarfalsos positivos.Wackopicko es una aplicació nweb creada intencionalmente con fallos de seguridad, ideal para que un analista interesado en formarse en desarrollo seguro pueda practicar y tomar conciencia de lo importante que es la seguridad en una aplicación web.En esta aplicación se encontró vulnerabilidades importantes  como XSS reflejado,XSS almacenado,SQL Injection,Remote File Upload,Remote Command Inclusion. Estas pueden afectar los principios de confidencialidad, integridad y disponibilidad a tal punto que de ser un caso real afectara la reputación de la aplicación,ademásde comprometer la privacidad y los datos de los usuarios.
