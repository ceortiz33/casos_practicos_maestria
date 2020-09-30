# Modulo 7 

## Enunciado

A lo largo de los contenidos se ha hablado de OWASP, análisis estático y análisis dinámico, vulnerabilidades, desarrollo seguro, etc. Los Smartphones no quedan libres de ataques. OWASP define un top 10 de riesgos en aplicaciones para dispositivos móviles

## Instrucciones

A partir de los contenidos estudiados y tras leer el enunciado anterior, analiza al menos tres aplicaciones (.apk) en busca de vulnerabilidades.

**Sugerencia**

+ Una apk no oficial, con vulnerabilidades deliberadamente introducidas, y que se publican en internet para prácticas de entrenamiento.
+ Una descargada de algún repositorio no oficial de apks.
+ Una descargada desde una tienda oficial.

Para el análisis se deben utilizar tanto herramientas stand-alone como online, de manera que produzcan resultados complementarios y completen un análisis exhaustivo de las aplicaciones.

Para las herramientas stand-alone pueden usarse las herramientas de la suite Androl4b: https://github.com/sh4hin/Androl4b

En cualquier caso, una vez seleccionadas las herramientas, se puede organizar el trabajo de análisis siguiendo las siguientes tareas:

1. Recolección de información (definir alcance y secciones a evaluar).
2. Análisis estático (observar recursos de la app, código fuente, ficheros de configuración, etc.).
3. Análisis dinámico (ejecutar la app y monitorizar la actividad).

Al finalizar las fases anteriores deberás discernir si existen algunas de las vulnerabilidades mas conocidas en estos dispositivos, como pueden ser: [CONCLUSION]

+ Hijacking y spoofing de intents.
+ Sticky broadcast tampering.
+ Almacenamiento inseguro.
+ Tráfico inseguro.
+ SQL injection.
+ Exceso de privilegios

Se deben clasificar las vulnerabilidades encontradas según OWASP-Mobile Top-10, y una valoración subjetiva de los riesgos asociados.

**Sugerencia**

Para ello se puede realizar un análisis en dos fases:

## Primera fase: análisis estático

+ Determinar el uso de ofuscadores de código.
+ Determinar si hay llamadas a funciones potencialmente insegura, detectando funciones que puedan ser peligrosas (y que históricamente se han relacionado con vulnerabilidades).
+ Analizar el fichero del manifiesto:
+ Declaraciones de intents, services, receivers, activities, content-providers.
+ Intercomunicación entre componentes, propiedad “exported”.
+ Permisos de componentes. En relación al tipo de app, ¿tiene permisos excesivos? ¿Tiene algún permiso que pueda ser sospechoso?

**Ejemplo**

+ Permiso: READ_SMS (usado en estafas de suscripción a servicios de mensajería, por ejemplo).
+ Receivers del tipo BOOT_COMPLETED. Los cuales podrían usarse para lanzar código malicioso cada vez que el terminal arranque.
+ Versión de SDK usada.
+ Propiedad “allowBackup” (permitiría realizar un backup de la aplicación mediante ADB, siempre que la propiedad de debugging USB esté habilitada).
+ Propiedad del “Flag debuggable” (¿está activada?).

## Segunda Fase: análisis dinámico

+	Análisis de las comunicaciones.
      + Mediante el uso de Burp Suite (como proxy) / wireShark o con el propio MobSF (mediante su módulo de análisis dinámico que permite visualizar las comunicaciones).
      + Detección de fugas de información si no hay cifrado (Check MiTM).
+ Análisis del comportamiento de la aplicación. Interacción con la app para generar logs y comunicaciones si las hubiera.
+ Almacenamiento inseguro (también detectable a partir de los permisos en análisis estático).
      + Archivos almacenados en tarjetas externas pueden ser leídos por cualquier app.
      + sharedPrefs: puede ser extraído. Evitar fuga de información a partir de los recursos alojados en él (chequear rutas /data/data/<package>/: ).
      + Content-providers (gestionan el uso compartido de información entre componentes: ficheros, DB sqlite, servicios externos).
      
# Insecurebankv2(App de entrenamiento)

**Descripción de la aplicación**


















