# Modulo 1

**Caso 1“El día23 de noviembre de 2017 a las 10:00 am”**

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
   
   * Codigodel sitio web (directorio “caso 1”)
   
## Antecedentes

![](/images/modulo1/Antecedentes.PNG)

Como se puede apreciar en la imagen, el atacante crea un archivo PHP para realizar el phishing simulando ser una página de Outlook común y corriente, de esta manera engana al usuario que no se percata del engaño, entrega su usuario y contraseña.

Adicionalmente mediante la función getenv de PHP captura ladirección IP de la víctima;una vez la obtiene, la añade a $message junto al usuario y contraseña. De esta manera con dichos datos el atacante procede a enviar un correo a su cuenta personal para hacer uso de las credenciales robadas.

Los elementos utilizados en la creación del sitio fraudulento para el caso son el botón de sign in y el entorno falso de Outlook como se muestra en las siguientes imágenes.

![](/images/modulo1/ventana_phishing.PNG)

Haciendo  una  búsqueda  por  el código  HTML  se  puede  observar  que  al  localizar  el elemento  img1.jpg,  dentro  de  él existe  un  formulario con  la etiqueta  form en el  que  se ejecuta el archivo PHP

![](/images/modulo1/codigoHTML.PNG)

![](/images/modulo1/logs_access_walkerfranch.PNG)

se   muestra la   actividad   de   la   cuenta   de   correo m.walker.franch@ficticy.de varía entre las 9:00pm y las 16:00 pm durante los días 20 de noviembre  del  2017  al  día  23  de  noviembre  del  2017,  además  de  tener  un  rango de  IP entre la 172.16.10.23-26 y una dirección de 77.72.83.26

![](/images/modulo1/logs_excel.PNG)

se puede observar la actividad que ha existido entre el atacante y los distintos usuarios.Entre los cuales se puede observar particularmente la interacción entre j.roman.stelso@ficticy.co.uk y m.walker.franch@ficticy.de dondeaparecen dos veces y cuyos asuntos son en la primera ocasión ‘Sector Privado’ a las 11:10 am el día20  de noviembre del 2017 y la segunda ocasión una respuesta con asunto ‘RE: Sector privado’ a las 17:15 pm al día siguiente. Una posible teoría seria que j.roman.stelso@ficticy.co.uk está filtrando información sensible y dándosela al atacante.

![](/images/modulo1/logs_sospechosos.PNG)

![](/images/modulo1/bloqueoMailer.PNG)

En  el segundo  correo  muestra  como support@mailer.com envía  un correo al atacante con un asunto ‘Invoice’, mailer.com es un servicio de compra de dominios, el atacante pudo obtener su dominio de esta página motivo por el cual recibió una  factura  por  su  compra.  El atacante  bloquea  los  correos  de  esta página para  no ser rastreado poresta página.

![](/images/modulo1/posiblesColaboradores.PNG)

El tercer y cuarto correo tienen como emisores  am.wils.keicher@ficticy.de y  a esitsecurity@ficticy.de con los asuntos ‘freund’  que significa amigo y ‘neues Konto’ que significa nueva cuenta, además de poseer el mismo dominio que el atacante lo que podría significar que el atacante no actuó solo.

![](/images/modulo1/servicioBloqueado.PNG)

Como se puede observar en el sexto correo, al igual que el segundo, esenviado por un servicio  de  envío  de  fax  por  internet  llamado  efax.com con asunto ‘Rechnung’ que traducido sería algo como Proyecto de ley. El atacante bloquea los correos provenientes de este emisor.

En  el  séptimo  correo, esitsecurity@ficticy.deenvíaun  correo  al  atacante  con asunto Actualizaciones.En los siguientes dos correos s.mick.resce@ficticy.deenvíacorreos con asuntos nueva tarea y Re: nueva tarea respectivamente, donde el segundo correo tiene un archivo adjunto.

El siguiente correo,verify@microsoft.com,este correo generalmente aparece cuando se han hecho múltiples casos para validar una contraseña o para validar que el propietario de la cuenta sea real, debido a esto el atacante se aprovecha de este correo y simula ser un correo de notificación y reenvía este correo a las víctimas. En los siguientes correos m.will.smith@ficticy.us,l.stephan.martin@ficticy.co.uk,l.martin.freire@ficticy.es,  j.rodriguez.maceda@ficticy.es, s.mick.resce@ficticy.nlresponden al atacante ,el asunto  es  RE:  FW:  Validate  Email  Account  motivo  por  el  cual  se  puede  sospechar  que  estos correos fueron aquellos que  cayeron en el phishing

![](/images/modulo1/victimasPhishing.PNG)

Despuésj.mach.christ@ficticy.dey r.voil.chran@ficticy.dehacen referencia a un ‘Nuevo Proyecto’ y ‘El proyecto’ respectivamente  loque  podría  significar  futuros  ataques  a  la misma   empresa   o   a otras   como   se   muestra   en   la   imagen   1.12.   Finalmente, noreply@proveedor.deenvíaun  correo  con  un archivo  adjunto  con  el  asunto  RE: Rechnung’ que vendría a ser la respuesta del ‘Proyecto de ley’.

![](/images/modulo1/nuevosAtaques.PNG)

## Identificar,  analizar  y  detallar  con  todo  lujo  de  detalles  en  el  primer caso:

### ¿Cómo se ha podido producir el suceso?

De  acuerdo  a  los  correos  mostrados  en  los  logs  se  puede  observar  que  el  principal sospechoso apunta a la cuenta de correo j.roman.stelso@ficticy.co.ukdebido aque pudo filtrar información de carácter confidencial al atacante, de esta forma se logra establecer el  engaño  mediante  el  phishing,  pero  al  verse  comprometida  la  información  de  una empresa con un dominio diferente da como resultado un CEO Fraud.

![](/images/modulo1/pregunta1.PNG)

Adicionalmente  luego  de  la  comunicación  con  el  correo j.roman.stelso@ficticy.co.ukexisten dos correos que involucran servicios de compra de dominios y mensajería por fax por internet motivo otro motivo más por el cual se puede sospechar de él. Otra cosa que se puede percatar es que son los únicos correos que se bloquean para que no puedan ser rastreados.

![](/images/modulo1/pregunta1_1.PNG)

### ¿Dónde se envían los datos comprometidos?

Los datos comprometidos se envían al correo m.walker.franch@ficticy.de

![](/images/modulo1/pregunta2.PNG)

### ¿Qué cuentas se han podido ver comprometidas? ¿Cómo podemos identificarlas?

Como se puede ver a continuación cinco cuentas han sido comprometidas las cuales son:

1. m.wils.smith@ficticy.us
2. l.stephan.martin@ficticy.co.uk
3. l.martin.fierre@ficticy.es
4. j.rodriguez.maceda@ficticy.es
5. s.mick.resce@ficticy.nl

Debido a que el atacante reenvía un correo de confirmación de Outlook, las víctimas no sospecharían que se trata de un phishing a menos que chequearan el asunto y las personas a las que se les está reenviando dicha información.

![](/images/modulo1/pregunta3.PNG)

### Medidas de Mitigaciónque se pueden tomar en estos incidentes

* Desconfiar de correos ajenos a la empresa.
* Capacitación al personal y clientes acerca de los peligros al facilitar contraseñas sin percatarse del origende los correos.

### Recomendaciones y Plan de Continuidad alequipo de seguridad

* Llevar un historial de todos los dominios relacionados a la empresa.
* Llevar un registro de loscorreos de losproveedores y clientes.
* Establecer canales de comunicación más eficiente entre los distintos departamentos de la empresa.
* Llevar un registro de las intrusiones y enviarlas a la lista negra para ser bloquedos.

**Caso 2: “Unashoras mástarde a las 13:00** 

“Unas  horas mástarde,  a  las  13:00,  recibes  la  noticia  de  que  se  ha  realizado  una transferencia a una cuenta inusual y que el proveedor pendiente de recibir ese pago se ha quejado porque no lo ha recibido.

Al  contactar  con  los  empleados  del  departamento  de transferencias(acontinuación,  se detallan sus datos), todos confirman haber estado trabajando durante toda la semana en la oficina, siempre utilizando la red corporativa y la VPN para el uso de herramientas. Sin embargo, los tres trabajadores afirman haber recibido correos sospechosos esta semana.

**Empleados del departamento de transferencias**

* **Nombre:** María Protector Fresco | **Email:** m.protector.fresco@ficticy.es| **Puesto:** responsabledel departamento de nóminas | **Redes sociales utilizadas:** Facebook, Twitter, Linkedin, Instagram.
* **Nombre:** Juan Philips Todobene | **Email:** j.philips.todobene@ficticy.es| **Puesto:** Responsable  de  pagos  y  transferencias  | **Redes  sociales  utilizadas:** Facebook, Linkedin, Infojobs.
* **Nombre:** Sofía Labial Guest | **Email:** s.labial.guest@ficticy.es| **Puesto:** Asistente de  pagos  y  transferencias  | **Redes  sociales  utilizadas:** Facebook,  Linkedin, Infojobs, Tuenti.

**Informaciónfacilitada para el análisis**

* Logs de correos recibidos.
* Logs de acceso a las cuentas de correo.
* Logs del MTA.
* Correos sospechosos reportados por los empleados.
  * Para obtener la contraseña de dicho ZIP, se debe identificar el ID del correo fraudulento.

## Identificar, analizar y explicar con todo lujo de detalles en el segundo caso:

## Antecedentes

![](/images/modulo1/logs_acceso_caso2.PNG)

se  muestran  los logs  de  acceso  de la cuenta j.phillips.todobene@ficticy.escorrespondiente a Juan Phillips Todobene, Responsable de pagos y transferencias, donde no se registran anomalíasen cuanto a su versión ya que usa la VPN de la empresa y ha tenido actividad entre las 8:00 AM a las15:20 PM desde los días 20 al 23 de noviembredel 2017.

![](/images/modulo1/logs_fresco.PNG)

se    pueden    observar    los    logs    de    acceso    de    la   cuenta m.protector.fresco@ficticy.escorrespondiente a María Protector Fresco, responsable del departamento de nóminas, dondeno se registran anomalías en cuanto a su versión ya que usa la VPN de la empresa y se registra actividad desde las 08:00 AM hasta las 15:10 PM,los días 20,21,22 y 23 de noviembre del 2017. 

![](/images/modulo1/logs_labial.PNG)

Se  observan  los  logs  de  acceso  de  la  cuenta s.labial.guest@ficticy.escorrespondiente  a  Sofía  Labial  Guest,  asistente  de  pagos  y transferencias, donde no se registran incongruencias en su versión ya que usa la VPN de la empresa y registra actividad desde las 08:50 AM hastalas 15:12 PM los días 20, 21,22 y 23 de noviembre del 2017.

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

















