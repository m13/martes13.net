---
layout: post
title: Infectando aplicaciones Android
post_id: 69
categories: 
- android
- bug
- google
- infectar
---

**Note: I do not care if you want to share or translate the document, but please, quote me 
![;)](http://s1.wp.com/wp-includes/images/smilies/icon_wink.gif?m=1221158483g) . Thanks!**


### Resumen

Es posible infectar cualquier aplicación del sistema operativo Android hasta el próximo 
rebootdel 
smartphone. Esto requiere de  una aplicación maliciosa con un exploit que de permisos root, es decir, se restringe a todo lo inferior a la Gingerbread (<2.3.x) que son más del 90% de dispositivos. Es la misma técnica que se utilizo con el DroidDream, pero en vez de crear nuevas aplicaciones e instalarlas, se aprovechan aplicaciones ya existentes.

### DroidDream

El virus DroidDream ha sido el primero en cuestionar la seguridad en Android. Los pasos que hace son los siguientes:

1. Se instala a través de varias aplicaciones de varios usuarios (unas 30 apps entre 3 usuarios).
2. Estas apps, ejecutan 2 exploits: exploid y/o rage-against-the-cage, obteniendo root.
3. Aún si no se obtiene, se envia el IMEI, ISMI, version de Android, etc. a un webservice del atacante.
4. Instala una nueva aplicación, la cual se encarga de robar más datos.

Se puede obtener mucha más información en esta dirección (y los enlaces a los que apunta):
[http://blog.fortinet.com/android-droiddream-uses-two-vulnerabilities/](http://blog.fortinet.com/android-droiddream-uses-two-vulnerabilities/)

Lo que interesa es preguntarse: ¿Porque instala una nueva aplicación?

### Seguridad by Google y sistema de firmas

Google es capaz de borrar remotamente aplicaciones del móvil (por nuestra seguridad...). Dado que todas las aplicaciones tienen una firma única, Google puede dirigirse a cada una de ellas inequívocamente. Así que la razón por la que instalaron los atacantes una nueva aplicación externa fue justamente para que Google no conociera su firma y no pudiera borrarla.

Poco después, la comunidad XDA-Developers descubrió un parche, tan simple como crear el fichero "/system/bin/profile" a través de la shell. Más información en 
[http://forum.xda-developers.com/showthread.php?t=977154](http://forum.xda-developers.com/showthread.php?t=977154)

Volviendo atrás, cuando un programador crea una aplicación, utiliza una clave privada que certifica que esa aplicación es suya y no es modificable por nadie más. Es muy importante guardar bien esa clave privada fuera del alcance de malas manos, sino se puede refirmar una aplicación como si fuera original. Aún así, el sistema Android permite ejecutar cualquier aplicación firmada (hasta modo 
debug), donde firmada significa que el contenido de "/META-INF/*" que se encuentra en el interior del APK lleva todas las firmas de todos los ficheros incluidos en el APK y se corresponden los hashes SHA1-DIGEST. Un ejemplo:

[![ejemplo_metaInf](/images/2011/03/ejemplo_metainf.png?w=300)](/images/2011/03/ejemplo_metainf.png)

El sistema operativo comprueba en 2 ocasiones que los paquetes esten firmados:

1. Cuando se INSTALAN del Market o de cualquier otro sitio (consola)
2. Cuando se reinicia el sistema operativo comprueba que son validos, sino los desactiva

Entonces, ¿y si se mueve directamente a la carpeta de "/data/app/*.apk"? ¡Zas!

### Reemplazando aplicaciones existentes

Para hacerlo divertido, vamos a hacer un ejemplo. (En esta ocasión, no explicaré como se hace reversing a aplicaciones Android, pero con 
[apktool](http://code.google.com/p/android-apktool/), 
[dex2jar](http://code.google.com/p/dex2jar/) y el DDMS de Eclipse hay de sobras.)

Lo primero, escogemos una victima. La aplicación será la Esdeveniments, una cutre aplicación del PSC que sólo tiene un WebView.

1. [![](/images/2011/03/descarga_psc.png?w=300)](/images/2011/03/descarga_psc.png) <br/>La instalamos en el smartphone
2. La enviamos al pc a través del ASTRO Manager o del DDMS (obtened root en la shell con el OneClick)
3. Renombramos el APK a ZIP
4. Abrimos el ZIP, y reemplazamos los icon por calaveras y el main.xml lo editamos (ojo que tendréis que pasar de raw a ascii). <br/> [![](/images/2011/03/main_xml1.png?w=300)](/images/2011/03/main_xml1.png)
5. Se vuelve a renombrar como APK, y con el DDMS, se hace push del fichero a /data/app/nombre_original.apk

El resultado es que, aunque el icono del escritorio no cambia (a excepción de volverlo a arrastrar y recrearlo), cuando se abre la aplicación:

[![](/images/2011/03/sorpresa1.png?w=180)](/images/2011/03/sorpresa1.png)

Hay que acordarse que para hacer esto, se necesitan permisos root (hacer igual que DroidDream y ejecutar nativamente el exploit), pero se puede automatizar el proceso de forma muy sencilla para hacerse en TODAS las aplicaciones instaladas en el movil. Es muy facil desde una aplicación programar que abra un APK, lo examine, modifique el main.xml, un archivo *.so (si se trabaja con NDK), o cualquier otro recurso, y lo vuelva a empaquetar. Imaginad que hace esto que digo de infectaros todas las aplicaciones... y reiniciáis el móvil :D. Adiós a todas vuestras aplicaciones. (un 
geek podría volvérselas a poner, o usar el recovery, pero ¿¿acaso no llegarían quejas a los proveedores de móviles??)

### Entonces, ¿qué?

La seguridad de Android reside EXCLUSIVAMENTE en no ser vulnerable a un exploit. Aunque aquí se haya mostrado que se puede infectar otras apps, también se puede reprogramar el sistema entero, instalando rootkits que no sois capaces de imaginar. Os animo a pedir a vuestros proveedores (movistar, vodafone, etc) que os ofrezcan las últimas actualizaciones del sistema operativo (GingerBread, 2.3.3), o sino, quien sabe si la próxima vez que abráis la aplicación de Gmail o Facebook sale mi cara :D

### Fe de erratas

Sin querer, aprete al botón de enviar mientras hacia pruebas. Lo siento...

[![](/images/2011/03/esdeveniments.png?w=180)](/images/2011/03/esdeveniments.png) 

...pude descubrir otra forma de debugging: provocando los errores a posta ;-)