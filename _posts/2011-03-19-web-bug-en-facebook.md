---
layout: post
title: Web-bug en Facebook
post_id: 21
categories: 
- bug
- facebook
---

El título podría ser este: "Como saber si tu ex te mira el perfil de Facebook", pero seamos rigurosos ;-)

### Resumen

Voy a explicar un fallo de seguridad en Facebook en relación a sus servidores cache, los cuales forman una capa entre Internet y los contenidos multimedia internos (fotos y videos subidos). Este fallo, posibilita acceder a las peticiones en crudo del navegador de nuestros amigos, permitiendo obtener información privada de esas personas (
[web-bug](http://es.wikipedia.org/wiki/Web_bug)), o utilizarlas como puente para aprovechar otras vulnerabilidades externas (
[CSRF](http://es.wikipedia.org/wiki/Cross_Site_Request_Forgery)).

### Facebook y su capa intermedia

Muchas veces habréis visto eso de "usa esta aplicación y descubre quien visita tu perfil", ¿cierto?, bien, esto será siempre un 
fake, dado que Facebook esta diseñado de una manera que lo hace imposible. Si os fijáis, cuando subís una foto, como la de perfil, esta se redimensiona, se comprime, y se guarda en un servidor propio de Facebook. Realmente, son centenares de servidores, que forman lo que se llama una 
[CDN](http://es.wikipedia.org/wiki/Red_de_entrega_de_contenidos). Un ejemplo de foto de perfil:

>http://profile.ak.fb**cdn**.net/hprofile-ak-snc4/41513_1381714233_4208850_q.jpg

Si vais a vuestra cuenta de Facebook, miráis el código fuente (tecla Ctrl+U en Firefox), y buscáis la secuencia `fbcdn´, estará lleno. Y la gracia es esta. Que si utilizáis un programa como `
[Tamper Data](https://addons.mozilla.org/en-us/firefox/addon/tamper-data/)´, veréis que todas las peticiones van allá. Ninguna sale al exterior.

La pregunta es: ¿Porqué? Gracias a esto, Facebook se ahorra muchos problemas de seguridad (particularme privacidad), ganando un control total sobre que va y a donde va (minería de datos). Esto no supone un coste cero a Facebook, sino que realmente es muy costoso económicamente.

Fijaros que no es el único en hacerlo. Tuenti, por ejemplo, también tiene su propia CDN.

## Proceso

El objetivo es conseguir un enlace directo al exterior, sin pasar por ese filtro interno.

Primero, creamos una nueva nota. El contenido, debe tener al menos el siguiente código:

<img src="http://[dirección]" />
Una vez se crea la nota, se puede escoger quien es el objetivo de esta. Es muy importante esto, debido a que será clave para poder dirigir el ataque a una persona o grupo concreto:


[![](http://sergioarcos.files.wordpress.com/2011/03/01.png?w=300)](http://sergioarcos.files.wordpress.com/2011/03/01.png)


[](http://sergioarcos.files.wordpress.com/2011/03/01.png)Una vez creada, si miramos el código fuente, podemos observar que se ha creado con dirección de la CDN:


[![](http://sergioarcos.files.wordpress.com/2011/03/02.png?w=300)](http://sergioarcos.files.wordpress.com/2011/03/02.png)


[](http://sergioarcos.files.wordpress.com/2011/03/02.png)Ahora, podemos ir a nuestro muro, donde se ha auto-creado un nuevo post, y borrarlo. No sirve de nada. Lo que hay que hacer, es compartir esa nota manualmente. También hay que seleccionar con quien queremos compartirla, igual que hicimos al crearla. Fijaros bien en el código que se replica:


[![](http://sergioarcos.files.wordpress.com/2011/03/03.png?w=300)](http://sergioarcos.files.wordpress.com/2011/03/03.png)

Resulta que el código enviado, es en crudo, no como hace cuando replicas un imagen o un vídeo subido por la vía típica, que ya va embebido. Facebook se fía de que las imágenes que entramos por la sección notas, son "subidas", no externas, por eso no pone ninguna seguridad adicional.

El resultado, es el esperado:


[![](http://sergioarcos.files.wordpress.com/2011/03/04.png?w=300)](http://sergioarcos.files.wordpress.com/2011/03/04.png)

## Utilidad

Yo veo 2 vectores de ataque, pero la imaginación al poder :-)

1) Sacar datos con un web-bug. Consiste en colocar en un servidor propio un fichero dinámico que recoja información (hora, ip, user_agent, referer, ...) pero haciéndose pasar por una imagen. Aquí entraría el poder configurar (se puede) Facebook de manera que sólo saliera en tu muro la imagen, lo cual, hace posible contar cuantos visitantes tienes (woo!).

He hecho una prueba de concepto, a nivel de amigos, y han habido muchísimos casos de navegar en Facebook a través del móvil, lo cual es curioso:


[![](http://sergioarcos.files.wordpress.com/2011/03/05.png?w=300)](http://sergioarcos.files.wordpress.com/2011/03/05.png)

2) Utilizar la vulnerabilidad para atacar otras páginas externas que sean vulnerables a CSRF. Un ejemplo seria poder operar en un banco solo haciendo una llamada directa a una dirección, como por ejemplo, http://url.ext/mover_dinero.php?de=XXX&a=XXX&cantidad=999999. Ideal para atacar sistemas de encuestas, configurando la visibilidad a nivel de `cualquiera´, e incitando a que la gente lo comparta. Desconozco que sea posible hacer alguna llamada a Facebook de este tipo aprovechando el Referer. No tengo intención ni de probarlo.

## Notas finales

Lo primero, tranquilizar a todo el mundo: "No es nada grave", ya esta reportado, y todos los datos que he recogido en la prueba de concepto son totalmente inofensivos. Facebook a estas alturas ya esta protegido.


Supongo que lo que más me ha chocado, es encontrar en un sitio tan popular y tan auditado, un error "tan simple". Supongo que ha sido suerte, pero me ha hecho ilusión :)

## Reporte

El día 17 de marzo por la tarde, intuí la vulnerabilidad. El día 18, verifique y encontré como explotarla. Hice la prueba de concepto durante 3-4 horas, y reporte la vulnerabilidad a Facebook por la tarde. El dia 19, sin todavía responderme de Facebook a mi correo, veo que esta solucionado. Publico el post entonces, aunque dudo que "lo hayan solucionado por mi report". Me da igual también.