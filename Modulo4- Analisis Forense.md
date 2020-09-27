# Modulo 4

## Enunciado

La empresa Invent S.L, que dispone de sedes en Australia, Italia y España, ha sufrido un incidente de seguridad en cada una de ellas.

## Sede Australia

Por una parte,en la sede de Australia, se ha detectado la fuga de información sensible de varios de sus empleados (direcciones de correo y contraseñas). El conjunto de afectados indica haber recibido una campaña de correos sospechosos con adjuntos HTML similares al  portal  de  Office  365 durante  los  últimos  días.  Esta  empresa  no  tiene (2FA)  factor  de autenticación en dos pasos, por lo que un atacante podría acceder al correo corporativo y otro tipo de aplicativos públicos en Internet alojados en Microsoft. Puesto que hay más de 10.000 empleados en la empresa, no es posible el reseteo y bloqueo de todas las cuentas por motivos de continuidad de negocio, por lo que es necesario localizar únicamente a los afectados.

## Sede Italia

Por otra parte, en Italia se ha producido un acceso no autorizado a uno de sus servidores de  contabilidad. Dicho acceso ha sido detectado mediante una revisión  periódica por el equipo de IT. En este caso, el equipo planea contratar a un proveedor externo para que se haga cargo de este incidente.

## Sede España

Finalmente, en la sede de España, se ha detectado un ataque en uno de los servidores de su fábrica de textiles. Todos los archivos del servidor han sido cifrados con la extensión **.NM4**. Estos equipos estaban parcheados contra MS17-010.

## Evidencias

**Australia:** Logs de tráfico del proxy de navegación en el intervalo de fechas en el que se produjo el incidente.

**Italia:** Ninguna puesto que se encargará un proveedor externo.

**España:** Estado de los puertos abiertos en el sistema y parte del mensaje de rescate

### 1. ¿Qué tipo de amenaza ha impactado en la sede de Australia?

El tipo de amenaza presente en la sede de Australia consta de dos partes, la primera es una campaña de phishing con un portal similar al de Office 365, una vez que se perpetua el engaño se obtienen las credenciales de los usuarios.Con la autenticación de dos pasos se puede frenar un poco los ataques y robos de información,ya que  agrega una capa adicional de seguridad al tradicional usuario y contraseña. Esta nueva capa de seguridad usa un PIN, biométrico o un código enviado vía SMS, etc. Al  no contar con la autenticación de dos pasos(2FA), el atacante tendrá vía libre para acceder a los aplicativos públicos alojados en Microsoft cuando obtenga las credenciales.

### 2. ¿Quétipode amenaza ha impactado en la sede de Madrid?

El tipo de amenaza que ha impactado en la sede de Madrid es un ataque por ransomware, en particular una variante en el Ransomware **XPAN** el cual produce los archivos cifrados **.NM4**. Normalmente se distribuyen mediante la descarga de programas gratuitos de terceros, correo electrónico entre otros.Con la ayuda del parche **MS17-010** se corrige la vulnerabilidad que existía en los equipos Windows 7 y versiones anteriores el cual se cifraban los archivos con el ransomware WannaCry al aprovecharse del protocolo de red,SMB,de los sistemas Windows.Si bien es cierto que la empresa tiene el parche de seguridad contra el ransomware WannaCry, para  solventar la vulnerabilidad de SMB, eso no implica que un ransomware diferente pueda ingresar a una red mediante el eslabón más débil, los usuarios,ya que podrían utilizarse otros vectores para su propagación, no únicamente el servicio de correo sino las descargas de terceros o incluso un pendrive infectado.

### 3. ¿Qué riesgo existe para una entidad cuando se produce una fuga de información como la descrita en el incidente de Australia?

Existen algunos riesgos para una empresa cuando se produce la fuga de información, una de ellas implica daño en la reputación de la empresa como consecuencia de no  poder protegerse ante este tipo de incidentes y no poder dar confianza a los clientes o proveedores cuando se produce filtración de bases de datos, correos electrónicos,etc. Otro riesgo es la divulgación de documentos que son confidenciales, esto puede ser de gran importancia por ejemplo para una empresa de la competencia para estar pendiente de algún nuevo producto o idea que está desarrollándose, o en el caso de un atacante vender los datos obtenidos como credenciales, cuentas bancarias, correos electrónicos a terceros.

## Australia

### Generar un script para parsear la captura de tráfico facilitada,de forma que muestre el resultado final por línea de comandos.

Para mostrar la captura facilitada por línea de comandos se puede hacer uso de **Tshark**, un complemento que viene instalado con Wireshark. De acuerdo al enunciado  de Australia, la empresa sufrió una campaña de phishing dirigido usando una pagina falsa de Office 365, con este antecedente se busca un log con información relacionada.

**Log sospechoso mostrado en Wireshark**

![](/images/modulo4/suspiciouslog.PNG)

Se puede realizar el mismo procedimiento usando Tshark con el siguiente comando

`Tshark -r /root/Downloads/australia.pcap -Y “dns”`

**Log sospechoso mostrado en Tshark**

![](/images/modulo4/tsharksuspiciouslog.PNG)

Con la salida DNS con **suspicious.microsoft.login** se busca ese log y se observa que se encuentra en el frame number 10597. Donde al filtrarlo por **user** se muestra lo siguiente

`Tshark -r /root/Downloads/australia.pcap -Y “http.request.method == “GET”“| grep “/user”`

**Resultado de filtro por usuario y peticion GET**

![](/images/modulo4/dnstshark.PNG)

Se produjeron dos resultados en los cuales se puede observar la palabra **user** de los cuales el mas interesante es.

**GET / user?bWdhcmNPYUBpbnZlbnQuY29tOm1hbnphbmExMjML**

El flag -V muestra el resumen de todo el contenido del log, para ver unicamente los parametros que interesan, se  usa **-T  fields**  y **-Y** para los filtros provenientes de Wireshark.

`tshark-r /root/Downloads/australia.pcap -T fields -e frame.time -e frame.number -e http.host -e http.user_agent -e ip.src -eip.dst -e http.request.method.full_uri -e tcp.srcport -e tcp.dstport -Y “http.request.method == “GET”“| grep “/user?”`

**Informacion mas detallada sobre los dos logs con informacion sobre el usuario**

![](/images/modulo4/masfiltrostshark.PNG)

Para una mejor visualización de los parámetros obtenido se crea un archivo con txt, mostrando la fecha y hora, el frame de donde se encontró,el host, el user_agent, full_URI, IP origen, IP destino, Puerto origen y puerto destino.

**Archivo txt con mas informacion del log**

![](/images/modulo4/archivotxt.PNG)

### ¿Qué direcciones de correo de usuario se han visto afectadas? Se puederealizar un análisis manual si no se ha conseguido realizar el apartado anterior.

De acuerdo a la página welivesecurity de Eset el procedimiento para analizar logs maliciosos comienza con analizar los logs DNS, seguido de las peticiones GET y POST y finalmente el análisis de correos usando el protocolo SMTP. En algunos casos el contenido y las direcciones de correo afectadas están encriptadas y por esa razon se  necesita desencriptar el trafico SMTP oculto por el protocolo TLS, este protocolo es la evolución del anterior protocolo SSL y encripta las conexiones que usan HTTPS.

Para desencriptar los logs existen varios métodos, el primero consiste en obtener el **SSLKEYLOGFILE** del navegador donde se está realizando la captura de los paquetes, en otros casos se provee el archivo **.der** junto al archivo **.pcap**, mismo que permitirá desencriptar el tráfico TLS v1.2 y como resultado mostrar el contenido oculto.

En este caso se encuentran las direcciones de correo junto a la petición GET obtenida anteriormente.

**Peticion GET con credenciales cifradas en su contenido**

![](/images/modulo4/peticiongetcredenciales.PNG)

Al expandir y visualizar el contenido del log se muestra lo siguiente.

**Credenciales dentro de la peticion GET**

![](/images/modulo4/contenidopeticionget.PNG)

**bWdhcmNpYUBpbnZlbnQuY29tOm1hbnphbmExMjMK==**
**aHR0cHM6Ly9wYXN0ZWJpbi5jb20vMlIwRmVtM0MK==**

En la peticion GET se visualizan dos parámetros mismos que pasan a través de un query request por lo general viajan cifrados en base64, con esto en mente se usara una página en internet para descifrar su contenido.

**Decodificacion base64**

![](/images/modulo4/base64decode.PNG)

El resultado muestra un correo y su contraseña **mgarcia@invent.com:manzana123** y una página web.Al abrir dicha página web se encuentra lo siguiente.

![](/images/modulo4/credencialesobtenidas.PNG)

3 correos adicionales que pudieron haberse usado para la campaña de phishing.

+ hifi@invent.com:123dmr
+ hjerf@invent.com:applepup
+ jdarwin@invent.com:redcar#

## Italia

**¿Se debería desconectar el equipo de la red para su análisis?**

Se debería desconectar el equipo de la red únicamente cuando ya se haya realizado un análisis de la red y tomado las evidencias respectivas.

**¿Qué parte hardware del servidor se debería clonar/volcar antes de apagar el equipo?**

Si el equipo ya esta encendido se procede a clonar los datos más volátiles que son los datos almacenados en la memoria RAM, para esto se hace un volcado de memoria.

**¿Mediante qué conocido comando se realizaría el clonado del disco duro? Escribir un ejemplo de ejecución?.**

Para clonar discos se puede usar el comando dd, procurandotener los permisos necesarios de administrador es decirejecutarlo en modo root.

`dd if=[archivoorigen] of=[archivedestino]`

**Clonado de disco**

![](/images/modulo4/clonadodico.PNG)

**¿Qué habría que calcular tras el clonado del disco para verificar su integridad?**

Para determinar la integridad de un disco se debe verificar su firma hash. Una firma hash es una cadena producida en base a los bits leídos del disco duro y  permite identificar cuando ha sido modificado. Cuando se termina el proceso de clonado del disco se obtiene una firma hash única que al ser modificada, esta cambia su valor. Esto es importante como investigadores forenses debido a que una modificación en la evidencia podría invalidarla.

**Describa brevemente la cadena de custodia que se debe seguir para el traspaso de las evidencias al proveedor externo**

La cadena de custodia son todos los procedimientos que se deben tener en consideración desde el inicio de un peritaje hasta el final donde se exponen las pruebas ante  una autoridad competente o juez. Al iniciar un procedimiento judicial se obtienen las pruebas que den sustento al delito que se ha producido, luego se asegura la   evidencia cuidadosamente para que este lo más segura posible, sea usando un tipo de embalaje o transportada por otro medio que garantice que la prueba estáen buenas condiciones para ser tratada,después son transportadas a un centro especializado donde las personas que se encuentren en posesión de la evidencia serán personas calificadas. Durante el análisis se debe garantizar la integridad de la evidencia, ya que al modificarla se invalida la prueba y pierde autenticidad. Al momento de  presentarse ante un juez se elabora un informe con toda la información recopilada de las evidencias, procedimientos y técnicas utilizados en la etapa de análisis como también un registro de aquellas personas que tuvieron acceso a la evidencia para descargos de responsabilidad y dejar constancia que el procedimiento ha sido transparente.

## Madrid

**¿Cómo funciona este tipo de amenaza?**

El tipo de amenaza que ha impactado en la sede de Madrid es un ataque por ransomware, en particular una variante en el Ransomware XPAN que encripta los archivos con  la extensión .MS4 y pide un rescate por recuperarlos. Normalmente se distribuyen mediante la descarga de programas gratuitos de terceros,correo electrónico u otro vector de ataque diferente al producido por la falla en SMB de Microsoft.

**¿Se podrían recuperar los datosa día de hoy?**

A día de hoy es posible recuperar los datos mediante una copia de seguridad y reestablecer el sistema, sin embargo se sugiere utilizar una herramienta antimalware para removerlo, si la extensión .NM4 se encuentra en el buscador basta con descargar un add-on o complemento propio del navegador que permita removerlos, en el caso  del sistema operativo se puede utilizar una herramienta automatizada como SpyHunter para que analice las distintas partes del sistema y removerlo.

**¿Cuál ha sido el vector de entrada utilizado por esta amenaza?**

**Puertos Abiertos**

![](/images/modulo4/puertosabiertos.PNG)

Analizando la evidencia obtenida en spain.jpg se observa las conexiones en estado LISTENING que existen en el equipo. Este resultado se puede obtener mediante el comando.

`NETSTAT -AN|FINDSTR /C:LISTENING`

Los puertos que se muestran son:

**Puerto 139 -> NetBIOS**
Un puerto que hace que la red sea vulnerable a ataques informáticos, sirve para el intercambio de archivos y aplicaciones de impresoras.

**Puerto 445->SMB**
Permite compartir archivos a través de una red TCP/IP vulnerabilidad aprovechada por WannaCry pero que ya esta parchada en el sistema.

**Puerto 3389/tcp-> Terminal Service**
Este puerto es aprovechado por los atacantes de ransomware para hacer un ataque de fuerza bruta, apoderarse del servidor y luego saltarse a una base de datos y  cifrar archivos importantes. Para solucionar esto se necesita del parche de la vulnerabilidad CVE -2019-0708.

**5357->Web Services on Devices Aplication Programming Interface (WSDAPI)**
En este puerto existia una vulnerabilidad en el 2009 que corresponde a MS09-063 sin embargo esta no tiene efecto en sistemas Windows7.

Los puertos **49152, 49153, 49154, 49155, 49156** son puertos habilitados por defecto en sistemas Windows 7.

TCP 49152 listening wininit.exe
TCP 49153 listening svchost.exe
TCP 49154 listening svchost.exe
TCP 49155 listening services.exe
TCP 49156 listening lsass.exe

Con este análisis de los puertos se pudo obtener lo siguiente:

1. El  equipo  corresponde a un sistema operativo Windows 7 basándonos en los puertos 49152-49156.
2. Si bien es cierto que algunos puertos comunes son utilizados para los servicios de Microsoft  como  el  135,139,445,5357  yalgunos  fueron  vulnerables  en  el  pasadocon las actualizaciones se han venido parchando. Sin embargo,el puerto  3389no es un puerto que viene habilitado pordefecto.
3. A  pesar  que  este  equipo  esta  parchado  contra  la  vulnerabilidad  MS17-010 que previene  que  se  aproveche  la  vulnerabilidad  en  el  puerto  445  SMB. Esto no garantiza  que se  pueda  recibir  un  ataque  por  otro  puerto, en  el  enunciado  se menciona  acerca  de un  ataque  a servidorespor  lo que  presumiblemente  se  trate del   puerto   3389,   mismo   que mediante   fuerza   bruta,los   atacantes   pueden aprovechar,infectar con ransomwarey saltar a una base de datos.
4. Este  equipo probablemente  no  cuente con  el  parche  para CVE -2019-0708que corrige el fallode seguridad en el puerto 3389.

**Implementar contramedidas para la sede de Australiade cara a que no se vuelva a repetir este tipo de incidente.**

+ Implementar autenticación de dos pasos, para hacer más difícil al atacante obtener las credenciales.
+ Capacitar al personal para evitar ser victimas de campañas de phishing, a sospechar de enlaces en correos electrónicos o abstenerse de conectar pendrives que han sido encontrados en el exterior.
+ Limitar la cantidad de información que se provee por los empleados en las redes sociales, de forma que el atacante se le dificulte hacer el reconocimiento de su objetivo
+ Evitar colocar números telefónicos, cuentas bancarias o información de carácter confidencial.

## Implementar contramedidas para la sede de Madrid de cara a que no se vuelva a repetir este tipo de incidente.

+ Para evitar un futuro incidente de este tipo, se sugiere no dejar abiertos puertos que puedan ser aprovechados por los atacantes.
+ Mantener actualizado los sistemas operativos ya  que el sistema afectado correspondia a Windows 7, que ya se encuentra descontinuado, las versiones más actualizadas  como Windows 10 vienen con parches de seguridad y se actualizan regularmente.
+ En este caso también se recomienda parchar contra la vulnerabilidad **CVE -2019-0708** de mayo del 2019 para evitar el ataque por el puerto 3389.


## Referencias

1. Análisis de logs maliciosos Wireshark
   https://www.welivesecurity.com/la-es/2013/01/28/uso-filtros-wireshark-para-detectar-actividad-maliciosa/
2. Tshark tutorial and filter
   https://hackertarget.com/tshark-tutorial-and-filter-examples/
3. Autenticacion en dos pasos,que es, como funciona y porque deberías activarla
   https://www.xataka.com/seguridad/autenticacion-en-dos-pasos-que-es-como-funciona-y-por-que-deberias-activarla
4. Eliminar malware PC
   http://www.eliminarmalware.com/ransomware/nm4-ransomware-como-descifrar-archivos/
5. Urgente aplicar parche, Microsoft vulnerabilidad CVE-2019-0708 RDP Terminal Service
   https://medium.com/@cesarfarro/urgente-aplicar-parche-microsoft-vulnerabilidad-cve-2019-0708-rdp-donde-corre-terminal-service-f831e2778272
6. Microsoft Security Bulletin MS09-063
   https://docs.microsoft.com/en-us/security-updates/securitybulletins/2009/ms09-063
7. Windows 7 Listening Ports
   https://serverfault.com/questions/95467/windows-7-tcp-listen-ports
8. Conocer los puertos abiertos en Windows, sus peligros y como cerrarlos
   https://norfipc.com/recuperar/puertos-abiertos.html
   

   

