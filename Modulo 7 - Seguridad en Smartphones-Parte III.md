# Parte III

# Reca(Aplicacion de mirror de apks)

**Descripción de la aplicación**

Reca es una aplicacion de venta de articulos de ferreteria en Alemania descargada de un repositorio de apks externo conocido como www.apkmonk.com. Esta aplicacion tiene varias opciones News, Catalogue, Basket, Branches, Profile.

En News aparecen los nuevos articulos que han sido agregados al catalogo, cada articulo tiene su descripcion, si se da clic en uno de estos articulos aparece mayor informacion como tambien articulos sugeridos que podrian interesarle a los clientes.En la opcion catalogo se muestra el stock disponible de articulos relacionados a ferreteria y productos quimicos.

En la opcion Basket aparecen los articulos que se han agregado al carrito para su posterior compra si asi el cliente lo desea, en Branches se muestran las sucursales donde se puede encontrar los productos y aparecen dos opciones la sede en Hamburg y la sede de Hannover, Finalmente esta la opcion de Profiles o perfiles aparece el usuario que esta visitando la aplicacion, RECA por defecto indica que no es necesario tener una cuenta para ver sus productos, sin embargo para hacer la compra si se debe ingresar un perfil.

**Herramientas y alcance**

Para el análisis estático se usarán las siguientes herramientas, jadx en modo línea de comandos para decompilar la apk y MobsF como entorno para dar un primer vistazo a la seguridad y componentes de la aplicacion.

En el caso del análisis dinámico se usarán herramientas como Drozer y Frida con sus aplicaciones client y server respectivamente, además se usará Objection que es un framework que utiliza Frida como base de su funcionamiento y facilitara la enumeración de clases y método para hacer hooking.

Como emulador de Android se usa el emulador de Android Studio con un dispositivo Nexus 5x Google API aprovechando que esta versión viene rooteada a diferencia de su contraparte Nexus 5x Google Play que si bien viene con la App Store de Google no tiene acceso root.



