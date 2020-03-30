---
layout: post
title: Session Fixation with PHP
post_id: 112
categories: 
- bug
- eyeos
- php
- security
- session fixation
---

Hace unos días asistí a un evento de 
[OWASP](https://www.owasp.org/index.php/Spain),  donde se daban cuatro charlas de seguridad web. Una de ellas, realizada por Raúl Siles de 
[Taddong](http://www.taddong.com/), trataba el concepto de `Session Fixation´. Como hacía unos días que un 
[amigo](http://www.rooibo.com/) me había animado a revisar el código de 
[EyeOS](http://www.eyeos.org/), intenté jugar con lo aprendido en la charla.

La vulnerabilidad localizada no es crítica ya que requiere de otra vulnerabilidad para fijar la 
cookie, y 
[ya ha sido corregida en la última versión](http://sourceforge.net/projects/eyeos/files/eyeos2/eyeos-2.4.1.tar.gz/download). La seguridad en un sistema no es más que el número de capas eficientes en cada proceso interno del software, y en este caso, había más protecciones en el proceso afectado. Lamentablemente, no sólo 
[EyeOS](http://www.eyeos.org/) fue/es vulnerable. Raúl Siles encontró vulnerabilidades en SAP, Joomla y otros sistemas, alertando que todavía más páginas están afectadas aún siendo una vulnerabilidad del año 2002.

El objetivo de esta entrada es analizar Session Fixation y cómo hay que tratarlo en PHP correctamente.

### How to find a session fixation

Para localizar esta vulnerabilidad basta con observar que la 
cookie de 
session (por defecto PHPSESSID) no cambia aunque se loguee, se desloguee, se vuelva a loguear, etc. Lo que cambia son los valores internos, pero no el identificador.

El código fuente normalmente usa la función 
**session_destroy();**
 pensando que es suficiente para regenerar la cookie, pero esa no es su función.

### How to exploit a session fixation

Normalmente será necesario tener un 
`Cross-Site Scripting´ (XSS) o otra vulnerabilidad (\r\n) para inyectar el código malicioso. El proceso es asignar una 
session que se conozca y cambiar la fecha/dominio de la cookie para que nunca expire, es decir, que sea permanente (a diferencia de las 
cookies que expiran al cerrar el navegador). Esto se puede conseguir de diferentes maneras. Una de muy elegante es introducir por XSS el siguiente código:

```html
><meta http-equiv="Set-Cookie" content="value=n;expires=date; path=url">
```

Hay que jugar con la fecha de expiración y la ruta entre subdominios para obtener el impacto deseado, pero en cada web será diferente.

### How to sanitize a session fixation

La función 
**session_destroy();**
 hace lo mismo que poner a 0 el contenido del fichero que genera en la ruta de 
sessiones ( "/tmp" por defecto en entornos GNU/Linux). Por lo tanto, la función que hay que utilizar es 
**session_regenerate_id(true);**
 que no borra la 
session, pero que cambia el identificador, borrando el fichero de la antigua 
session, ayudando a controlar los ficheros residuales.

PHP facilita el trabajo de las 
sessiones, comprobando que el nombre de estas no contiene caracteres especiales (
valid characters are a-z, A-Z, 0-9 and '-,') y su longitud (cread una carpeta con nombre muy largo e insertaros una 
cookie de 128 caracteres (el máximo). Saldrá un error y no dejará continuar), pero hay que saberlas configurar.

### How to steal sessions

Uno de los errores que ha crecido más en la lista OWASP es el `A6: Security Misconfiguration´. Los administradores de sistema están pensando que los 
software precocionados están preparados para instalarse y olvidarse. Pues no, empezando por PHP, MySQL, Apache, y cualquier otro que sirva de base para aplicaciones como Joomla, Drupal, etc.

Como se comentó, las 
sessiones suelen almacenarse en "/tmp" por defecto. Si se esta en un servidor compartido (vhost) lo más normal es que todos tengan acceso a esa partición, pudiendo leer las 
sessiones de los usuarios ya logueados. Creo que no hace falta comentarlo, pero robar la session es tan preocupante como que se roben los usuarios y las contraseñas. Al final la 
session es la manera de saber que tiempo atrás hubo una autentificación satisfactoria.

### How to maximize the security

Hay dos consejos que considero adecuados aplicarlos:

1) Usar 
**session_regenerate_id(true);**
en 
cada petición del usuario al servidor. Esto evitaría ataques `
Cross-site request forgery´ (CSRF) tal como comentaba Raúl Siles. 
[EyeOS](http://www.eyeos.org/) no era vulnerable porque utilizaban un 
token interno por ellos que iba cambiando dependiendo del proceso y más variables, pero la 
session seguía en un nivel inferior.

2) Utilizar la función 
**session_save_path('/path');**
 y 
**session_cache_expire();**
para asegurarse del directorio donde las 
cookies se guardan (OJO: es importante asignar los permisos correctos a esta ruta o sino el problema será el mismo) y reasignar el tiempo de expiración de la cache (defecto es 180) dependiendo de la aplicación.

### Conclusions

He tratado el tema bastante por encima. En 
[Taddong](http://www.taddong.com/) hay información muy buena del proceso completo para su detección y explotación. Aunque me haya centrado en PHP, la lógica se aplica a cualquier otro sistema: Si no se regenera la id, el sistema puede verse vulnerado. ¡
**session_Id**
 con cuidado!