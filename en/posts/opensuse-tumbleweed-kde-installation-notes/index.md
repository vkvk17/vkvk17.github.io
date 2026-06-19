# My openSUSE Tumbleweed and KDE Installation Notes


## Before Installation

There is not much to say here. I like using [Ventoy](https://www.ventoy.net/en/index.html) to create a bootable USB drive, then copying the [openSUSE Tumbleweed](https://get.opensuse.org/tumbleweed/) image onto it.

## KDE Plasma Desktop Configuration

My Plasma configuration is very simple. It mainly consists of the following steps:

1.  Move the panel to the top and reduce its size to 40.
2.  Go to Settings > Appearance > Icons and choose an icon pack I like. The default icon pack looks a little ugly to me, so I use Papirus.
3.  Go to Settings > Workspace Behavior > Virtual Desktops and adjust the virtual desktop layout. I set the number of rows to 1, and the default 2 virtual desktops are enough.
4.  Go to Settings > Window Management > Task Switcher and change the task switching effect from Breeze to Compact. It is the simplest switcher effect. I also remove the `` Alt + ` `` shortcut.
5.  Go to Settings > Input Devices > Keyboard > Advanced, enable Configure keyboard options, then enable Make Caps Lock an additional Ctrl. Of course we want to completely turn Caps Lock into Ctrl.

After logging into the system, KDE Plasma users may see a KDE Wallet error the first time they connect to a wireless network because a key is missing.

To avoid entering an authentication password repeatedly, open KDE Wallet, choose to create a new wallet, select the traditional Blowfish encrypted file format, leave the password empty, and confirm. This gives you an empty KDE Wallet with no authentication password, so connecting to the network no longer requires identity verification.

## YaST Configuration and Repository Changes

First, install the Simplified Chinese language package. In YaST > Language > Secondary Language, select Simplified Chinese to install it.

Next, open Software Repositories and remove the local repository that comes with the installation, something like `openSUSE-20240216-0`.

Then switch the official repositories to the [USTC openSUSE mirror](https://mirrors.ustc.edu.cn/help/opensuse.html). Because openSUSE uses MirrorBrain, the central server (`download.opensuse.org`) redirects download requests to nearby mirrors based on IP geolocation. However, repository metadata is still fetched from the central server when refreshing repositories, so changing mirrors usually speeds up repository refreshes more than actual package downloads.

Then add the Packman repository directly in the terminal:

bash

```ruby
sudo zypper ar -cfp 90 https://mirrors.ustc.edu.cn/packman/suse/openSUSE_Tumbleweed/ USTC:PACKMAN
```

After that, refresh and update Tumbleweed:

bash

```bash
sudo zypper ref && sudo zypper dup
sudo reboot
```

Then install some multimedia codecs:

bash

```bash
sudo zypper dist-upgrade --from USTC:PACKMAN --allow-vendor-change
sudo zypper install --from USTC:PACKMAN ffmpeg gstreamer-plugins-{good,bad,ugly,libav} libavcodec-full vlc-codecs
```

## Network Proxy Configuration

I use v2raya for network proxying. First install the v2ray core and v2raya:

bash

```bash
sudo zypper in v2ray
opi v2raya
```

After starting the v2raya service, follow the [quick start](https://v2raya.org/docs/prologue/quick-start/) guide to use the proxy. Transparent proxying can provide proxy access for almost all programs.

bash

```bash
sudo systemctl enable --now v2raya.service
```

For more proxy usage methods, see [Configuring proxies](https://zh.opensuse.org/SDB:%E9%85%8D%E7%BD%AE%E4%BB%A3%E7%90%86).

## Fonts and Chinese Input Method

Installing fonts in KDE is easy: drag the fonts into Font Management, preferably into the Personal Fonts group. Then refresh the font cache with `fc-cache -fv`.

The more traditional method is to place fonts in the following directories, listed roughly by priority:

bash

```swift
Font directories:
        /usr/X11R6/lib/X11/fonts
        /opt/kde3/share/fonts
        ~/.local/share/fonts
        ~/.local/share/flatpak/exports/share/fonts
        /var/lib/flatpak/exports/share/fonts
        /usr/local/share/fonts
        /usr/share/fonts
        ~/.fonts
        /usr/share/fonts/100dpi
        /usr/share/fonts/75dpi
        /usr/share/fonts/Type1
        /usr/share/fonts/cyrillic
        /usr/share/fonts/encodings
        /usr/share/fonts/ghostscript
        /usr/share/fonts/misc
        /usr/share/fonts/texlive-lm
        /usr/share/fonts/truetype
        /usr/share/fonts/xscreensaver
        /usr/share/fonts/encodings/large
```

I recommend putting fonts in `~/.fonts` or `~/.local/share/fonts`, then refreshing them with `fc-cache -fv`.

My recommended font is [Sarasa Term SC Nerd](https://github.com/laishulu/Sarasa-Term-SC-Nerd), because it handles table alignment well in Emacs and includes Nerd Fonts, which saves some trouble.

For input methods, I recommend installing [Fcitx5](https://zh.opensuse.org/Fcitx5):

bash

```bash
sudo zypper in fcitx5 fcitx5-chinese-addons
sudo zypper in fcitx5-configtool # install the configuration tool (optional)
```

When installing Fcitx5, the old Fcitx will be removed and replaced by default. Just follow the system prompts.

After installation, log in again. Then go to Settings > Regional Settings > Input Method and click Add Input Method to add Pinyin. If you are using KDE on X11, the required environment variables are already set automatically by the system.

You can also click Configure addons to adjust the input method UI and related settings.

Dictionaries can improve the input method experience. Here are two popular dictionaries:

-   [felixonmars/fcitx5-pinyin-zhwiki](https://github.com/felixonmars/fcitx5-pinyin-zhwiki)
-   [wuhgit/CustomPinyinDictionary](https://github.com/wuhgit/CustomPinyinDictionary)

Follow the README files in those repositories and place the downloaded or extracted `.dict` files into `~/.local/share/fcitx5/pinyin/dictionaries/`. If the directory does not exist, create it manually, then restart fcitx5.

## Installing Software

Here is a list of some software I install at the beginning. This is just a casual list; install what you need:

bash

```less
Install with zypper:
git
emacs
opi
keepassxc
mpv
telegram-desktop
qbittorrent
filezilla
wireshark
flameshot # installation guide (https://zh.opensuse.org/Flameshot)
cheat
nodejs
syncthing

Install with opi:
megasync
google-chrome-stable
v2raya
```

## Power Management for Laptops

The default TLP configuration is fine. You can use [TLPUI](https://github.com/d4nj1/TLPUI) for easier configuration.

bash

```bash
sudo zypper in tlp
sudo systemctl disable tlp && sudo systemctl stop tlp
sudo systemctl enable tlp
```

In TLPUI, I simply configure the charging thresholds, which can help extend laptop battery life a little:

bash

```ini
START_CHARGE_THRESH_BAT0=75
# start charging threshold
STOP_CHARGE_THRESH_BAT0=85
# stop charging threshold
```

Use the following command to check battery status:

bash

```bash
sudo tlp-stat -b
```

## Terminal Emulator

Use whichever terminal emulator feels comfortable. I used to spend a lot of time tweaking shells, but now I am too lazy to configure them, so the default bash is fine. Installing Oh My Bash can make it look nicer. At the moment, my favorite shell may actually be Eshell inside Emacs.

Also, if I use zsh, I personally dislike Oh My Zsh and would switch to a lighter configuration framework such as [zim](https://github.com/zimfw/zimfw).

For terminal emulators, I have been using [kitty](https://sw.kovidgoyal.net/kitty/conf/). It has good color support and includes kitten plugins. Whenever I set up a new machine, I can use kitten directly to configure themes and other settings. It is also very fast.

My favorite terminal font is [CaskaydiaMono Nerd Font](https://github.com/ryanoasis/nerd-fonts/releases/download/v3.1.1/CascadiaMono.zip).

Also, the newer terminal emulator [Warp](https://www.warp.dev/blog/warp-for-linux) was already available on Linux when I wrote this article. I have always liked using this very modern terminal on macOS.

For terminal emulator configuration, changing the font is usually enough. One more thing: I really dislike transparent terminal backgrounds.

## References

-   [openSUSE Wiki quick configuration guide](https://zh.opensuse.org/SDB:%E5%BF%AB%E9%80%9F%E9%85%8D%E7%BD%AE%E6%8C%87%E5%8D%97)
-   [openSUSE Wiki battery management](https://zh.opensuse.org/SDB:%E7%94%B5%E6%B1%A0%E7%AE%A1%E7%90%86)
-   [v2raya user documentation](https://v2raya.org/docs/prologue/introduction/)

