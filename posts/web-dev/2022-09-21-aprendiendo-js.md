---
layout: post
title: "Aprendiendo Javascript desde Cero | NUCBA"
tags: [Web Dev, Javascript, Frontend]
toc: false
good: false
notfull: 1
icon: js.png
date: 2022-09-21
keywords: "javascript learning aprendiendo desde cero js jscript typerscript react frontend learn web developer desarrollador desarrollo programador programar aprender reactjs tsx noob nuevo"
---

Tips / Consejos / Javascript

👉 Nota: [Documentación Javascript](https://es.javascript.info/)

## Introducción a JS:

<div class="p-list">

🔅 ¿Qué es Javascripts?

<i>Como una breve introducción, ya que esto no es un tutorial ni una documentación. __Javascript__ es lo que le da __"vida"__ a las páginas.</i>

🔅 ¿Cómo incluirlo en una web?

Incluído dentro de un elemento html:

~~~ html
<!-- En este caso al hacer click sobre el texto "Hola Mundo,
   Vamos a ver una alerta que dice "hola".               -->
<h1 onClick="alert('hola')">Hola Mundo...</h1>
~~~

Incluído dentro de la etiqueta scripts

~~~ html
<!-- En esta situación nos saldría una alerta en el navegador que diría "hola". -->
<body>
    <script>
    alert('hola');
    </script>
</body>
~~~

Incluído en un archivo externo <i>(La mejor forma de incluir javascript).</i>

~~~ html
<!-- De esta forma estamos linkeando al archivo index.js
     Dentro de la carpeta del proyecto               -->
<script src="./index.js"></script>
~~~

~~~ js
/* index.js */
alert('hola');
~~~

</div>

## Fundamentos de Javascript:

<div class="p-list">

🔅 Variables:

<i>Una variable es un "almacen" con un nombre, donde se guardan ciertos datos. En Javascript utilizamos __let__ y __const__.</i>

~~~ js/3
let message; // Let define la variable "message"
message = 'Hola mundo'; // la variable "message" muestra 'Hola mundo'

console.log(message); // Con console.log podemos ver en la consola 'Hola mundo'
~~~

__Otra forma más óptima de escribir el mismo código es:__
~~~ js/2
let message = 'Hola mundo';

console.log(message) // Muestra un "Hola mundo"
~~~

- Declarar Varias Variables: 

<i>Podemos declarar variables separándolas con una ',':</i>
~~~ js
let user = 'linuxin', age = 28, message = 'Hola mundo';
~~~

🔅 Constantes:

<i>Las constantes son variables inmutables.</i>

~~~ js/0/2
const myBirthday = '1994-09-08'

myBirthday = '1996-10-22'; // ¡error, no se puede reasignar la constante!
~~~

</div>

## Interacciones: 

<div class="p-list">

🔅 alert:

~~~ js 
alert('hola mundo') // muestra una alerta "hola mundo" en el navegador
~~~

🔅 prompt:

~~~ js
let age = prompt('¿Cuántos años tengo?') // Pregunta en el navegador cuántos años tengo.

console.log(`¡Tengo ${age} años!`) // muestra la cantidad de años.
~~~

🔅 confirm:

~~~ js 
let isNucbaBoss = confirm("Es NUCBA el jefe?");

console.log( isNucbaBoss ); // true si se pulsa OK
~~~

</div>

## Condicionales:

<div class="p-list">

🔅 if:

<i>En el siguiente ejemplo, declaramos dos números. 420 cómo 'numberReal' y 520 como 'numberFalse'.</i>

~~~ js/5,9
let numberReal = 420
let numberFalse = 520

// Si numberFalse es mayor o igual a 420, devuelve 420 klk.
if(numberReal >= numberFalse) {
    console.log('420 klk')
}
// Si es mayor devuelve 'no es elegante este número'.
else{
    console.log('No es elegante este número')
}
~~~

🔅 Operador ternario '?'

~~~ js/3-7
let accessAllowed;
let age = prompt('¿Qué edad tenés?', '');

if (age > 18) {
  accessAllowed = true;
} else {
  accessAllowed = false;
}

alert(accessAllowed);
~~~

<i>El __operador ternario__ permite ejecutar esto mismo de manera más sencilla.</i>

~~~ js 
let accessAllowed = (age > 18) ? true : false;
~~~

</div>