
<img src="/home/deiivid/Imágenes/logoOadis.png" align="center" alt="logoOadis" style="zoom:100%;" />

# Xss Dom

## Que es Xss Dom

<div style="text-align: justify">
Bueno pues lo prometido es deuda. En el artículo anterior sobre Xss stored hablamos de realizar una entrada sobre Xss Dom y pues como somos personas serias  he aquí lo que he investigado y escrito sobre esta vulnerabilidad.</div>

<br>

>Xss Dom es una vulnerabilidad presente comúnmente en las aplicaciones web, esto debido a que dicha vulnerabilidad básicamente depende de la modificación del Modelo de Objetos del Documento(DOM). En este tipo de ataque la carga maliciosa se ejecuta derivado de la modificación del DOM a través de la generación de código HTML con JAVASCRIPT ,el cual se refleja dentro del navegador de la víctima, cabe recalcar que las páginas se generan de manera dinámica dependiendo de los datos proporcionados por el usuario, estas páginas son generadas del lado del cliente, los datos no van al servidor de aplicación. Esto comúnmente se hace cuando se requiere actualizar contenido sin tener que actualizar el total de la página.

<br>

<div style="text-align: justify">
Es cierto que muchas aplicaciones realizan una validación exhaustiva del lado de servidor, sin embargo muchas veces esa validación no se realiza de la misma manera del lado del cliente lo cual deriva de un fallo de seguridad grave.</div>

<br>

<div style="text-align: justify">
Veamos un ejemplo para que nos quede clara esta vulnerabilidad. Supongamos que nuestro habilidoso hacker "sí Ramiro" es un arduo comprador, el descubre un portal que publica descuentos de distintas tiendas online "PromoPlanet", así mismo este portal ofrece una membresía para miembros premium mediante la cual los  usuarios con dicha membresía pueden acceder a las ofertas más significativas antes de que la comunidad en general puedan visualizarlas. Ramiro empieza a visualizar el sitio y a navegar en el, ya convencido de darle like a una oferta bastante atractiva se percata que el portal lo nombra como usuario invitado ya que no está registrado, así mismo visualiza un botón que indica ir al sitio para registrarse, al dar click el sitio lo redirige hacia la página la cual contiene el formulario de registro y el Login, pero también visualiza que el parámetro del nombre de usuario en este caso el de invitado es pasado como un valor dentro de la url para redirigir y mostrar dicho nombre en el encabezado de la página de registro. Ya se imaginaron verdad, a Ramiro le brillaron los ojitos, casi como a un gato en la obscuridad. Al retornar a la página inicial sustituye el valor del usuario invitado por el código para ejecutar un script y al dar enter confirma lo que intuyó, PromoPlanet es vulnerable a Xss Dom. ¿Cómo sucedió esto? Simple, el portal al redirigir al login/registro muestra la pantalla y mediante JAVASCRIPT modifica dicha pantalla insertando el valor del usuario invitado, esto directamente del lado del cliente sin pasar por alguna validación del lado del servidor de aplicación, finalmente utiliza este valor para mostrarlo.
Ramiro contento con su hallazgo a través de sus técnicas obtiene el correo electrónico de otros usuarios de PromoPlanet incluyendo a Juanito, así mismo hace una web prácticamente igual y crea una campaña en donde promete una suscripción anual con un descuento del 75% del costo total. Crea un script el cual aprovecha la vulnerabilidad descubierta, el cual lleva al sitio oficial de PromoPlanet y posteriormente redirige hacia el sitio controlado por Ramiro el cual contiene la pantalla de login y posteriormente al loguearse muestra un formulario para ingresar los datos de tarjeta y obtener la membresía con el descuento prometido, así es como Juanito y los demás usuarios ilusos han perdido una cantidad considerable de dinero transfiriendo este a la cuenta de Ramiro de forma inconsciente.</div>
<br>

<font size="2">La imagen siguiente muesta un ejemplo de explotación de esta vulnerabilidad</font> 

![Xss Dom](../../Recursos/Imagenes/xssDom.png)

## Conceptualización de la vulnerabilidad en código.

*Se muestra un ejemplo del código js que muestra el usuario invitado en la página de login/registro de PromoPlanet el cual es vulnerable a Xss DOM.*

```js
// Funcion que toma la cadena que se encuentra despues del símbolo numeral para guardarla en la variable usuario
1<h2>
2    <script>
3        var usuario = document.location.hash.split('#')[1]; https://promoplanet.com.mx/guests/landing#invitado
4        document.write("Hola " + usuario + "¡Para acceder a todas las promociones registrate o ingresa primero!");    
5    </script>
6</h2>
```

<br>

<div style="text-align: justify">
1.- Para mostrar el usuario invitado dentro de la página que contiene el login y el formulario de registro de PromoPlanet se ha utilizado la función split la cual obtiene la cadena que está justamente después del numeral y almacena este valor en la variable "usuario". Linea 3.
<br>
<br>
2.- Posteriormente la variable usuario es concatenada al texto del mensaje que indica que te registres o accedas para visualizar todo el contenido. Linea 4.
<br>
<br>
3.- Como se percibe en el ejemplo anterior la variable usuario es es pasada directamente al modelo de objetos del documento a través del método document.write(). En la línea 4 se percibe que el valor de la variable usuario es mostrado prácticamente sin realizar algún tipo de validación previo, lo cual permite que esta función sea aprovechada para ejecutar acciones maliciosas.
</div>

<br>


## ¿Cómo se percibe el script que explota esta vulnerabilidad?

<div style="text-align: justify">
Ramiro crea el script que ejecuta un redireccionamiento a su portal aprovechando la función de JAVASCRIPT "window.location" el cual se muestra de la siguiente manera:</div>

```js
https://promoplanet.com.mx/guests/landing#<script>window.location='https://ramiropromoplanet.com.mx/login'</script>
```


## Impacto de esta vulnerabilidad.

*La tabla siguiente muestra el impacto que puede ocasionar esta vulnerabilidad tomando como referencia la triada de seguridad.*

<table style="text-align:center; float:center;">
<thead>
<tr>
  <th bgcolor="007892" style="color:#f7f7f7;">Impacto-Vulnerabilidad</th>
  <th bgcolor="007892" style="color:#f7f7f7;">Confidencialidad</th>
  <th bgcolor="007892" style="color:#f7f7f7;">Integridad</th>
  <th bgcolor="007892" style="color:#f7f7f7;">Disponibilidad</th>
</tr>
</thead>
<tbody>
<tr>
  <td bgcolor="7d5a5a" style="color:#f7f7f7;" align="left">Lectura de datos</td>
  <td bgcolor="d8345f" style="color:#f7f7f7;">Aplica</td>
  <td></td>
  <td></td>
</tr>
<tr>
  <td bgcolor="7d5a5a" style="color:#f7f7f7;" align="left">Obtención de privilegios</td>
  <td bgcolor="d8345f" style="color:#f7f7f7;">Aplica</td>
  <td></td>
  <td></td>
</tr>
<tr>
  <td bgcolor="7d5a5a" style="color:#f7f7f7;" align="left">Ejecución no autorizada de comandos</td>
  <td bgcolor="d8345f" style="color:#f7f7f7;">Aplica</td>
  <td bgcolor="d8345f" style="color:#f7f7f7;">Aplica</td>
  <td bgcolor="d8345f" style="color:#f7f7f7;">Aplica</td>
</tr>
<tr>
  <td bgcolor="7d5a5a" style="color:#f7f7f7;" align="left">Modificación de datos</td>
  <td></td>
  <td bgcolor="d8345f" style="color:#f7f7f7;">Aplica</td>
  <td></td>
</tr>
</tbody>
</table>

## Conceptualización de la remediación de la vulnerabilidad en código.

```js
// Funcion que toma la cadena que se encuentra despues del símbolo numeral para guardarla en la variable usuario, posteriormente mediante una expresion regular valida que la variable usuario solo contenga letras y números
1<h2>
2    <script>
3        var usuario = document.location.hash.split('#')[1]; https://promoplanet.com.mx/guests/landing#invitado
4        if (usuario.match(/^[a-zA-Z0-9]*$/) ){
5        
6          document.write("Hola " + usuario + "¡Para acceder a todas las promociones registrate o ingresa primero!");    
7
8         } else{
9
10           window.alert("Error al ingresar")
11         }
12    </script>
13</h2>
```

<br>

<div style="text-align: justify">
1.- Para llevar a cabo la remediación de esta vulnerabilidad simplemente se valida mediante un expresión regular que la variable usuario no contenga caracteres especiales, en caso de que esta validación sea exitosa se procede a escribir el valor en el DOM, de lo contrario se estará enviando una alerta de error. 
Cabe aclarar que en este caso aplica para "PromoPlanet" en donde se sabe que el nombre de un usuario solo contiene letras y números, sin embargo esta solución va a diferir dependiendo del diseño de cada sitio. Yo recomiendo encarecidamente determinar y tener el conocimiento  de aquellos datos o caracteres que serán permitidos ya que muchas veces se opta por obtener ejemplos de soluciones que incluso pueden llegar a afectar el funcionamiento del aplicativo.
</div>


## ¿Que elementos puedo utilizar para reforzar la seguridad ante esta vulnerabilidad o en el momento en que este desarrollando una nueva aplicación?

<div style="text-align: justify">
1.- Validar que al uso de las funciones siguientes contengan validaciones sobre los datos permitidos al invocar estas:

window.name document.referrer document.URL document.documentURI location location.href location.search location.hash eval setTimeout setInterval document.write document.writeIn innerHTML outerHTML
<br>
<br>
2.- Codificar Html y posteriormente Javascript cuando se requiera escribir directamente en el DOM del lado del cliente.
<br>
<br>
3.- Uso del atributo HTTPOnly en las cookies.
Este indicador evita que código JS acceda al contenido de la cookie, lo cual evita que las cookies sean obtenidas a través de la explotación de esta vulnerabilidad.
<br>
<br>
4.- Uso del encabezado de respuesta http Content-Security-Policy. 
Este funciona baja el principio de white list, este encabezado restringe las fuentes desde las cuales se pueden cargar scripts y datos en todo sitio web, si un código malicioso se introduce en el código HTML sin ser reconocido dentro de la whitelist y este intenta cargar datos externos, el navegador del usuario lo rechazará.
<br>
<br>
5.- Uso de Frameworks modernos.
Muchos de los frameworks de desarrollo modernos suelen integrar soluciones para evitar este tipo de vulnerabilidades, esto suele ser de gran ayuda. Sin embargo, a mi parecer y experiencia como análista de seguridad algunos de estos tienen integradas vulnerabilidades dentro del código. Por tal sugiero se realice un análisis previo para tomar la mejor desicion al optar por uno de estos. 

</div>

## La pregunta del millón

### Cómo diferencío cada tipo de Xss?

Con breves palabras diría lo siguiente:

1. Reflected se explota del lado del servidor y esta vulnerabilidad es aprovechada únicamente durante la sesión en la que se encuentre la víctima.

2. Stored se explota del lado del servidor y esta vulnerabilidad afecta a todos los usuarios que tengan acceso al segmento del portal web vulnerable.

3. Dom se explota del lado del cliente y esta vulnerabilidad es aprovechada únicamente durante la sesión en la que se encuentra la víctima.

   <br>

### Proximas actividades por incluír dentro de este artículo.

<div>
<img src="../../Recursos/Imagenes/bluewritte.png" align="right" alt="Blue-Daemon" style="zoom:40%;" />
<p>
- Desarrollo de aplicación dummie vulnerable a xss dom.
<br>
- Riesgos y vulnerabilidades que pueden derivar de la explotación de esta vulnerabilidad.
<br>
- Walkthrough para explotación de vulnerabilidad.
<br>
- Intrusion test para validar que la solución de remediación propuesta es adecuada.
</p>
<h4>Referencias</h4>
<p>
https://cheatsheetseries.owasp.org/cheatsheets/DOM_based_XSS_Prevention_Cheat_Sheet.html<br>
https://capec.mitre.org/data/definitions/588.html<br>
https://owasp.org/www-community/attacks/xss/<br>
</p>
</div>

