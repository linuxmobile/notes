---
layout: blog
title: "Creando un todolist con javascript, en nucba"
description: "Creando un ToDo List con Javascript. Tarea de NUCBA."
tags: [Frontend]
toc: true
keywords: "first project todolist todo list to do nucba nucbantastico javascript js jquery const var let "
date: 2022-10-19
---

<i>Esta semana comenzamos a aplicar lo básico de __Javascript__ en el bootcamp de NUCBA. Estamos haciendo un ToDoList con HTML + CSS + JS.</i>

![Ejemplo](https://i.imgur.com/lba4Wfq.png){:.img-100}

<i>Nucba nos brindó un ejemplo, y me gustaría escribir el proceso que seguimos. ¡De paso, me queda a mi como recordatorio y me sirve para aprender y procesar lo aprendido!</i>

### Este es el HTML:

![html](https://i.imgur.com/Bbtnget.png){:.img-100}

## Analizando el HTML:

<i>Para empezar, lo mejor es analizar el HTML que nos ofrecieron, e ir "despedazándolo" por partes.</i>

- Tenemos un `<form></form>`.
- Un `<input>`.
- El `<button>Agregar</button>`.
- Un `<ul></ul>`. Para la lista de tareas.
- Y un `<button>Borrar Tareas</button>`.

## Comenzando con el main.js

<div class="p-list">

🔅 Ya que tenemos divido por partes el HTML, creamos las variables. (En este caso, vamos a usar __const__, ya que el contenido de estas variables no va a cambiar.)

~~~ js
// Seleccionamos los elementos del DOM
// y lo añadimos a una variable.
const input = document.querySelector(".input-text");
const addBtn = document.querySelector(".add-btn");
const addForm = document.querySelector(".add-form");
const tasksList = document.querySelector(".tasks-list"); // <- <ul></ul>
const deleteBtn = document.querySelector(".deleteAll-btn");
~~~

🔅 Una vez creadas las variables, utilizamos __JSON.parse{% ref "https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse" %}__ para tomar los elementos de la lista.

~~~ js
// Del LocalStorage obtenemos las listas,
// y si no hay nada creamos un array vacío.
let tasks = JSON.parse(localStorage.getItem("tasks")) || [];

// Creamos una funcionan para "crear" los elementos en el LS.
// Utilizamos stringify para convertirlo a 'strings'.
const saveLocalStorage = tasksList => { 
    localStorage.setItem('tasks', JSON.stringify(tasksList));
~~~


🔅 Creamos la función __'createTask'__ donde vamos a poder agregar las tareas con innerHTML. 

~~~ js
// Creamos una función que recibe la tarea y la renderiza
// en HTML de manera individual.
const createTask = (task) => {
    `<li>${task.name}<img class="delete-btn" src="./img/delete.svg" data-id=${task.taskId}></li>`
}
~~~

🔅 Creamos una función, utilizando __innerHTML{% ref "https://developer.mozilla.org/es/docs/Web/API/Element/innerHTML" %}__, __map{% ref "https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Array/map" %}__, y __join{% ref "https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Array/join" %}__ para renderizar la lista como HTML en el DOM.

~~~ js
// La función renderTaskList crea los elementos en el html,
// .map recorre y devuelve un nuevo <li></li> en cada caso.
// .join, utilizamos join para evitar la "coma" entre cada elemento del array.
const renderTaskList = (todoList) => {
    tasksList.innerHTML = todoList.map((task) => createTask(task)).join('');
}
~~~

🔅 Creamos una función para que el botón para borrar las listas aparezca o desaparezca dependiendo de si hay contenido o no, utilizando __lenght{% ref "https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Array/length" %}__.

~~~ js
// .length recorre taskList y si no hay nada
// oculta el botón, utilizando la clase hidden.
const hideDeleteAll = tasksList => {
  if (!tasksList.length) {
    deleteBtn.classList.add('hidden');
    return; // Para que no se siga ejecutando
  }
  deleteBtn.classList.remove('hidden');
};
~~~

🔅 La función addTask:

<i>La función __addTask__ tiene varios pasos.</i>

1. Con preventDefault, evitamos el comportamiento por default del submit.
2. con __trim__, guardamos la constante en __taskName__ quitando los espacios al principio y al final.
3. Con __length__, comprobamos si ingresamos una tarea vacía o si hay una tarea en el array con ese mismo nombre.
4. Con el __spread operator{% ref "https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Operators/Spread_syntax" %}__, asignamos a las tareas el mismo array de tareas pero sumando una tarea más. taskId + 1. 
5. Con __input.value__, reseteamos el valor del input.
6. Renderizamos las tareas.
7. Las guardamos en el LocalStorage.
8. Y verificamos si el botón de "borrar tareas" tiene que ocultarse o no.

~~~ js
const addTask = e => {
  e.preventDefault();
  const taskName = input.value.trim();
  if (!taskName.length) {
    alert('Por favor, ingrese una tarea');
    return; /*Para que no se siga ejecutando*/
  } else if (
    tasks.some(task => task.name.toLowerCase() === taskName.toLowerCase())
  ) {
    alert('Ya existe una tarea con ese nombre');
    return; /*Para que no se siga ejecutando*/
  }

  /* El id será la longitud del array de tareas actual mas uno.*/
  tasks = [...tasks, { name: taskName, taskId: tasks.length + 1 }];
  input.value = '';
  renderTasksList(tasks);
  saveLocalStorage(tasks);
  hideDeleteAll(tasks);
};
~~~

🔅 Creamos una función que borra las tareas utilizando __filter{% ref "https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Array/filter" %}__.

~~~ js
// si la lista (<li>) no contiene la clase delete-btn
// no hace nada (return),
// si no: filtra el ID por data-id= (del html)
// pasamos a número el id del elemento (<li>).
// el array "tasks" usamos filter para borrar 
// el que tenga un id distinto.
const removeTask = e => {
  if (!e.target.classList.contains('delete-btn')) return;
  const filterId = Number(e.target.dataset.id);
  tasks = tasks.filter(task => task.taskId !== filterId);
  renderTasksList(tasks);
  saveLocalStorage(tasks);
  hideDeleteAll(tasks);
};
~~~

🔅 Configuramos el botón de "borrar tareas".

~~~ js
// La función removeAll. Vacía el array de tareas.
const removeAll = () => {
  tasks = [];
  renderTasksList(tasks);
  saveLocalStorage(tasks);
  hideDeleteAll(tasks);
};
~~~

🔅 Con la función __init__ organizamos todas las tareas en un solo lugar.

~~~ js
const init = () => {
  renderTasksList(tasks);
  addForm.addEventListener('submit', addTask);
  tasksList.addEventListener('click', removeTask);
  deleteBtn.addEventListener('click', removeAll);
  hideDeleteAll(tasks);
};

// Ejecutamos init.
init();
~~~

</div>

## Conclusión y tips

::: info
**Info**: Para comprender un poco más, esto no es exactamente un tutorial. Sino más bien un TIL (today i learn).
:::

<i>Para recordar algunas cosas de este __todo__. "__tasks__" es un array con las tareas que agregamos.</i>