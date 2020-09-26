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

## Resultados Obtenidos

OWASP ZAP clasifica las alertas en orden de prioridad como resultado del análisis se encontraron un total de 15 alertas, 6 alertas con riesgo alto, 4 alertas de riesgo medio, 5 alertas de riesgo bajo y no se encontraron alertas de tipo informacional.

![](/images/modulo3/summaryalerts.PNG)

Para una mejor visualización del impacto de los riesgos se utilizar una representación por color, siendo rojo riesgo alto, amarillo riesgo medio y verde riesgo bajo.

![](/images/modulo3/riesgocolor.PNG)

## Remote OS Command Injection

OWASP ZAP encuentra en el directorio **/passcheck.php**. Esta vulnerabilidad corresponde a ejecución no autorizada de comandos del sistema operativo. El parámetro atacado para esta vulnerabilidad es el **password**.

![](/images/modulo3/remoteos.PNG)
