# Modulo 2

## Enunciado

Como hemos aprendido, el hacking ético es un método efectivo para comprobar los mecanismos de seguridad y verificar la postura de seguridad de una organización frente a un ataque. Este método se lleva a cabo siguiendo un orden lógico de manera que todo aquello obtenido en la fase anterior nos pueda servir de ayuda para la fase siguiente. Os recordamos que no se puede atacar a una organización sin tener previo consentimiento de la misma. Para una parte de este ejercicio vamos a utilizar IMF.

## Se pide

Conociendo únicamente el nombre de la organización IMF se debe buscar toda la información posible sobre esta. Dentro de las fases del análisis, únicamente contemplaremos las fases de reconocimiento (reconocimiento, fingerprinting) y escaneo (comandos básicos y puertos, servicios).

No está permitido realizar un escaneo web contra los servidores identificados ni lanzar herramientas semiautomáticas en busca de vulnerabilidades. Únicamente trabajaremos a nivel de detección de puertos y servicios.

### Como resultado de este ejercicio:

Se debe **mostrar la información encontrada**: (por ejemplo: información sobre empleados, dominios, IP, servidores públicos, y cualquier información extra), las herramientas utilizadas y la fase a la que corresponde cada tipo de prueba. La información se puede agrupar, por ejemplo, por fases, explicando los pasos que se han seguido, etc.

### El siguiente paso logico dentro del hacking etico:

Sería realizar un análisis de vulnerabilidades contra aquellos servidores detectados. En este caso, dada la criticidad del entorno no disponemos de permisos para realizar este tipo de pruebas y, por el momento, no disponemos de un entorno de pruebas para poder realizar ataques. De modo que, para este segundo ejercicio, **es necesario descargar una máquina virtual que se ha preparado para ello.**

### Una vez descargada e instalada hay que realizar las siguientes acciones:

+ Lanzar un escaneo semiautomático para identificar las posibles vulnerabilidades de la web.
+ Para aquellas vulnerabilidades detectadas, comprobar si se tratan de falsos positivos o si son amenazas reales.
+ Explotar las vulnerabilidades detectadas con otras herramientas diferentes de la de detección de vulnerabilidades.
+ Realizar una escalada de privilegios.
+ Realizar un análisis del resto de servicios en ejecución.

### Importante

Todos los pasos deben de estar correctamente explicados, añadiendo evidencias (informe de resultado de la herramienta semiautomática, capturas de pantalla, etc.).
La máquina virtual cuenta con 10 flags repartidas por todo el sistema. Es importante tener en cuenta que no es necesario disponer de un usuario en el sistemas.

## Parte 1:Enumeración yEscaneo de puertos IMF

Para esta seccion se realizará el reconocimiento pasivo de IMF usando el buscador Google usando distintos dorks para obtener diversos resultados. Realizando una búsqueda simple únicamente con la palabra **IMF** se obtienen algunos resultados entre los cuales destaca el Fondo Monetario Interacional(International  Monetary  Fund  Home  Page) y la página de IMF Business School. Ademas aparece un numero con prefijo +34 correspondiente a España, obteniendo un aproximado de 93600000 resultados.

**Busqueda Simple**

![](/images/modulo2/busquedasimple.png)

Al usar el dork **site:imf.com** se reducen los resultados a unos 896 y se muestran algunos subdominios de la pagina de imf.com como lo son noticias, grados, entre  otros. Además de la página oficial de los Masters Online, MBA y Becas.

**site:imf.com**

![](/images/modulo2/siteimf.PNG)

Usando el operador – limita aun más las búsquedas de imf.com mostrando unos 39 resultados y de la misma manera mostrando los subdominios grados,noticias.

**Limitacion de sitios referentes al Fondo Monetario Internacional**

![](/images/modulo2/filtradoimf.PNG)

Usando el dork **intitle:IMF** muestra todos los  resultados  que  tengan  IMF  en  el  título, se puede  apreciar  en  la siguiente  imagen el  dominio www.imf.org correspondiente al Fondo Monetario Internacional, www.imf-formacion.com y un resultado de IMF International Business School de Wikipedia.Tanto imf.com como www.imf-formacion.com  están relacionados al grupo IMF Business School por lo que se utilizarán para posteriores análisis.

**Dork Intitle**

![](/images/modulo2/intitleimf.PNG)

Al usar el dork **filetype** se encuentran 7 resultados de tipo PDF con relación a imf.com, catalogo_2015_masters, un catálogo de los cursos, horario de ponencias,  compliance-program-master, entre otros.

**Dork filetype**

![](/images/modulo2/filetypeimf.PNG)

Continuando con la etapa de reconocimiento pasivo, ahora se utilizará TheHarvester, herramienta que ya se encuentra instalada en Kali Linux y que permite obtener  correos relacionados a un dominio. IMF está relacionado a los dominios **imf.com** e  **imf-formacion.com**. Por lo tanto se hará uso del comando `theharvester -d imf.com -l 500 -b google`

**Correos imf.com**

![](/images/modulo2/correosimfcom.PNG)

`theharvester -d imf-formacion.com -l 500 -b google`

**Correos imf-formacion.com**

![](/images/modulo2/correosimfformacion.PNG)

Whois es una herramienta disponible tanto en la web como en Kali Linux, proporciona información acerca del dominio proporcionado, el ID, fecha de creación, actualización, los nombres de los servidores, correos, etc.

**Whois imf.com**

![](/images/modulo2/whoisimf.PNG)

A continuación se realiza la fase de reconocimiento activo, en el cual se utilizaran escaners como masscan y nmap para obtener los puertos y servicios del dominio imf.com. La IP se obtuvo a partir de harvester en donde se muestra que la dirección IP es 82.98.160.177. Se usa masscan primero debido a que es una herramienta que es mucho mas rápida que nmap. Una vez que termina de analizar se observa que los puertos 110,465,80,20996,587,12340,993,3306,995 están abiertos.

**Escaner de puertos con Masscan**

![](/images/modulo2/masscan.PNG)

Como ya se conocen los puertos abiertos,nmap ya no tendra que analizar los 65535 puertos, sino solo los mencionados anteriormente.

`nmap -sC -sV 82.98.160.177 -p 80,110,465,20996,587,12340,993,3306,995 -o imf.nmap`

**Escaner de puertos con Nmap**

![](/images/modulo2/nmapimf.PNG)

![](/images/modulo2/nmapimf2.PNG)

**Servicios encontrados**

![](/images/modulo2/serviciosimf.PNG)

Los servicios encontrados fueron los siguientes: http versión nginx, pop3  Dovecot, ssl/mpls , smtp Postfix smtp, ssl/pop3s, mysql 5.5.62-0+deBul-log. También se  puede destacar que el sitio lleva algún tipo de seguridad que no permite o restringe la información obtenida de los escáneres ya que algunos aparecen como unknown o forbidden.

## Parte 2: Maquina Virtual

### Reconocimiento

Una vez que se ha instalado la máquina virtual en Vmware se realiza un netdiscover para conocer la IP de la maquina virtual.

`netdiscover -i eth0 -r 192.168.190.0/24`

**IP de la maquina virtual**

![](/images/modulo2/netdiscover.PNG)

La IP 192.168.190.129 es ingresada en Firefox y muestra lo siguiente

**Retos Web**

![](/images/modulo2/firefox.PNG)

En la imagen se menciona que no son los únicos retos por lo que podría haber información adicional en el código fuente, para ello se presiona F12 o también con clic derecho y luego View Page Source.

**Codigo Fuente de la pagina web**

![](/images/modulo2/sourcecode.PNG)

Aqui se obtiene la primera flag **FLAG{B13N_Y4_T13N3S_UN4_+}**

El primer reto muestra un inicio de sesión con usuario y contraseña, uno de los metodos mas comunes donde podria haber credenciales filtradas es en el codigo fuente. En este se encuentra el usuario y la contraseña requeridos para ingresar.

**Credenciales filtradas**

![](/images/modulo2/login1.PNG)

El codigo fuente revela que el usuario es **admin** y la contraseña es **supersecret**. Al ingresar estas credenciales se revela la siguiente flag **{LOGIN_Y_JAVASCRIPT}**

**Segunda Flag**

![](/images/modulo2/flag2.PNG)

En la segunda opción **Bypass login 2**, se muestra una sesión de login con el mensaje del sitio de “Area  Segura”. Para tratar de ingresar se probaron con algunos  métodos como fuerza bruta con hydra, diccionarios, sql Injection, File inclusion, XSS  sin éxito alguno. El método por el cual se obtuvo la flag fue mediante los métodos HTTP.

**Login 2**

![](/images/modulo2/login2.PNG)

BurpSuite es un proxy que permite capturar peticiones de las aplicaciones web, para  utilizarlo hay que configurar un proxy con IP 127.0.0.1 y puerto 8080. Esto se  puede lograr mediante las preferencias de Firefox, luego en Network Proxy se escoge Settings y se configura la dirección y el puerto mencionados anteriormente  o mediante un add-on de Firefox como FoxyProxy.

**Peticion capturada**

![](/images/modulo2/burp1.PNG)

Con la información obtenida se da clic derecho y se envia al repeater usando la opcion **Send  to repeater**. Con esta opción se puede modificar y enviar solicitudes utilizando los distintos métodos de HTTP como **GET,POST,PUT,HEAD,DELETE**. Los métodos que se utilizaron fueron GET y POST.
+ GET que permite recuperar datos
+ POST se utiliza para enviar una entidad a un recurso en específico, causando a menudo un cambio en el estado o efectos secundarios en el servidor.

**Metodo GET en el repeater**

![](/images/modulo2/burp2.PNG)

Algunos sitios web emplean la indexación como una forma de prevenir ataques o que personas sin autorización accedan a información privada con relación a usuarios, contraseñas y configuraciones en general. En este caso al intentar acceder a .htaccess, .htpasswd, ambos aparecen como Forbidden o Acceso Restringido. La indexación por lo general usa un archivo index.html o index.php en este caso para redireccionar a una página con 404 error o Forbidden y restringir el acceso a esos archivos. Dicho esto se cambia el método HTTP a POST y se cambia el directorio de la URL por /login_2/index.php/.

**Metodo POST en el repeater**

![](/images/modulo2/burp3.PNG)

Se obtuvo la tercera flag **FLAG{BYPASSING_HTTP_METHODS_G00D!}**

La tercer opcion PING - PONG muestra lo siguiente.

**Contenido de la opcion PING-PONG**

![](/images/modulo2/pingpong1.PNG)

El mismo mensaje indica que se puede ejecutar el comando ping usando el metodo GET.

**Respuesta del comando ?ip=127.0.0.1**

![](/images/modulo2/pingpong2.PNG)

Se prueba la IP junto a otro comando en este caso **ls** y para anexar la ejecución en la URL se utiliza el simbolo “|“, dando como resultado los archivos cargados y mostrando un archivo interesante como **estonoesunaflag.txt**.

**Directorio de archivos encontrado**

![](/images/modulo2/pingpong3.PNG)

Finalmente se usa el comando **cat** para visualizar el contenido de **estonoesunaflag.txt**, obteniendo la cuarta flag. Esta vulnerabilidad corresponde a una ejecucion remota de comandos o RCE, esto permitió que se obtenga información usando varios comandos junto al comando establecido ping.

**Cuarta Flag encontrada**

![](/images/modulo2/pingpong4.PNG)

La cuarta flag es **FLAG{SIMPLEMENTE_RCE}**

Una vez terminado el reconocimiento en la aplicacion web de los retos, se procede a enumerar los directorios con la finalidad de obtener mas informacion que se haya podido pasar por alto.

Dirb es una herramienta que permite enumerar directorios de servicios web con el siguiente comando se obtiene el resultado mostrado a continuacion.

`dirb http://192.168.190.129`

**Enumeracion de directorios**

```
-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Mon Sep 28 01:45:48 2020
URL_BASE: http://192.168.190.129/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://192.168.190.129/ ----
+ http://192.168.190.129/index.php (CODE:200|SIZE:456)                                                                                                       
==> DIRECTORY: http://192.168.190.129/ping/                                                                                                                  
+ http://192.168.190.129/robots.txt (CODE:200|SIZE:38)                                                                                                       
+ http://192.168.190.129/server-status (CODE:403|SIZE:303)                                                                                                   
==> DIRECTORY: http://192.168.190.129/uploads/                                                                                                               
                                                                                                                                                             
---- Entering directory: http://192.168.190.129/ping/ ----
+ http://192.168.190.129/ping/index.php (CODE:200|SIZE:272)                                                                                                  
                                                                                                                                                             
---- Entering directory: http://192.168.190.129/uploads/ ----
+ http://192.168.190.129/uploads/index.php (CODE:200|SIZE:34)                                                                                                
                                                                                                                                                             
-----------------
END_TIME: Mon Sep 28 01:46:03 2020
DOWNLOADED: 13836 - FOUND: 5
```
Encontrandose 5 resultados
 + robots.txt
 + /ping/
 + /server-status
 + /uploads/
 + index.php
 
 **Contenido del directorio /uploads/**
 
 ![](/images/modulo2/enumeracion.PNG)

La quinta flag es **FLAG{ENUMERA_DIRECTORIOS_SIEMPRE}**

Con la fase de **reconocimiento** terminada se realiza el **Escaneo de puertos** para recabar mayor informacion de los puertos y versiones dentro de la maquina virtual.

### Escaneo de puertos

Para esto se usa el comando `nmap -sC -sV 192.168.190.129 -o cyber.nmap`

**Puertos y servicios encontrados**

![](/images/modulo2/nmapcyber1.PNG)

![](/images/modulo2/nmapcyber2.PNG)

Como resultado del escaneo se encontraron los puertos 21,22,25,80,110,119

```
PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
25/tcp  open  smtp
80/tcp  open  http
110/tcp open  pop3
119/tcp open  nntp
```

Entre los directorios que aparecen junto al puerto 80 esta **robots.txt**, este archivo generalmente se usa para no indexar ciertas paginas en el buscador.

**Contenido del archivo robots.txt**

![](/images/modulo2/robots1.PNG)

Aqui se menciona al directorio **/cyberacademy**, a continuacion se intenta cambiar a este directorio para encontrar mayor informacion.

**Sexta Flag**

![](/images/modulo2/robots2.PNG)

La quinta flag se encuentra en el nuevo directorio /cyberacademy con el contenido **FLAG{YEAH_R0B0T$.RUL3$}**

El puerto 21(ftp) esta abierto, como resultado del escaneo tambien se muestra que se puede acceder con el usuario **ftp** y ademas que existe un archivo llamado **flag.txt**.

```
21/tcp  open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 ftp      ftp            30 Dec 07  2017 flag.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.190.128
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable

```

Una vez dentro se puede extraer los archivos de ftp usando el comando **mget**

**Flag en FTP**

![](/images/modulo2/flagftp.PNG)

**Contenido de la flag**

![](/images/modulo2/flag6.PNG)

Septima Flag **FLAG{FTP_4n0nym0us_G00D_JoB!}**



## Bibliografía

1. **Métodos de petición HTTP** obtenido de:https://developer.mozilla.org/es/docs/Web/HTTP/Methods
2. **Apache James Server 2.3.2 – Remote Command Execution** obtenido de: https://www.exploit-db.com/exploits/35513
3. **Linux Kernel < 4.4.0/ < 4.8.0 (Ubuntu14.04/16.04 / Linux Mint 17/18 / Zorin) - Local Privilege Escalation (KASLR / SMEP)** obtenido de:https://www.exploit-db.com/exploits/47169
4. **Metasploit Web Delivery: Un módulo que simplifica el despliegue de payloads** obtenido de: http://www.elladodelmal.com/2017/02/metasploit-web-delivery-un-modulo-que.html
5. **Hackthebox Write-up–Solidstate** obtenido de:https://dominicbreuker.com/post/htb_solidstate/
6. **Comandos de Meterpreter Kali Linux** obtenido de: https://www.creadpag.com/2018/05/comandos-de-meterpreter-en-kali-linux.html
7. **Comandos FTP en consola** obtenido de: https://victorroblesweb.es/2013/12/02/comandos-ftp-en-la-consola/










