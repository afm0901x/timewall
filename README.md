<div align="center">

# timewall

[![CI](https://github.com/bcyran/timewall/actions/workflows/test.yml/badge.svg)](https://github.com/bcyran/timewall/actions/workflows/test.yml)
[![license](https://img.shields.io/github/license/bcyran/timewall)](https://github.com/bcyran/timewall/blob/master/LICENSE)

Apple dynamic HEIF wallpapers on GNU/Linux.

![timewall_preview](https://user-images.githubusercontent.com/8322846/197593208-0e0b7901-2caf-43b0-b7fd-847c2cb49ff1.gif)

</div>

---

Features:
- Support for original HEIF/HEIC dynamic wallpaper files used in MacOS.
- Support for all schedule types: sun position based, time based, dark/light mode.
- Set wallpaper once and continuously (daemon mode).
- Preview wallpaper changes.
- Display wallpaper metadata.
- Extract all images and metadata as XML.

---

## Usage
### Initial configuration
If you intend to use sun position based wallpapers, you need to provide `timewall` with your approximate location.
To do this, create a config file `$XDG_CONFIG_HOME/timewall/config.toml` (probably `~/.config/timewall/config.toml` if you're not sure).
This file will be also written when you first run `timewall set`.

Put the following contents in the file, while changing `lat` (latitude) and `lon` (longitude) values to your needs:
```toml
[location]
lat = 51.11
lon = 17.02
```

### Setting the wallpaper
#### One-time mode
To set the wallpaper just run:
```bash
timewall set path/to/wallpaper.heif
```
This will set your wallpaper to the correct image, taking into account current time or sun position, depending on the wallpaper schedule.
Note that wallpaper set like this will not update with time.
You can update it by repeating the command above, you can also shorten it to just `timewall set` - last used wallpaper is remembered.

#### Daemon mode
You probably don't want to update the wallpaper manually every time.
To do this automatically you can use the daemon mode:
```bash
timewall set --daemon
```
This command will run continuously and update your wallpaper as time passes.
It's a good idea to run it automatically at startup as a background process.

As you can see, the command above doesn't include the wallpaper to set.
This is because the daemon mode by default uses the last set wallpaper.
If you already ran `timewall set` manually, then daemon will use whatever wallpaper you set then.
Moreover, if you ever want to change your wallpaper, it's enough to run `timewall set path/to/new/wall.heif`.
The daemon will pick up the change and update the new wallpaper from now on.

#### Systemd service
One way to achieve this is using `systemd` service.
Write the following contents to `~/.config/systemd/user/timewall.service`:
```systemd
[Unit]
Description=Dynamic wallpapers daemon

[Service]
Type=simple
ExecStart=timewall set --daemon

[Install]
WantedBy=default.target
```

And run:
```bash
systemctl --user enable --now timewall.service
```
After this `timewall` should start automatically on boot and update your wallpaper during the day.

### Previewing
To preview the wallpaper, run:
```bash
timewall preview path/to/wallpaper.heif
```
This will quickly cycle all images in the wallpaper to simulate changes throughout the day.
Preview speed can be controlled by specifying the delay in milliseconds between consecutive wallpaper changes using the `--delay` option.
You can also infinitely loop the preview using `--repeat` option.

### Unpacking
To unpack all images stored in the wallpaper, as well as its metadata in XML format, run:
```bash
timewall unpack path/to/wallpaper.heif path/to/output/directory
```

### Reading metadata
All metadata known to `timewall` can be displayed using:
```bash
timewall info path/to/wallpaper.heif
```

## Resources / credits
The following resources helped me in `timewall` development:
- https://itnext.io/macos-mojave-dynamic-wallpaper-fd26b0698223
- https://itnext.io/macos-mojave-dynamic-wallpapers-ii-f8b1e55c82f
- https://itnext.io/macos-mojave-wallpaper-iii-c747c30935c4
- https://github.com/mczachurski/wallpapper
- https://git.spacesnek.rocks/johannes/heic-to-gnome-xml-wallpaper
