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















