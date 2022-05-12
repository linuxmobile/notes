---
layout: post
title: "Tutorial / Instalación / Artix"
tags: [Skills, Linux, Shell]
toc: true
good: true
noOutdated: true
icon: linux.svg
keywords: "find with command line in linux ubuntu archlinux elementary os distro distribution files trash owner screen shot screenshot windows partition resize disk drive turn off minimize wm manager kill process .bin .run install shrink disk ipconfig thunar file shortcut hotkey keybind $PATH vim neovim nvim folder mount iso disk extract rsync ssh youtube-dl mp3 installing install instalacion artix artixkeyring"
---

Tips / Instalación / Artix

👉 Nota: [ISO de Artix / dinit](https://iso.artixlinux.org/iso/artix-base-dinit-20220123-x86_64.iso)

::: warning
Recuerda que el usuario y contraseña es "artix"
:::

## Configuración del lenguaje

<div class="p-list">

🔅 Para checkear los layouts disponibles:

~~~ bash
ls -R /usr/share/kbd/keymaps 
~~~

🔅 Ahora tipeamos el nombre del layout sin la extension. Por ejemplo, yo utilizo el layout de latinoamérica.

~~~ bash
loadkeys la-latin1
~~~

</div>

## Particionado

<div class="p-list">

🔅 Corroboramos en qué disco vamos a instalar:

~~~ bash
fdisk -l
~~~

🔅 Particionamos el disco:

::: code-output-flex
~~~ bash
cfdisk /dev/nvme0n1 
~~~

~~~ 
      Start                  Size
>> /dev/nvme0n1p1            512M

>> /dev/nvme0n1p2            60G

>> /dev/nvme0n1p3            405.3G
~~~
:::

👉 Note: <i>La instalación que realizo es exclusivamente para UEFI, y también consideren que no estoy utilizando dual boot. Sólo artix.</i>

🔅 Formateamos las particiones:

~~~ bash
mkfs.ext4 -L ROOT /dev/nvme0n1p2        # Particion root
mkfs.ext4 -L HOME /dev/nvme0n1p3        # Particion home (opcional
mkfs.fat -F 32 /dev/nvme0n1p1           # Particion EFI/boot
fatlabel /dev/nvme0n1p1 EFI
~~~

🔅 Montamos las particiones:

~~~ bash
mount /dev/disk/by-label/ROOT /mnt
mkdir -p /mnt/boot
mkdir -p /mnt/home
mount /dev/disk/by-lable/HOME /mnt/home
mount /dev/disk/by-label/BOOT /mnt/boot
~~~

🔅 Conectamos al internet:

👉 Note: <i>En mi caso, utilizo Cable. Así que no solicito demasiado configuración.</i>

~~~ bash
ping artixlinux.org
~~~
</div>

## Instalamos la base:

<div class="p-list">

🔅 Instalamos la base usando basestrap:

~~~ bash
# En mi caso voy a elegir DINIT.
basestrap /mnt base base-devel dinit elogind-dinit
~~~

🔅 Instalamos el kernel:

~~~ bash
basestrap /mnt linux linux-firmware
~~~

🔅 Generamos el /etc/fstab. Para esto uso `-U` para que sea UUIDs. Y `-L` para las particiones.

👉 Note: <i>Para este punto no hace falta recordar que tienen que utilizar `sudo`.</i>

~~~ bash
fstabgen -U /mnt >> /mnt/etc/fstab

# No olvidemos corroborar que todo esté bien con un:
cat /mnt/etc/fstab
~~~

🔅 Ahora entramos como root con:

~~~ bash
artix-chroot /mnt
~~~
</div>

## Configurames el sistema

<div class="p-list">

🔅 Configuramos el reloj

~~~ bash
ln -sf /usr/share/zoneinfo/America/Argentina/Buenos_aires /etc/localtime  # Acá tenemos que poner la region/ciudad.
~~~

__Con hwclock generamos el /etc/adjtime__

~~~ bash
hwclock --systohc
~~~

🔅 Configuramos la Localización:

~~~ bash
pacman -s nano
nano /etc/locale.gen
~~~

__En el locale.gen yo elijo en_US__

~~~ bash
# generamos los locales
locale-gen
~~~

🔅 Instalamos el bootloader:

~~~ bash
pacman -S grub efibootmgr
~~~

</div>
