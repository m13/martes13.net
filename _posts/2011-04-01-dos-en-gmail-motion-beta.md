---
layout: post
title: DoS en Gmail Motion BETA
post_id: 85
categories: 
- april fools
- beta
- bug
- DoS
- fun
- gmail
- motion
---

**Note: I do not care if you want to share or translate the document, but please, quote me 
![;)](http://s1.wp.com/wp-includes/images/smilies/icon_wink.gif?m=1221158483g) . Thanks!**


### Resumen

Gmail ha sacado su nuevo servicio llamado 
[Gmail Motion](http://mail.google.com/mail/help/motion.html), todavía en estado Beta. Se basa en, a través de la cámara web, leer gestos y hacer acciones que representan. Este servicio puede verse alterado, e incluso provocar una denegación de servicio (DoS) tanto en el servidor como en el cliente si alguien aparece por detrás haciendo nuevos gestos.

### Angulo de visión de la webcam

El enfoque de la webcam son 50º en vertical y 34º en horizontal, permitiendo el cuerpo de más de una persona a la vez. Dado que el sistema todavía esta en Beta, se guia por los colores de la pies, las siluetas y la profundidad por los tonos negros, sin contemplar el número de extremidades que aparecen.

Esto implica una fácil suplantación del individuo añadiendo otra persona dentro del marco procesado.

### Denegación de servicio

Se ha comprobado que si el programa esta en marcha y se incorpora una nueva persona realizando otro gesto supuestamente reconocido, el programa bloquea ipso-facto el ordenador con el que se trabaja. Se rumorea que el servicio de Gmail Motion empieza a ir más y más lento de manera general, pero gracias a su sistema SMTML (si-me-tiras-me-levanto) de momento no ha causado grandes estragos. Se recuerda que el sistema sigue en Beta, aunque no será fácil reparar este error.

[![Gmail Motion DoS](/images/2011/04/01.png)](/images/2011/04/01.png)

Esto implica que hay que cerrar bien la puerta de la habitación antes de comenzar a utilizar el servicio. Desde Google desaconsejan activarlo si se tienen hermanos pequeños.


### Otros problemas


Se dice que si se pone una hoja en negro que ocupe todo el enfoque de la webcam el sistema te autobloquea la cuenta de Gmail automáticamente. Yo paso de probarlo, que no tengo tanta tinta para imprimirmela, pero os dejo una de muestra por si queréis testearlo.

[![april_fools](/images/2011/04/02.png?w=300)](/images/2011/04/02.png)

****