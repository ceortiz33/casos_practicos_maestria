# Modulo 1

## Caso 1 “El día 23 de noviembre de 2017 a las 10:00 am”

El día 23 de noviembre de 2017 a las 10:00 am recibes un correo electrónico desde una cuenta de correo corporativa de otro país, en el que se facilita un enlace a un sitio web sospechoso.
La cuenta desde la que se envía ese mensaje es: **m.walker.franch@ficticity.de**
____________________________________________________________________________________________________________________________________



**El asunto del correo es:** FW: Validate Email Account

**El cuerpo del mensaje es:**

This  is to  notify  all  employees  that  we  are  validating    active  accounts.  Please,  confirm that your account is still in use by clicking the validation link below:

Validate Email Account

Cheers,
__________________________________________________________________________________________________________________________________
El código del sitio web se encuentra en la carpeta “caso 1”

Al  comentarlo  con  el  resto  de  compañeros  del  equipo,  todos  ellos  explican  que  han recibido el mismo email.A continuación, empiezan a llegar incidencias de los empleados de la compañía reportando la recepción del email, indicando que les parece sospechoso.

   * Logs de emails recibidos por la cuenta **m.walker.franch@ficticy.de**
   
   * Codigo del sitio web (directorio “caso 1”)
   
## Antecedentes

![](/images/modulo1/Antecedentes.PNG)

En la imagen se muestra un archivo PHP donde se solicita el ingreso de usuario y contraseña, además se usa el encabezado de hotmail para aparentar ser una pagina inofensiva, de esta manera los empleados de Ficticity son victimas de phishing.

Los datos extraidos en la campaña de phishing son: la dirección IP de la víctima mediante el comando **getenv** , el usuario y contraseña que luego son enviados a la cuenta personal del atacante para hacer uso de las credenciales robadas.

**Sitio web Fraudulento**

![](/images/modulo1/ventana_phishing.PNG)

**Inspeccion de elementos del sitio web**

![](/images/modulo1/codigoHTML.PNG)

Haciendo  una  búsqueda  por  el código  HTML  se  puede  observar que el elemento  img1.jpg tiene un  formulario done se ejecuta el archivo PHP anteriormente mencionado.

**Logs de acceso m.walker.franch**

![](/images/modulo1/logs_access_walkerfranch.PNG)

Los logs de este usuario registran una actividad de la cuenta de correo **m.walker.franch@ficticy.de** entre las 9:00 am y las 16:00 pm durante los días 20 al 23 de noviembre del  2017,además de tener varios accesos desde distintos rangos de  IP: 172.16.10.23-26 y 77.72.83.26

**Correos enviados a m.walker.franch**

![](/images/modulo1/logs_excel.PNG)

En la imagen se puede observar la actividad que ha existido entre el atacante y los distintos empleados.Entre los cuales se puede destacar la interacción entre **j.roman.stelso@ficticy.co.uk** y **m.walker.franch@ficticy.de** que aparecen dos veces y cuyos asuntos son relacionados a **‘Sector Privado’**. El primero aparece a las 11:10 am el día 20 de noviembre del 2017 y la segunda ocasión es una respuesta con asunto **‘RE: Sector privado’** a las 17:15 pm al día siguiente. Una posible hipotesis sería que **j.roman.stelso@ficticy.co.uk** está filtrando información sensible y dándosela al atacante.

**Logs sospechosos**

![](/images/modulo1/logs_sospechosos.PNG)

**Correo de soporte de mailer bloqueado**

![](/images/modulo1/bloqueoMailer.PNG)

En el segundo correo se muestra como **support@mailer.com** envía  un correo al atacante con un asunto **‘Invoice’**. La actividad a la que se dedica mailer.com es a la compra de dominios,el atacante pudo obtener su dominio de esta página motivo por el cual recibió una factura por su compra.  El atacante  bloquea  los  correos  de  esta página para evitar las notificaciones que puedan vincularlo con mailer.com.

**Posibles colaboradores**

![](/images/modulo1/posiblesColaboradores.PNG)

El tercer y cuarto correo tienen como emisores a **m.wils.keicher@ficticy.de** y a **esitsecurity@ficticy.de** con los asuntos **‘freund’** que significa amigo y **‘neues Konto’** que significa nueva cuenta, además de poseer el mismo dominio que el atacante dando a entenderque el atacante no actuó solo.

**Servicio eFax Bloqueado**

![](/images/modulo1/servicioBloqueado.PNG)

Como se puede observar en el sexto correo, al igual que el segundo se bloquean las notificaciones de un servicio. En este correo se notifica al atacante de un servicio de envío de fax por internet con **efax.com** y asunto **‘Rechnung’** que traducido sería como Proyecto de ley.

En el séptimo correo **esitsecurity@ficticy.de** envía un correo al atacante con asunto **Actualizaciones** refiriendose a posibles cambios en la seguridad u otra informacion. En los siguientes dos correos **s.mick.resce@ficticy.de** envía correos con asuntos **nueva tarea** y **Re:nueva tarea** en donde el segundo correo tiene un archivo adjunto.

El siguiente correo tiene como emisor a **verify@microsoft.com**, este correo generalmente aparece cuando se han hecho múltiples intentos para validar una contraseña o para validar que el propietario de la cuenta sea real, debido a la naturaleza misma de este correo, el atacante se aprovecha y lo reenvia con la finalidad de obtener las credenciales de los empleados que no se percataron que es un reenvio del correo real. 

**Victimas de Phishing**

![](/images/modulo1/victimasPhishing.PNG)

Las cuentas de correo m.will.smith@ficticy.us, l.stephan.martin@ficticy.co.uk, l.martin.freire@ficticy.es, j.rodriguez.maceda@ficticy.es y s.mick.resce@ficticy.nl responden al falso correo de recuperacion de contraseña motivo por el cual se puede concluir que estos correos fueron de aquellos que  cayeron en el phishing.

**Posibles nuevos ataques**

![](/images/modulo1/nuevosAtaques.PNG)

Las cuentas de correo **j.mach.christ@ficticy.de** y **r.voil.chran@ficticy.de** mencionan un **‘Nuevo Proyecto’** y **‘El proyecto’** respectivamente dando a entender que podrían haber futuros ataques a la misma empresa o a otras. Finalmente, **noreply@proveedor.de** envía un correo con un archivo adjunto y asunto **RE: Rechnung’** que vendría a ser la respuesta del **‘Proyecto de ley’** mencionado en los primeros correos.

## Identificar,  analizar  y  detallar  con  todo  lujo  de  detalles  en  el  primer caso:

### ¿Cómo se ha podido producir el suceso?

De acuerdo a los correos mostrados en los logs se puede observar que el principal sospechoso apunta a la cuenta de correo **j.roman.stelso@ficticy.co.uk** debido a que pudo dar información de carácter confidencial al atacante, este seria el vector de entrada para realizar la campaña de phishing y engañar a los empleados de la organizacion y sustraer sus credenciales mediante un CEO Fraud.

![](/images/modulo1/pregunta1.PNG)

Adicionalmente  luego  de  la  comunicación  con  el  correo **j.roman.stelso@ficticy.co.uk** existen dos correos que involucran servicios de compra de dominios y mensajería por fax por internet motivo otro motivo más por el cual se puede sospechar de él. Otra cosa que se puede percatar es que son los únicos correos que se bloquean para que no puedan ser rastreados.

![](/images/modulo1/pregunta1_1.PNG)

### ¿Dónde se envían los datos comprometidos?

Los datos comprometidos se envían al correo **m.walker.franch@ficticy.de**

![](/images/modulo1/pregunta2.PNG)

### ¿Qué cuentas se han podido ver comprometidas? ¿Cómo podemos identificarlas?

Las cuentas que han sido comprometidas son:

1. **m.wils.smith@ficticy.us**
2. **l.stephan.martin@ficticy.co.uk**
3. **l.martin.fierre@ficticy.es**
4. **j.rodriguez.maceda@ficticy.es**
5. **s.mick.resce@ficticy.nl**

Debido a que el atacante reenvía un correo de confirmación de Outlook, las víctimas no sospecharían que se trata de un phishing a menos que chequearan el asunto y las personas a las que se les está reenviando dicha información.

![](/images/modulo1/pregunta3.PNG)

### Medidas de Mitigación que se pueden tomar en estos incidentes

* Desconfiar de correos ajenos a la empresa.
* Capacitación al personal y clientes acerca de los peligros al facilitar contraseñas sin percatarse del origen de los correos.

### Recomendaciones y Plan de Continuidad alequipo de seguridad

* Llevar un historial de todos los dominios relacionados a la empresa.
* Llevar un registro de los correos de losproveedores y clientes.
* Establecer canales de comunicación más eficiente entre los distintos departamentos de la empresa.
* Llevar un registro de las intrusiones y enviarlas a la lista negra para ser bloquedos.

## Caso 2: “Unas horas más tarde a las 13:00** 

“Unas  horas más tarde, a las 13:00, recibes la noticia de que se ha realizado una transferencia a una cuenta inusual y que el proveedor pendiente de recibir ese pago se ha quejado porque no lo ha recibido.

Al contactar con los empleados del departamento de transferencias(acontinuación, se detallan sus datos), todos confirman haber estado trabajando durante toda la semana en la oficina, siempre utilizando la red corporativa y la VPN para el uso de herramientas. Sin embargo, los tres trabajadores afirman haber recibido correos sospechosos esta semana.

**Empleados del departamento de transferencias**

* **Nombre:** María Protector Fresco | **Email:** m.protector.fresco@ficticy.es| **Puesto:** responsabledel departamento de nóminas | **Redes sociales utilizadas:** Facebook, Twitter, Linkedin, Instagram.
* **Nombre:** Juan Philips Todobene | **Email:** j.philips.todobene@ficticy.es| **Puesto:** Responsable  de  pagos  y  transferencias  | **Redes  sociales  utilizadas:** Facebook, Linkedin, Infojobs.
* **Nombre:** Sofía Labial Guest | **Email:** s.labial.guest@ficticy.es| **Puesto:** Asistente de  pagos  y  transferencias  | **Redes  sociales  utilizadas:** Facebook,  Linkedin, Infojobs, Tuenti.

**Información facilitada para el análisis**

* Logs de correos recibidos.
* Logs de acceso a las cuentas de correo.
* Logs del MTA.
* Correos sospechosos reportados por los empleados.
  * Para obtener la contraseña de dicho ZIP, se debe identificar el ID del correo fraudulento.

## Identificar, analizar y explicar con todo lujo de detalles en el segundo caso:

## Antecedentes

**Logs de acceso Juan Phillips Todobene**

![](/images/modulo1/logs_acceso_caso2.PNG)

Los logs de acceso de la cuenta **j.phillips.todobene@ficticy.es** correspondiente a Juan Phillips Todobene, Responsable de pagos y transferencias, donde no se registran anomalías en cuanto a su versión ya que usa la VPN de la empresa y ha tenido actividad entre las 8:00 AM a las15:20 PM desde los días 20 al 23 de noviembredel 2017.

**Logs de acceso María Protector Fresco**

![](/images/modulo1/logs_fresco.PNG)

Los logs de acceso de la cuenta **m.protector.fresco@ficticy.es** correspondiente a María Protector Fresco, responsable del departamento de nóminas, donde no se registran anomalías en cuanto a su versión ya que usa la VPN de la empresa y se registra actividad desde las 08:00 AM hasta las 15:10 PM,los días 20,21,22 y 23 de noviembre del 2017. 

**Logs de acceso Sofía  Labial  Guest**

![](/images/modulo1/logs_labial.PNG)

Los logs de acceso de la cuenta **s.labial.guest@ficticy.es** correspondiente  a  Sofía Labial Guest, asistente de pagos y transferencias, donde no se registran incongruencias en su versión ya que usa la VPN de la empresa y registra actividad desde las 08:50 AM hastalas 15:12 PM los días 20, 21,22 y 23 de noviembre del 2017.



![](/images/modulo1/logs_phillip.PNG)

En  los  logs  de  la  actividad  de  los  correos  enviados  a j.philips.todobene@ficticy.essepuede observar tres correossospechosos provenientes de transfer@mailer.com

Dentro  de  las  evidencias  proporcionadas  se adjunta  un  archivo.zipcodificadoque contiene los mensajes enviados por transfer@mailer.com,donde se menciona que para desbloquearlo se requiere el ID del correo sospechoso en este caso fue 27665. Una vez los archivos fueron descomprimidos se generaron tres archivos de texto:

* New account.msg.txt 
* RE New account.msg.txt
* New account (1). msg.txt

El contenido de estos archivos tiene una parte codificada en formato base64. Por lo que para    poder    ver    el    contenido    se    usó    una    página    web    para    decodificar https://www.base64decode.org/.

![](/images/modulo1/mensaje_cifrado.PNG)

![](/images/modulo1/mensaje_decodificado.PNG)

En  este  mensaje  el  proveedor  IT  le  solicita  el  cambio  de  la  cuenta  bancaria a j.philips.todobene@ficticy.esa la nueva cuenta INTEXER67 1026 7842 0814 5674 1236 8905

![](/images/modulo1/mensaje_codificado2.PNG)

![](/images/modulo1/mensaje_decodificado2.PNG)

se observa la respuesta de Juan Philips al proveedor IT en el que le solicita undocumento que valide el cambio de cuenta.

![](/images/modulo1/mensaje_codificado3.PNG)

![](/images/modulo1/mensaje_decodificado3.PNG)

Como se puede observar aquí el “proveedor IT” presiona a la persona responsable de los pagos y transferencias para que le ayude a realizar elcambio de cuenta dando fe de que le proporcionara con los documentos pertinentes para la habilitación lo más rápido posible cuando  en  realidad  esto  nunca  pasó.  Aquí  se  puede  ver  un  caso  de  ingeniería  social mediante el cual una persona desconocida se hacepasar por el proveedor de servicios IT, dándole más seguridad a la persona responsable para que esta le facilite con el cambio de cuenta y que pueda saltarse las políticas de seguridad dado a que esta persona le entregaría los documentos después.

![](/images/modulo1/phishing_mach.PNG)

Además de hacer el cambio de la cuenta bancaria para realizar los pagos. Se realiza un phishing a lacuenta j.mach.christ@ficticy.co.ukcon la cuenta j.mach.christ@ficticy.derecordando que en el caso anterior los correos con dominio .de  para la empresa ficticy  eran  sospechosos,  dado  que   los correos son  válidos  para España,  Reino  Unido,  Países Bajos, Estados Unidos con dominios .es, .uk, nl, us respectivamente.

![](/images/modulo1/mails_fresco.PNG)

De acuerdo a la versión de María Protector Fresco también recibió correos sospechosos, lo cual no se refleja en los logs ya que los posibles errores en los pagos pudieran darse por cosas propias del sistema, debido a que la aprobación del cambio de cuentabancariase produjo alrededor de las 15:00 PM el día 23 de noviembre del 2017.

![](/images/modulo1/mails_guest.PNG)

Para este caso sepuede apreciar también un requerimiento de transferenciapor parte de j.mach.christ@ficticy.co.ukperoesta vez hacia la asistente de pagos y transferencias. En donde se puede ver a la cuenta de phishing j.mach.christ@ficticy.de

![](/images/modulo1/logs_transferencias.PNG)

Como  se  puede  observar  en  la  imagen  2.14  los  correos  en  color  naranja,  hay  varios intentos  de  establecer  una  comunicación  con  el  correo  del  responsable  de  los  pagos  y transferencias. Donde la comunicación no se ha establecido por ende aparece el estado “dropped”,Una vez logra dar con el correo del  responsable de transferencias se procede a realizar la ingenieria social para que se realice el cambio de cuenta bancaria.

### ¿Cómo se ha podido producir el suceso?

El  suceso  se  produce  debido  a  que  el  atacante  realiza  ingeniería  social  para  que  el responsable  de  los  pagos  y  transferencias  le  facilite  el  cambio  de  cuenta  bancaria, mientras el simula ser un proveedor IT, dado que al ser un contacto “seguro” podría saltarse las políticas de seguridad implantadas en la empresa ficticy.

### ¿Qué método ha podido utilizar el atacante para realizar este envío dirigido?

El atacante pudo haber realizado una búsqueda en Google o en otros buscadores como Bing,  las  redes  sociales  que  contenían información  delresponsable  de  los  pagos  y transferenciasque en este caso son: Facebook, LinkedIn, Infojobs.

### ¿Cómo se ha podido utilizar?

Al realizar la búsqueda y encontrar que Juan Philips TodoBene era el responsable de los pagos  y  transferencias  de  la  empresa  ficticy  en  España,  todo  lo  que  hizo  fue  probar combinaciones del nombre y los apellidos como se muestra a continuación.

![](/images/modulo1/pregunta2_3.PNG)

**Resumen del caso**

En el caso 2 se abordo temas como el phishing la ingeniería socialy búsquedas en internet. El problema ocurre a raíz de la ingeniería social que aplica el atacante hacia la persona responsable de los pagos y transferencias. Una vez que el atacante logra convencer a esta persona que le haga el cambio de cuenta bancaria, se hace pasar por un proveedor y crea una cuenta de correo con un dominio que no existe dentro de la compañía ficticy, en este caso un domino .de que corresponde a Alemania.

Logrando que el pago se lo hagan al atacante con la nueva cuenta bancaria proporcionada. Para poder contactar con la cuenta j.philips.todobene@ficticy.es,se hizo muchos intentos de combinaciones para dar con la cuenta correcta usando búsqueda en internety en las redes sociales de dicha persona.De esta manera el atacante burla las políticas de seguridad y  causa  perjuicioseconómicosa  la  compañía  ficticy  al  recibir  un  pago  de  uno  de  sus proveedores.

**Medidas de mitigaciónque se pueden tomar en estos incidentes**

* No  facilitar informaciónsensible  sin  importar  el origen,  siempre  respetar  las políticasde seguridad y el debido procedimiento.
* Ser mas cuidadoso respecto a la informaciónmostrada en redes sociales.
* En lo posible reportar los correos sospechosos para que estos sean analizados  y detectados lo másrápidoposible.

**Recomendaciones y plan de continuidad para el equipo de seguridad**

* Crear políticasde seguridad más fuertes a la hora de facilitar informaciónsensible.
* Notificar al departamento de seguridad cada cambio que se registre en cuanto a informaciónque facilite dinero u otros activos importantes para la empresa.

**Caso 3: Para acabar el díaa  las  15:00**

Para acabar el día, a las 15:00, empiezan a generarse múltiples alertas en los sistemas de endpoint indicando el siguiente mensaje

**Mensaje sistema endpoint**

“#   C:\WINDOWS\mssecsvc.exe   #   Ransom-WannaCry!7339A0EFC768   #   trojan   # deleted  #  1  #  VIRUS_DETECTED_REMOVED  #  VIRUSCAN8800  #  VirusScan Enterprise #

Al mismo tiempo, comienzan a cifrarse archivos de múltiples equipos, a los que se les va añadiendo  la  extensión “. wncry”. Decara  al  análisis  del  incidente,  se  dispone  de  la siguiente información

* Logs de ePO.
* Ejemplo de ficheros creados en los sistemas infectados.
* Capturas de pantalla de la configuración del Firewall.
* Logs del Firewall.

## Identificar, analizar y explicar con todo lujo de detalles en el tercer caso:

## Antecedentes

![](/images/modulo1/ransomware.PNG)

En el caso de un Ransomware su funciónconsiste en ‘secuestrar’los datos de la víctima, codificándolosde  tal  forma  que  estos  seaninaccesibles  sin  la  debido configuracióno introducciónde una clave en especial. En este caso en particular se guíaal usuario a correr un archivo .exe,para poderrecuperar los archivos encriptados.

En algunos casos se exige un pago  generalmente con bitcoins al autor del ransomware para que este proceda a desencriptar los archivos y de esta manera salvar la informaciónimportante de la empresa ficticy.

![](/images/modulo1/firewall.PNG)

a pesar de utilizar una VPN, todo el tráfico estápermitido, lo que podríallevar a fallos en la seguridad.

![](/images/modulo1/logs_antivirus.PNG)

se  puede  apreciar  que  el  ransomware WannaCry  es  detectado,  y  se aprovecha de una vulnerabilidad existente en el sistema Windows 2008 R2 Server Pack #1,  la  cual  al  realizar  una búsquedaen  la  base  de  datos  de  Microsoft  se  encuentra que corresponde a la vulnerabilidad MS17-010

![](/images/modulo1/bulletin.PNG)

![](/images/modulo1/sistemas_afectados.PNG)

![](/images/modulo1/logs_firewall.PNG)

Como se puede observar en los logs del firewall todas las peticionesse aprueban, pasandopor el puerto 445,el cual ayuda a propagar el ransomware Wanacryen el sistema.

### ¿Qué tipo de amenaza se ha sufrido?

El  sistema  endpointha  sido  infectado  con  el  malware  Ransomware  Wanacry  el  cual codifica los archivos del sistema,les añade la extensiónWNCRYlo cual haceimposible leerlos sin la contraseña adecuadaque en ocasiones se encuentra dentro del sistema y en otras debe ser dada por el autor.

![](/images/modulo1/files.PNG)

### ¿Cómo se ha podido producir el suceso?

El  suceso  se  ha  producido  debido  a  las  pocas  medidas  de  seguridad  en  el  firewall  del sistema, donde todo trafico que entra es aprobado a pesar de utilizar una VPN, ademáspara que esto ocurriese fue necesario que el puerto 445 estuviera expuesto a Internet y asíse logro infectar.

### ¿Cómo se propaga el malwarea través de la red interna?

El malware se propaga a travésde la red interna mediante el puerto 445, el cual puede observarse en los logs del firewall, donde todas las peticiones a este puerto son aprobadasdebido a  las  pocas  medidas de  seguridad en  el  firewall  como  filtrosde tráficoentrante haciadistintos puertos.

![](/images/modulo1/logs_puertos.PNG)

### ¿Qué vulnerabilidad ha podido explotarse?

La vulnerabilidad que se ha explotado es la MS17-010, que afecta a los sistemas Windowsy en este caso al Windows Server 2008 R2 Service Pack 1.Una de las vulnerabilidades que permitíala ejecuciónde códigoremoto en dispositivos Windows que no hayan sido parchados.

### Medidas de mitigaciónque se pueden tomar en estos incidentes.

* Reportarde manera inmediata al equipo de seguridad para que décon el paradero del autor, o haga lo posible por desencriptar los archivos.
* Aislar los sistemas infectados.
* Volvera una versiónanterior de una copiade seguridad del sistemapara poder tener un respaldo de los archivos antes que se infectaran

### Recomendaciones y plan de continuidad que debe realizar el equipo de seguridad

* Parchar  o  Actualizar  a  la últimaversiónlos  sistemas  para  corregir diversas vulnerabilidades.
* Aplicar filtros mas rígidosal trafico entrante que pasa por el Firewall.
* Reportar  y  comunicarse  con  otros  grupos  de  ciberseguridad  para notificarde  la presencia deun malware.


