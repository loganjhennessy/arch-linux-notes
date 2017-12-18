# Base System

Base system was created by following GloriousEggroll's guide:
[Arch Linux EFI Install Guide](https://www.gloriouseggroll.tv/arch-linux-efi-install-guide)

His instructions were followed right up until he started getting into installing
NVIDIA drivers and binaries, at which point I took a snapshot of the system.

# Installing Yaourt

In order to install my desired window manager (qtile), it was recommended I use yaourt.

To install yaourt, I found the following guide:
[Install Yaourt Arch Linux](https://ostechnix.com/install-yaourt-arch-linux)

And I went with option 2. Install Yaourt using AUR. This called for the following:

Install prerequisite packages:
`sudo pacman -S --needed base-devel git wget yajl`

Install package-query:
`git clone https://aur.archlinux.org/package-query.git`
`cd package-query`
`makepkg -si`
`cd ..`

Install yaourt:
`git clone https://aur.archlinux.org/yaourt.git`
`cd yaourt`
`makepkg -si`
`cd ..`

Cleanup:
`sudo rm -dR yaourt/ package-query/`


# Installing qtile

Arch Linux has a supported distribution of qtile. It should work with just:
`yaourt -S qtile`

But, when I tried this, I got a WARNING message I didn't understand, so I
decided to go with the moree hands-on approach and install it from source.

# Installing Python from source

Used the following guide:

[Installing Python from Source](https://passingcuriosity.com/2015/installing-python-from-source)

When downloading python, the first thing that needs to be done is to verify
the authenticity of the download. To do this, we need to get the keys from
Python. Instructions can be found on [The Python Downloads Page](https://www.python.org/downloads/):

`wget https://www.python.org/static/files/pubkeys.txt`
`gpg --import pubkeys.txt`

Then we can verify the downloaded signature with:
`gpg --verify Python-3.x.x.tgz.asc`

The rest can be done line-for-line from the guide above. My VirtualBox
configuration had audio turned off, so there were a few modules that were
skipped in the `make test` step, but it still all checked out o.k. 

# Installing vim

I got tired of using nano for everything, so I read up on [vim](https://wiki.archlinux.org/index.php/vim)
on the Arch Linux wiki. This led me to install gvim, since it looked like the
more full-featured vim:

`sudo pacman -S gvim`

# Installing pip

Pip was actually super easy, comparatively speaking. The following just worked:

`sudo pacman -S python-pip`

This installed the latest (version 9) pip for Python 3.

# Install qtile dependencies

Qtile needs a bunch of things that are downloaded via pip. Since they are
running system-wide, I believe it is okay to install them using sudo, so
this is what I did.

Here's the [guide I used](docs.qtile.org/en/latest/manual/install/index.html).

And the commands:
`sudo pip install xcffib`
`sudo pip install cairocffi`

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

`sudo pacman -S python-dbus`
`sudo pacman -S python-gobject`


# Actually running Qtile

I still wasn't done at this point.

To actually get Qtile to run, I needed X. Still  not sure what this nebulus
program is or does, but it is needed. So I installed the following:

`sudo pacman -S xorg-xinit`
`sudo pacman -S xorg-server`
`sudo pacman -S xterm`

The first was definitely necessary. The second, I'm not enitrely sure about.
And the third was just to test that Qtile was working.

Making Qtile work required copying the default_config.py file over from the
cloned repo and then setting an environment varaible as described in the Qtile
docs. I tried a bunch of stuff so I'm not actually sure what it was that made
it work.

Once it had a configuration, I was able to run:

`startx`

And Qtile started up.
