---
layout: post
title: "Tutorial / Instalaci贸n / Artix"
tags: [Skills, Linux, Shell]
toc: true
good: true
noOutdated: true
icon: linux.svg
keywords: "find with command line in linux ubuntu archlinux elementary os distro distribution files trash owner screen shot screenshot windows partition resize disk drive turn off minimize wm manager kill process .bin .run install shrink disk ipconfig thunar file shortcut hotkey keybind $PATH vim neovim nvim folder mount iso disk extract rsync ssh youtube-dl mp3 installing install instalacion artix artixkeyring"
---

Tips / Instalaci贸n / Artix

馃憠 Nota: [ISO de Artix / dinit](https://iso.artixlinux.org/iso/artix-base-dinit-20220123-x86_64.iso)

::: warning
Recuerda que el usuario y contrase帽a es "artix"
:::

::: danger
Importante hacer todo el proceso como root (haciendo `su` y dando enter)
:::

## Configuraci贸n del lenguaje

<div class="p-list">

馃攨 Para checkear los layouts disponibles:

~~~ bash
ls -R /usr/share/kbd/keymaps 
~~~

馃攨 Ahora tipeamos el nombre del layout sin la extension. Por ejemplo, yo utilizo el layout de latinoam茅rica.

~~~ bash
loadkeys la-latin1
~~~

</div>

## Particionado

<div class="p-list">

馃攨 Corroboramos en qu茅 disco vamos a instalar:

~~~ bash
fdisk -l
~~~

馃攨 Particionamos el disco:

馃憠 Note: __En mi caso mi disco es NVME, por ende remplacen NVME por sda, sdb o el que tengan.__

::: code-output-flex
~~~ bash
cfdisk /dev/nvme0n1 
~~~

~~~ 
      Start                  Size
/dev/nvme0n1p1               512M         # 512M / 1GB recomendado

/dev/nvme0n1p2               60G          # 40/60GB

/dev/nvme0n1p3              405.3G        # El resto del espacio
~~~
:::

馃憠 Note: <i>La instalaci贸n que realizo es exclusivamente para UEFI, y tambi茅n consideren que no estoy utilizando dual boot. S贸lo artix.</i>

馃攨 Formateamos las particiones:

~~~ bash
mkfs.ext4 -L ROOT /dev/nvme0n1p2        # Particion root
mkfs.ext4 -L HOME /dev/nvme0n1p3        # Particion home (opcional
mkfs.fat -F 32 /dev/nvme0n1p1           # Particion EFI/boot
fatlabel /dev/nvme0n1p1 EFI
~~~

馃攨 Montamos las particiones:

~~~ bash
mount /dev/disk/by-label/ROOT /mnt
mkdir -p /mnt/boot
mkdir -p /mnt/home
mount /dev/disk/by-lable/HOME /mnt/home
mount /dev/disk/by-label/EFI /mnt/boot
~~~

馃攨 Conectamos al internet:

馃憠 Note: <i>En mi caso, utilizo Cable. As铆 que no solicito demasiado configuraci贸n.</i>

~~~ bash
ping artixlinux.org
~~~
</div>

## Instalamos la base:

<div class="p-list">

馃攨 Instalamos la base usando basestrap:

~~~ bash
# En mi caso voy a elegir DINIT.
basestrap /mnt base base-devel dinit elogind-dinit
~~~

馃攨 Instalamos el kernel:

~~~ bash
basestrap /mnt linux linux-firmware
~~~

馃攨 Generamos el /etc/fstab. Para esto uso `-U` para que sea UUIDs. Y `-L` para las particiones.

~~~ bash
fstabgen -U /mnt >> /mnt/etc/fstab

# No olvidemos corroborar que todo est茅 bien con un:
cat /mnt/etc/fstab

# Dentro tendr铆an que tener la partici贸n Root, Home y boot
~~~

馃攨 Ahora entramos como root con:

~~~ bash
artix-chroot /mnt
~~~
</div>

## Configurando el sistema

<div class="p-list">

馃攨 Configuramos el reloj

~~~ bash
ln -sf /usr/share/zoneinfo/America/Argentina/Buenos_aires /etc/localtime  # Ac谩 tenemos que poner la region/ciudad.
~~~

馃憠 Note: __Con hwclock generamos el /etc/adjtime__

~~~ bash
hwclock --systohc
~~~

馃攨 Configuramos la Localizaci贸n:

~~~ bash
pacman -s nano
nano /etc/locale.gen
~~~

馃憠 Note: __En el locale.gen yo elijo en_US. (Si quer茅s el sistema en espa帽ol es_ES.__

~~~ bash
# generamos los locales
locale-gen
~~~

馃攨 Instalamos el bootloader:

~~~ bash
pacman -S grub efibootmgr
~~~

馃憠 Note: __Mi configuraci贸n est谩 pensada para sistemas UEFI. Atentos...__

馃攨 Instalamos el grub:

~~~ bash
# Si est谩s usando MBR y no UEFI:
# grub-install --recheck /dev/sda
grub-install --target=x86_64-efi --efi-directory=/boot --recheck

grub-mkconfig -o /boot/grub/grub.cfg
~~~

馃攨 Asignamos la contrase帽a a ROOT:

~~~ bash
passwd # al darle enter, tipeas la contrase帽a.

useradd -m -G wheel -s /bin/bash tusuario # Obviamente, tipea tu usario ah铆!

passwd tusuario
~~~

馃攨 Agregamos WHEEL al archivo sudoers:

~~~ bash
EDITOR=nano visudo
# descomentamos: (quitando el #)
%wheel ALL=(ALL:ALL) ALL
~~~

馃攨 Configuramos el hostname:

~~~ bash
nano /etc/hostname
# el hostname es un darle un nombre al host: 
# A mi me gusta "aesthetic" Por ende puede ser como tu nickname.
~~~

馃攨 Lo agergamos al hosts:

::: code-output-flex
~~~ bash
nano /etc/hosts
~~~

~~~ bash
127.0.0.1               localhost
::1                     localhost
127.0.1.1               tuhostname.localdomain tuhostname

#reemplaza "tuhostname" por el hostname que elegiste.
~~~

馃攨 Instalamos DHCPCD (es el cliente de internet):

~~~ bash
pacman -S dhcpcd dhcpcd-dinit
~~~

馃攨 Desmontamos y reiniciamos:

~~~ bash
exit                    # Para salir de su
exit                    # Para salir de chroot
umount -R /mnt          # Para desmontar
reboot                  # Para reiniciar
~~~

::: info
Llegado a este punto en el que reiniciaste y no te apareci贸 ning煤n error o inconveniente, quiere decir que ya terminaste de instalar la base del sistema __Artix__. Me gustar铆a dejarte unos pasos m谩s a seguir que me sirvieron cuando no entend铆a nada.
:::

馃攨 Habilitar DHCPCD para tener internet:

~~~ bash
sudo dinitctl enable dhcpcd
sudo dinitctl start dhcpcd
~~~

馃攨 Habilitamos los repositorios de Archlinux:

~~~ bash
sudo pacman -S artix-archlinux-support

# Agregamos los repos a /etc/pacman.conf (con nano /etc/pacman.conf)

[extra]
Include = /etc/pacman.d/mirrorlist-arch

[community]
Include = /etc/pacman.d/mirrorlist-arch

[multilib]
Include = /etc/pacman.d/mirrorlist-arch
~~~

馃攨 Habilitas los repos y los regeneras:

~~~ bash
sudo pacman-key --populate archlinux

# Actualizas los repos
sudo pacman -Syy
~~~

馃攨 Instalamos paru:
__Si no tenemos git, hay que instalarlo `sudo pacman -S git wget`.__

~~~ bash
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
~~~

馃攨 Por 煤ltimo, unos paquetes que te van a ser 煤tiles:

~~~ bash
# Manejar archivos zip, rar.
sudo pacman -S zip unzip unrar

# Alsa y pipewire para manejar el audio
sudo pacman -S pipewire pipewire-alsa pipewire-pulse alsa-utils

# Para poder visualizar discos externos
sudo pacman -S ntfs-3g dosfstools exfat-utils

# Drivers de intel / amd
paru -S xf86-video-amdgpu vulkan-radeon mesa-libgl mesa-vdpau libvdpau-va-gl #AMD 
paru -S xf86-video-intel mesa-libgl libvdpau-va-gl #Intel
~~~

::: success
Bueno, eso es todo. Espero que te haya servido. Si llegaste hasta ac谩 te pido que revises mis otras notas!
:::

</div>
