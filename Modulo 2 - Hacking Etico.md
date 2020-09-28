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

![](/images/modulo2/busquedasimple.PNG)

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













