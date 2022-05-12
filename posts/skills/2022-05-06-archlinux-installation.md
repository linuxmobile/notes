---
layout: post
title: "Mi Instalación de Archlinux"
tags: [Skills, Linux, Shell]
toc: true
notfull: 1
icon: linux.svg
keywords: "find with command line in linux ubuntu archlinux elementary os distro distribution files trash owner screen shot screenshot windows partition resize disk drive turn off minimize wm manager kill process .bin .run install shrink disk ipconfig thunar file shortcut hotkey keybind $PATH vim neovim nvim folder mount iso disk extract rsync ssh youtube-dl mp3"
---

::: success
Esta nota se mantendrá actualizada.
:::

Tips / Instalación / **archlinux**

👉 Note:
👉 Note:

## ISO

<div class="p-list">

🔅 Primero descargamos la ISO oficial de [Aquí](https://archlinux.org/download)

🔅 Escribimos la iso en un usb:

~~~ bash
# Si estamos usando otra distro de linux podemos hacer lo siguiente
fdisk -l # para conocer la partición del usb
dd if=$HOME/Downloads/archlinux-version-.iso of=/dev/sdX bs=1M # bs=1M es opcional
~~~

Si estamos utilizando __Windows__ podemos recurrir a [Rufus](https://rufus.ie)

## Iniciamos el USB e instalamos la BASE

🔅 Siguiendo, tenemos que reiniciar en el usb. Normalmente con <kbd>F2</kbd> o <kbd>Supr</kbd> deberíamos poder ingresar al bios y seleccionar el USB.

🔅 Booteamos Archlinux y seguimos

🔅 Configuro el layout del teclado con `loadkeys la-latin1`

🔅 Habilitamos las descargas paralelas.

~~~ bash
nano /etc/pacman.conf

# Descomentamos la opción y le indicamos el número que consideremos necesario. En mi caso cinco, me parece perfecto.
ParallelDownloads = 5 
~~~

🔅 Guardamos con: 
 - <kbd>Ctrl</kbd> + <kbd>o</kbd>
 - <kbd>Ctrl</kbd> + <kbd>x</kbd>

🔅 Actualizamos los repositorios e instalamos git

~~~ bash
pacman -Sy git
~~~

🔅 Las particiones:

_En mi caso, yo mantengo el disco sin dual boot, solo tengo un Sistema operativo y es Archlinux. Pero voy a enseñar el método que uso si necesito dejar Windows (¡por alguna razón!)_ 

~~~ bash
# con fdisk compruebo el nombre de las particiones.
fdisk -l

# Creo la carpeta /boot y borro el boot anterior de la distro que normalmente tengo.
mkdir -p /mnt/boot/
mount /dev/nvme0n1p1 /mnt/boot/ 
~~~

_Este paso lo realizo para no tener que crear otra partición EFI, utilizo la misma que Windows, y no formateo esta misma partición_

``` bash
# borro las entradas del boot de archlinux

rm {*.img}
rm vmlinuz-linux
rm -rf grub/
rm -rf EFI/arch/

```

_Dejando la carpeta EFI/Microsoft sin tocar_

🔅 Luego formateo la partición donde irá la raíz `mkfs.btrfs -F /dev/nvme0n1p5/`

🔅 Montamos las particiones en `/mnt/`

~~~ bash
mount /dev/nvme0n1p5 /mnt
mount --mkdir /dev/nvme0n1p1 /mnt/boot # usamos --mkdir por si no creamos la carpeta
~~~

~~~ bash
pacstrap /mnt base base-devel linux linux-headers linux-firmware nano dhcpcd which btrfs-progs efitools git wget curl dosfstools
~~~

🔅 Generamos el archivo fstab

~~~ bash
genfstab -U /mnt >> /mnt/etc/fstab
~~~

🔅 Cambiamos a root dentro del sistema nuestro

~~~ bash
arch-chroot /mnt 
~~~

🔅 Comenzamos con algunas configuraciones básicas

~~~ bash
ln -sf /usr/share/zoneinfo/America/Buenos_Aires /etc/localtime

hwclock --systohc
~~~

🔅 Generamos los locales

~~~ bash
locale-gen

# Acá podemos editar el archivo y seleccionar el favorito
nano /etc/locale.conf
LANG=en_US.UTF=8 # en mi caso, prefiero en_US

nano /etc/vconsole.conf
KEYMAP=la-latin1
~~~

🔅 Le ponemos nombre al hostname

~~~ bash
nano /etc/hostname
aesthetic
~~~

🔅 Generamos un nuevo initramfs.

~~~ bash
# En este punto yo hago un paso que según otros comentarios es innecesario, pero a mi me alegra y deja conforme hacerlo:

echo "sed -i \"s/MODULES=()/MODULES=(nvme)/g\" /mnt/etc/mkinitcpio.conf"
sed -i "s/MODULES=()/MODULES=(nvme)/g" /mnt/etc/mkinitcpio.conf

# Ese paso agrega el módulo NVME.
mkinitcpio -P 
~~~

🔅 Creamos el password para root `passwd`

</div>
