# Parte II

# Brainly(App de Google Play)

**Descripción de la aplicación**

Brainly es una aplicación de preguntas y respuestas, tiene tres modos principales de uso: modo teclado, modo cámara y modo micrófono. En modo teclado el usuario puede escribir su pregunta directamente y esperar que otro usuario de una respuesta, el modo cámara permite tomar fotos de las preguntas que uno quiera proponer, el modo micrófono de igual manera permite hacer una pregunta, pero mediante la voz.

El sistema que utiliza Brainly es el siguiente, un usuario pregunta y obtiene respuestas de los demás, las preguntas pueden ser calificadas con estrellas siendo 5 estrellas la calificación más alta, también pueden reaccionar si a las personas les gusto esa pregunta.

El objetivo de la plataforma es crear un repositorio de preguntas con temas variados, cada usuario empieza con un puntaje de 0 y en nivel principiante, a medida que se van superando varias preguntas el puntaje incrementa y de esta manera se sube de rango.

En la versión free no se ve una tabla de puntajes con las personas de mayor rango en la plataforma, pero si se puede ingresar a los perfiles de cada usuario y seguirlas o viceversa que esas personas te sigan. 

**Herramientas y alcance**

Para el análisis estático se usarán las siguientes herramientas, jadx en modo línea de comandos para decompilar la apk y Android Studio como entorno IDE para hacer las respectivas modificaciones como el Refactor que es de uso esencial con códigos ofuscados.

En el caso del análisis dinámico se usarán herramientas como Drozer y Frida con sus aplicaciones client y server respectivamente, además se usará Objection que es un framework que utiliza Frida como base de su funcionamiento y facilitara la enumeración de clases y método para hacer hooking.

Como emulador de Android se usa el emulador de Android Studio con un dispositivo Nexus 5x Google API aprovechando que esta versión viene rooteada a diferencia de su contraparte Nexus 5x Google Play que si bien viene con la App Store de Google no tiene acceso root.


