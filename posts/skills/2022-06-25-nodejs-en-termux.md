---
layout: post
title: "NodeJS en Termux (y sus problemas)"
tags: [Skills, Shell, Android]
toc: false
good: true
icon: linux.svg
date: 2022-06-25
keywords: "android termux linux arch artix configurando configurar setup programar programming hub fullstack developer learning how to ide IDE myTermux ohmyzsh zsh bat cat cava ncmpcpp mpd mpc mpv firefox develop developing programando tablet samsung xiaomi tab galaxy nodejs npm nvm"
---

## Intentando utilizar NPM en android y todos sus problemas!

<div class="p-list">

🔅 Como primer paso, es poder instalar npm:

~~~ bash
pkg i -y npm
~~~


🔅 Comenzando con npm:
__En este caso voy a estar utilizando como "proyecto", el del creador de esta web: [Dinhanthi](https://dinhanhthi.com/)__

![gracias, dinhanthi](https://i.imgur.com/VwQj4S9.png){:.img-100}

~~~ bash
git clone https://github.com/dinhanthi/dinhanthi.com
~~~

![npm](https://i.imgur.com/Z7UJI5O.png){:.img-100}

~~~ bash
# Instalamos con npm:
npm install
~~~

🔅 Los primeros problemas:

![sharp y android](https://i.imgur.com/1clXnIP.png){:.img-100}

<a>Sinceramente, me esperaba que esto fuera a pasar. No es fácil configurar Android para programar.</a>

👉 Nota: <strong>Configurar android para programar no quiere decir que sea dificil. Pero si hay que tener en cuenta que la mayoría de los programadores, no lo hacen desde un dispositivo android, y se pueden presentar algunos problemas. Veamos las soluciones.</strong>
</div>

## Sharp no soporta Android:

<div class="p-list">

![Sharp](https://i.imgur.com/E3DExxc.png){:.img-100}

__Sharp, no soporta la plataforma Android.__

Sin embargo, Sharp nos ofrece una solución. <a>Aun que a mi no me funcionó del todo.</a>

~~~ bash
# Setearlo en true, y luego volver a false.
npm install --ignore-scripts=false
~~~

__Con este método, ofrecido por [sharp](https://sharp.pixelplumbing.com/install), logré instalar el módulo de nodejs. Pero al tratar de compilar la web, no funciona porque no reconoce Android.__

<strong>Busqué mucho una solución para poder continuar con esto y no encontré nada útil. Así que acá dejo mi solución:</strong>

## Solución a problemas de compatibilidad con Android:

🔅 Instalar un entorno basado en Linux:

👉 Nota: En mi caso, prefiero elegir Archlinux. Así que vamos a utilizar la herramienta de [TermuxArch](https://github.com/TermuxArch/TermuxArch)

::: warning
Al menos, vas a necesitar 2GB. ¡Tenlo en cuenta!
:::

~~~ bash
git clone https://github.com/TermuxArch/TermuxArch
bash TermuxArch/setupTermuxArch.sh
~~~

👉 Nota: Si viste mi post sobre 'cómo configurar Termux', deberías saber que este paso es sumamente importante: `termux-setup-storage`.


:::info
Yo no hice este paso, pero en la Documentación lo recomiendan! Así que lo dejo por si más adelante es necesario
:::

🔅 Instalamos los submódulos del repo:

~~~ bash
bash scripts/maintenance/pullTermuxArchSubmodules.sh
~~~
</div>

## Archlinux bajo Termux:

🔅 Iniciamos arch:

![Archlinux / Termux](https://i.imgur.com/kJsmTvf.png){:.img-100}

`startarch` para iniciar el sistema como root. 
`startarch c 'comando'` para correr un comando como root. __Ejemplo: `startarch c 'addauser linuxmobile'`__
`startarch login linuxmobile` para loguearte con tu usario. __Claro, remplaza 'linuxmobile' por tu usario__.

🔅 Configuramos algunas cosas:

__Una vez iniciado arch, hagamos algunos ajustes:__

~~~ bash
su
passwd linuxmobile
# Y le asignamos el password a nuestro usuario.
~~~

~~~ bash
# Lo necesario para instalar un AUR helper. En mi caso, prefiero paru.
sudo pacman -S git base-devel
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
~~~