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

**Log sospechoso mostrado en Wireshark

![](/images/modulo4/suspiciouslog.PNG)

Se puede realizar el mismo procedimiento usando Tshark con el siguiente comando

`Tshark -r /root/Downloads/australia.pcap -Y “dns”`

**Log sospechoso mostrado en Tshark

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

* hifi@invent.com:123dmr
+ hjerf@invent.com:applepup
+ jdarwin@invent.com:redcar#

## Italia





