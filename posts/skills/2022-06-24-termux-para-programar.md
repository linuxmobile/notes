---
layout: post
title: "Configurando Termux para programar"
tags: [Skills, Linux, Shell, Android]
toc: true
good: true
icon: linux.svg
keywords: "android termux linux arch artix configurando configurar setup programar programming hub fullstack developer learning how to ide IDE myTermux ohmyzsh zsh bat cat cava ncmpcpp mpd mpc mpv firefox develop developing programando tablet samsung xiaomi tab galaxy"
---

Tips / Instalación / Termux

👉 Nota: Para descargar, utilizaremos la versión de F-Droid: [Termux](https://f-droid.org/en/packages/com.termux/)

<div class="p-list">

<i>Hace unos días adquirí una tablet con el fin de estudiar programación Fullstack con ella. Hoy me gustaría enseñarles como configurar Termux para poder programar; junto con algunas aplicaciones que yo utilizo.</i>

## Instalando lo necesario:

🔅 Para comenzar actualizamos Termux, y configuramos algunas opciones:

~~~ bash
# Actualizamos los paquetes y termux.
pkg update && pkg upgrade

# Damos permisos de almacenamiento.
termux-setup-storage

# Instalamos algunos paquetes necesarios.
pkg i -y git bc wget
~~~

🔅 Instalamos algunos paquetes:

::: hsbox (opcional)
__Estos paquetes son opcionales, pero sin estos la configuración de las screenshots, no te va a funcionar__
:::

~~~ bash
pkg i -y zsh bat exa neofetch termux-api tmux awesomeshot lf inotify-tools neovim 
~~~

</div>
