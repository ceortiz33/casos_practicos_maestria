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


