# Base System

Base system was created by following GloriousEggroll's guide:
[Arch Linux EFI Install Guide](https://www.gloriouseggroll.tv/arch-linux-efi-install-guide)

His instructions were followed right up until he started getting into installing
NVIDIA drivers and binaries, at which point I took a snapshot of the system.

# Take 1

## Installing Yaourt

In order to install my desired window manager (qtile), it was recommended I use yaourt.

To install yaourt, I found the following guide:
[Install Yaourt Arch Linux](https://ostechnix.com/install-yaourt-arch-linux)

And I went with option 2. Install Yaourt using AUR. This called for the following:

Install prerequisite packages:
`sudo pacman -S --needed base-devel git wget yajl`

Install package-query:
```
git clone https://aur.archlinux.org/package-query.git
cd package-query
makepkg -si
cd ..
```

Install yaourt:
```
git clone https://aur.archlinux.org/yaourt.git
cd yaourt
makepkg -si
cd ..
```

Cleanup:

`sudo rm -dR yaourt/ package-query/`


## Installing qtile

Arch Linux has a supported distribution of qtile. It should work with just:
`yaourt -S qtile`

But, when I tried this, I got a WARNING message I didn't understand, so I
decided to go with the moree hands-on approach and install it from source.

## Installing Python from source

Used the following guide:

[Installing Python from Source](https://passingcuriosity.com/2015/installing-python-from-source)

When downloading python, the first thing that needs to be done is to verify
the authenticity of the download. To do this, we need to get the keys from
Python. Instructions can be found on [The Python Downloads Page](https://www.python.org/downloads/):

```
wget https://www.python.org/static/files/pubkeys.txt
gpg --import pubkeys.txt
```

Then we can verify the downloaded signature with:
`gpg --verify Python-3.x.x.tgz.asc`

The rest can be done line-for-line from the guide above. My VirtualBox
configuration had audio turned off, so there were a few modules that were
skipped in the `make test` step, but it still all checked out o.k. 

## Installing vim

I got tired of using nano for everything, so I read up on [vim](https://wiki.archlinux.org/index.php/vim)
on the Arch Linux wiki. This led me to install gvim, since it looked like the
more full-featured vim:

`sudo pacman -S gvim`

## Installing pip

Pip was actually super easy, comparatively speaking. The following just worked:

`sudo pacman -S python-pip`

This installed the latest (version 9) pip for Python 3.

## Install qtile dependencies

Qtile needs a bunch of things that are downloaded via pip. Since they are
running system-wide, I believe it is okay to install them using sudo, so
this is what I did.

Here's the [guide I used](docs.qtile.org/en/latest/manual/install/index.html).

And the commands:

```
sudo pip install xcffib
sudo pip install cairocffi
```

The next one was less than clear. The instructions said I needed `pangocairo`,
and provided a handy ubuntu `apt-get` command, but nothing for arch linux. So,
I looked it up in the arch linux repo and found `lib32-pango` which I assumed
to be equivalent. Then I ran:

`sudo pacman -S lib32-pango`

This presented three options of providers. The latter two both had NVIDIA in
the name, so I opted for option 1). This installed.

The next step (asyncio/trollius) was void since I'm running Python 3.6.

The next step was for dbus and gobject. Fortunately Arch Linux had both of
these in the repo, so it was just as simple as two pacman calls:

```
sudo pacman -S python-dbus
sudo pacman -S python-gobject
```

## Actually running Qtile

I still wasn't done at this point.

To actually get Qtile to run, I needed X. Still  not sure what this nebulus
program is or does, but it is needed. So I installed the following:

```
sudo pacman -S xorg-xinit
sudo pacman -S xorg-server
sudo pacman -S xterm
```

The first was definitely necessary. The second, I'm not enitrely sure about.
And the third was just to test that Qtile was working.

Making Qtile work required copying the default_config.py file over from the
cloned repo and then setting an environment varaible as described in the Qtile
docs. I tried a bunch of stuff so I'm not actually sure what it was that made
it work.

Once it had a configuration, I was able to run:

`startx`

And Qtile started up.

# Take 2

Found another tutorial, this one specific to VirtualBox:

[A Guide to Installing Arch in VirtualBox](http://wideaperture.net/blog/?p=3851)

## Installing X and Guest Additions

### X stuff

```
sudo pacman -S xorg-server xorg-xinit
sudo pacman -S xorg-apps
```

And go with the default (all), and option 1).

After all that, `startx` still didn't work, so I found this StackExchange post:

[Arch Installation startx not working](https://unix.stackexchange.com/questions/12269/arch-installation-startx-not-working)

Then ran:

`sudo pacman -S xorg-utils`

I also install `xorg-server` again, but I don't think that did anything.

Then `startx` worked.

### Python

```
sudo pacman -S wget git
```

#### Installing Python from source

Used the following guide:

[Installing Python from Source](https://passingcuriosity.com/2015/installing-python-from-source)

When downloading python, the first thing that needs to be done is to verify
the authenticity of the download. To do this, we need to get the keys from
Python. Instructions can be found on [The Python Downloads Page](https://www.python.org/downloads/):

```
wget https://www.python.org/static/files/pubkeys.txt
gpg --import pubkeys.txt
```

Download the following files:
```
wget https://www.python.org/ftp/python/3.6.3/Python-3.6.3.tgz
wget https://www.python.org/ftp/python/3.6.3/Python-3.6.3.tgz.asc
```

Then we can verify the downloaded signature with:
`gpg --verify Python-3.6.3.tgz.asc Python-3.6.3.tgz`

The rest can be almost done line-for-line from the guide above. My VirtualBox
configuration had audio turned off, so there were a few modules that were
skipped in the `make test` step, but it still all checked out o.k.

One tweak was to use the `--enable-optimizations` flag when running `./configure`.
Not sure how much this helps, but I figured it can't hurt.

So it becomes:

```
tar xvf Python-3.6.3.tgz
cd Python-3.6.3
./configure --enable-optimizations
make
make test
sudo make install
```

### Installing pip

Pip was actually super easy, comparatively speaking. The following just worked:

`sudo pacman -S python-pip`

This installed the latest (version 9) pip for Python 3.

### Install qtile dependencies

Qtile needs a bunch of things that are downloaded via pip. Since they are
running system-wide, I believe it is okay to install them using sudo, so
this is what I did.

Here's the [guide I used](docs.qtile.org/en/latest/manual/install/index.html).

And the commands:

```
sudo pip install xcffib
sudo pip install cairocffi
```

The next one was less than clear. The instructions said I needed `pangocairo`,
and provided a handy ubuntu `apt-get` command, but nothing for arch linux. So,
I looked it up in the arch linux repo and found `lib32-pango` which I assumed
to be equivalent. Then I ran:

`sudo pacman -S lib32-pango`

This presented three options of providers. The latter two both had NVIDIA in
the name, so I opted for option 1). This installed.

The next step (asyncio/trollius) was void since I'm running Python 3.6.

The next step was for dbus and gobject. Fortunately Arch Linux had both of
these in the repo, so it was just as simple as two pacman calls:

```
sudo pacman -S python-dbus
sudo pacman -S python-gobject
```

# Take 3/4

Okay. Let's try this again. This time I am following [this guide](https://github.com/jieverson/dotfiles/wiki/arch-linux-for-dummies)
because the author specifically has decided to install a window manager.

```
ls /sys/firmware/efi/efivars    # Ensure EFI booted properly
ping -c2 google.com             # Ensure interenet is working

# Ensure the system clock is accurate
timedatectl set-ntp true
timedatectl status

# Partitioning
lsblk                           # List drives and partitions
parted sda
(parted) print                  # Will result in ERROR
(parted) mklabel gpt
(parted) print                  # ERROR is fixed now
(parted) mkpart ESP fat32 1MiBB 1025MiB
(parted) set 1 boot on
(parted) mkpart primary linux-swap 1025M 4G
(parted) mkpart primary ext4 4G 100%
(parted) quit
lsblk /dev/sda

# Formatting
mkfs.fat -F32 /dev/sda1
mkswap /dev/sda2
swapon /dev/sda2
mkfs.ext4 /dev/sda3

# Mountinig
mount /dev/sda3 /mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot

# Installation
pacstrap /mnt
genfstab -U /mnt >> /mnt/etc/fstab

## Change to root user
arch-chroot /mnt /bin/bash
nano /etc/locale.gen
locale-gen
echo LANG=en_US.UTF-8 > /etc/locale.conf
tzselect

## Find zone inside /usr/share/zoneinfo
rm /etc/localtime
ln -s /usr/share/zoneinfo/America/Los_Angeles /etc/localtime
hwclock --systohc --utc

# Boot loader
pacman -S grub efibootmgr
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub
mkdir /boot/EFI/boot
cp /boot/EFI/grub/grubx64.efi /boot/EFI/boot/bootx64.efi
pacman -S intel-ucode
grub-mkconfig -o /boot/grub/grub.cfg

# Network configuration
echo arch-template-2 > /etc/hostname
```

# Take 5

Loosely following: http://wideaperture.net/blog/?p=3851

## Partition

Starting from a VBox Hard Drive with 60GB of space.

Only a boot partition with 512M and a root partition for everything else.

For both of these, I just used the default 8300 hex code for Linux file system.

I used gdisk to create a GPT partition table.

## Format

Then format them both to ext4

```
mkfs.ext4 /dev/sda1
mkfs.ext4 /dev/sda2
```

## Mount

Only two partitions to mount

```
mount /dev/sda2 /mnt
cd /mnt
mkdir boot
mount /dev/sda1 boot
```

## Installation

```
# first, rank the mirrors
# make a backup
cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup

# uncomment all the servers
sed -i 's/^#Server/Server/' /etc/pacman.d/mirrorlist.backup

# run the rankmirrors script
rankmirrors -n 10 /etc/pacman.d/mirrorlist.backup > /etc/pacman.d/mirrorlist

# actual installation
pacstrap -i /mnt base base-devel
```

## Bootloader

`pacstrap /mnt syslinux`


## Setup

```
nano /etc/locale.conf # opens an empty file

# add this line
LANG="en_US.UTF-8"

# then
nano /etc/locale.gen

# uncomment these lines
en_US.UTF-8 UTF-8
en_US ISO-8859-1

# set time zone
rm /etc/localtime
ln -s /usr/share/zoneinfo/America/Los_Angeles
```

# Take 6 - Success

Specs:
- BIOS GPT partition table
- GRUB bootloader

Loosely based on: 

- http://wideaperture.net/blog/?p=3851
- https://blog.m157q.tw/posts/2013/12/30/arch-linux-quick-installation-with-gpt-in-bios/
- https://github.com/jieverson/dotfiles/wiki/arch-linux-for-dummies

## Partition

Starting from a VBox Hard Drive with 60GB of space.

Partioned as follows:

- /dev/sda1, boot partition with 512M
- /dev/sda2, BIOS boot (hex code EF02) with 1M
- /dev/sda3, root partition with the remainder

For the boot and root partitions, I just used the default 8300 hex code for Linux file system.

I used gdisk to create a GPT partition table.

## Format

Then format boot and root to ext4, and the BIOS boot to vfat

```
mkfs.ext4 /dev/sda1
mkfs.vfat /dev/sda2
mkfs.ext4 /dev/sda3
```

## Mount

Only two partitions to mount

```
mount /dev/sda2 /mnt
cd /mnt
mkdir boot
mount /dev/sda1 boot
```

## Installation

```
# first, rank the mirrors
# make a backup
cp /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup

# uncomment all the servers
sed -i 's/^#Server/Server/' /etc/pacman.d/mirrorlist.backup

# run the rankmirrors script
rankmirrors -n 10 /etc/pacman.d/mirrorlist.backup > /etc/pacman.d/mirrorlist

# actual installation
pacstrap -i /mnt base base-devel
```

## Setup and Configuration

```
# Fstab
genfstab -U /mnt >> /mnt/etc/fstab

# Chroot
arch-chroot /mnt

# Time zone
ln -sf /usr/share/zoneinfo/America/Los_Angeles /etc/localtime
hwclock --systohc
nano /etc/locale.gen
  en_US.UTF-8 UTF-8 # uncomment
  en_US ISO-8859-1  # uncomment
locale-gen
nano /etc/locale.conf
  LANG=en_US.UTF-8
  
# Hostname
nano /etc/hostname
  arch-template
  
# Initramfs
mkinitcpio -p linux

# Root password
passwd
```

## Internet

```
dhcpcd
pacman -S networkmanager
systemctl enable NetworkManager.service
nmtui-connect
```

## Finishing up

```
exit
umount /mnt/boot
umount /mnt

# remove virtual disk from drive, then
reboot
```

## Create user

```
useradd -m -g users -G wheel,storage,power -s /bin/bash lhennessy
passwd lhennessy
  # set the password
EDITOR=nano visudo
  # uncomment the line
  %wheel ALL=(ALL) ALL
```

## Virtualbox stuff

```
sudo pacman -S virtualbox-guest-utils
  # choose virtualbox-guest-modules-arch
modprobe -a vboxguest vboxsf vboxvideo
VBoxClient-all
```

## Autologin via lightdm

```
sudo pacman -S lightdm
```

Follow the autologin instructions in the [LightDM Arch Linux Wiki page](https://wiki.archlinux.org/index.php/LightDM#Enabling_autologin)

```
# /etc/lightdm/lightdm.conf
[Seat:*]
autologin-user={username}
autologin-session=awesome

# enable the user to autologin
sudo groupadd -r autologin
sudo gpasswd -a {username} autologin
```

## Awesome WM Customizations

https://github.com/lcpz/awesome-copycats

## Todo

- Get audio and video to work
- Awesome WM customizations
- Python
- Bash and Vim customizations

## Done

- Copy and paste from VM
  - For writing and saving the gist
- Display manager
  - Auto-login
- Firefox
- Vim alias
- Neovim
- Second screen