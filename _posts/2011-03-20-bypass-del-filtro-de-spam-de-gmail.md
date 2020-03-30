---
layout: post
title: Bypass del filtro de spam de Gmail
post_id: 42
categories: 
- bug
- bypass
- filter
- gmail
---

**Note: I do not care if you want to share or translate the document, but please, quote me ;). Thanks!**


### Resumen

A través de la confianza que gestiona Gmail, es posible saltarse el filtro de spam y conseguir spoofear correos. Esto devuelve a la década pasada, cuando el spam y el phishing eran más libres, pero a día de hoy, incluso permitiría hacer un 
[Spear Phishing](http://en.wikipedia.org/wiki/Spear_phishing) muy hardcore. Esta prueba de concepto esta ya arreglada, pero creo interesante compartirla.

En mi Proyecto Final de Carrera, uno de los temas de los que he hablado es la confianza, y tirando un poco del hilo, observe que Gmail tenia una vulnerabilidad a la hora de como funcionaba internamente. Es lo que llamo `
a featuremisused´.

### Gmail y la confianza

No se si os habéis fijado, pero cuando recibís un correo de un nuevo amigo, y le contestáis, automáticamente os lo agrega al Gtalk. Esto es porque internamente Gmail lo ha añadido al grupo de amigos, o dicho de otra manera, a la lista de la que tiene "confianza". Además de esta, Gmail utiliza otras maneras para vincular vuestra cuenta con otras, como, por ejemplo, cuando alguien os ha enviado la invitación (me remonto a cuando empezó Gmail, sí).

El objetivo de ir asociando cuentas es que la red se expanda, pero también hacer la vida más fácil al usuario (usabilidad). A partir de haber recibido y enviado ese primer correo, podremos utilizar el auto-completar.

### Sacando la amistad entre gente

Es un punto muy interesante, pero me limitaré a dar algunas pautas: (seguro que hay más)

- **Listas de correo:**
 Dependerá de su configuración, pero en general estas envían los correos originales, y muchas veces al hacer reply-all son arrastrados consigo, haciendo que se intercambien correos.

- **Chain List:**
 Las famosas cadenas basura, más famosas por quebrantar la 
[LOPD](http://es.wikipedia.org/wiki/Ley_Org%C3%A1nica_de_Protecci%C3%B3n_de_Datos_de_Car%C3%A1cter_Personal_de_Espa%C3%B1a) que por su interesante contenido, son perfectas para aprovecharse de esta vulnerabilidad.

### Proceso

El objetivo es enviar un correo y que llegue al `inbox´ en vez de la carpeta `spam´.

Desde cualquier distribución GNU/Linux podemos crear un correo personalizado y enviarlo como prueba de concepto. Uso la cuenta de mi hermano para pasar el filtro.


[![](/images/2011/03/virt.png?w=300)](/images/2011/03/virt.png)Y en nuestra `bandeja de entrada´ recibiríamos lo siguiente. A la izquierda, sin desplegar. A la derecha, desplegado. (¿Quién desplega?)


[![](/images/2011/03/gmail_poc.png?w=300)](/images/2011/03/gmail_poc.png)Podemos hacernos pasar por quien queramos, no por correo, sino por su nombre, y como es lo único que nos enseña Gmail, ¡olé!. En su día envíe muchos correos para probarlo, y aseguro que funcionaba.

Además, si tenéis filtros personalizados y el correo que enviáis cumple la condición, se introduce en la etiqueta concreta (a excepción de que la condición sea la dirección de From (lógico)).

### Reporte

Cuando descubrí la vulnerabilidad era por otoño/invierno del año pasado 2010, pero dado que era un ejemplo del PFC, no lo reporte todavía. Si os fijáis, la prueba la realice en febrero, que es cuando recogí las imágenes. A día de hoy, 20 de marzo, ya no funciona. Si utilizáis el siguiente código para enviar el correo:

>/usr/sbin/sendmail -t **-vv** < fakemail.txt

veréis que el servidor de correo de Gmail os salta con la siguiente dirección URL -> 
[http://mail.google.com/support/bin/answer.py?answer=10336](http://mail.google.com/support/bin/answer.py?answer=10336) . Resulta que ayer me devolvía el error en el verbose, pero hoy, no lo esta haciendo (tampoco estoy recibiendo ningún correo, simplemente no lo avisa).

Eso significa que no se permiten enviar correos a Gmail si tu dirección IP no concuerda con el dominio del emisor. Pero no significa que el bug de confianza no este, sólo que a ver quien lo explota así xD.

### Aunque...

A veces se le cuela. Ayer probé uno de los correos que tenia preparados al azar y coló, aún saliendo el error en el verbose.

[![](/images/2011/03/testing_yesterday.png?w=300)](/images/2011/03/testing_yesterday.png) 

Supongo que es por desincronización entre servidores.

Si hubiera sido más espabilado lo hubiera enviado en su dia al 
[Reward Program](http://www.google.com/corporate/rewardprogram.html), jeje.