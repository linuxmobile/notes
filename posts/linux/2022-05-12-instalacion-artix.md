---
layout: post
title: "Tutorial / Instalación / Artix"
tags: [Linux, Shell]
toc: true
good: true
noOutdated: true
icon: linux.svg
date: 2022-05-12
keywords: "find with command line in linux ubuntu archlinux elementary os distro distribution files trash owner screen shot screenshot windows partition resize disk drive turn off minimize wm manager kill process .bin .run install shrink disk ipconfig thunar file shortcut hotkey keybind $PATH vim neovim nvim folder mount iso disk extract rsync ssh youtube-dl mp3 installing install instalacion artix artixkeyring"
---

Tips / Instalación / Artix

👉 Nota: [ISO de Artix / dinit](https://iso.artixlinux.org/iso/artix-base-dinit-20220123-x86_64.iso)

::: warning
Recuerda que el usuario y contraseña es "artix"
:::

::: danger
Importante hacer todo el proceso como root (haciendo `su` y dando enter)
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

👉 Note: __En mi caso mi disco es NVME, por ende remplacen NVME por sda, sdb o el que tengan.__

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
mount /dev/disk/by-label/EFI /mnt/boot
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

~~~ bash
fstabgen -U /mnt >> /mnt/etc/fstab

# No olvidemos corroborar que todo esté bien con un:
cat /mnt/etc/fstab

# Dentro tendrían que tener la partición Root, Home y boot
~~~

🔅 Ahora entramos como root con:

~~~ bash
artix-chroot /mnt
~~~
</div>

## Configurando el sistema

<div class="p-list">

🔅 Configuramos el reloj

~~~ bash
ln -sf /usr/share/zoneinfo/America/Argentina/Buenos_aires /etc/localtime  # Acá tenemos que poner la region/ciudad.
~~~

👉 Note: __Con hwclock generamos el /etc/adjtime__

~~~ bash
hwclock --systohc
~~~

🔅 Configuramos la Localización:

~~~ bash
pacman -s nano
nano /etc/locale.gen
~~~

👉 Note: __En el locale.gen yo elijo en_US. (Si querés el sistema en español es_ES.__

~~~ bash
# generamos los locales
locale-gen
~~~

🔅 Instalamos el bootloader:

~~~ bash
pacman -S grub efibootmgr
~~~

👉 Note: __Mi configuración está pensada para sistemas UEFI. Atentos...__

🔅 Instalamos el grub:

~~~ bash
# Si estás usando MBR y no UEFI:
# grub-install --recheck /dev/sda
grub-install --target=x86_64-efi --efi-directory=/boot --recheck

grub-mkconfig -o /boot/grub/grub.cfg
~~~

🔅 Asignamos la contraseña a ROOT:

~~~ bash
passwd # al darle enter, tipeas la contraseña.

useradd -m -G wheel -s /bin/bash tusuario # Obviamente, tipea tu usario ahí!

passwd tusuario
~~~

🔅 Agregamos WHEEL al archivo sudoers:

~~~ bash
EDITOR=nano visudo
# descomentamos: (quitando el #)
%wheel ALL=(ALL:ALL) ALL
~~~

🔅 Configuramos el hostname:

~~~ bash
nano /etc/hostname
# el hostname es un darle un nombre al host: 
# A mi me gusta "aesthetic" Por ende puede ser como tu nickname.
~~~

🔅 Lo agergamos al hosts:

::: code-output-flex
~~~ bash
nano /etc/hosts
~~~

~~~ bash
127.0.0.1               localhost
::1                     localhost
127.0.1.1               tuhostname.localdomain tuhostname

# reemplaza "tuhostname" por el hostname que elegiste.
~~~
:::

🔅 Instalamos DHCPCD (es el cliente de internet):

~~~ bash
pacman -S dhcpcd dhcpcd-dinit
~~~

🔅 Desmontamos y reiniciamos:

~~~ bash
exit                    # Para salir de su
exit                    # Para salir de chroot
umount -R /mnt          # Para desmontar
reboot                  # Para reiniciar
~~~

::: info
Llegado a este punto en el que reiniciaste y no te apareció ningún error o inconveniente, quiere decir que ya terminaste de instalar la base del sistema __Artix__. Me gustaría dejarte unos pasos más a seguir que me sirvieron cuando no entendía nada.
:::

🔅 Habilitar DHCPCD para tener internet:

~~~ bash
sudo dinitctl enable dhcpcd
sudo dinitctl start dhcpcd
~~~

🔅 Habilitamos los repositorios de Archlinux:

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

🔅 Habilitas los repos y los regeneras:

~~~ bash
sudo pacman-key --populate archlinux

# Actualizas los repos
sudo pacman -Syy
~~~

🔅 Instalamos paru:
__Si no tenemos git, hay que instalarlo `sudo pacman -S git wget`.__

~~~ bash
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si
~~~

🔅 Por último, unos paquetes que te van a ser útiles:

~~~ bash
# Manejar archivos zip, rar.
sudo pacman -S zip unzip unrar

# Alsa y pipewire para manejar el audio
sudo pacman -S pipewire pipewire-alsa pipewire-pulse alsa-utils

# Para poder visualizar discos externos
sudo pacman -S ntfs-3g dosfstools exfat-utils

# Drivers de intel / amd
paru -S xf86-video-amdgpu vulkan-radeon mesa-libgl mesa-vdpau libvdpau-va-gl libva-mesa-driver #AMD 
paru -S xf86-video-intel mesa-libgl libvdpau-va-gl #Intel
~~~

::: success
Bueno, eso es todo. Espero que te haya servido. Si llegaste hasta acá te pido que revises mis otras notas!
:::

</div>
