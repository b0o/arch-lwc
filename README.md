# arch-lwc [![version](https://img.shields.io/github/v/tag/b0o/arch-lwc?style=flat&color=yellow&label=version&sort=semver)](https://github.com/b0o/arch-lwc/releases) [![License: MIT](https://img.shields.io/github/license/b0o/arch-lwc?style=flat&color=green)](https://opensource.org/licenses/MIT)

Create and run lightweight Arch Linux containers with pacstrap and systemd.

For example, to run a fresh instance of Firefox Developer Edition in an
ephemeral container from a Wayland host and pass through the GUI:

```sh
sudo arch-lwc -tu container-user -p firefox-developer-edition \
  -b /dev/shm -b /dev/dri -b /dev/snd -b "$XDG_RUNTIME_DIR/$WAYLAND_DISPLAY:/var/run/user/container-user/$WAYLAND_DISPLAY" \
  -e XDG_RUNTIME_DIR=/var/run/user/container-user -e "WAYLAND_DISPLAY=$WAYLAND_DISPLAY" -e MOZ_ENABLE_WAYLAND=1 \
  -x firefox-developer-edition ./firefox-container
```

## Usage

<!-- USAGE -->

```

Usage: arch-lwc [opts] CONTAINER

Create and run lightweight Arch Linux containers with pacstrap and systemd.

Options
  General
  -h        show help
  -v        show version
  -i        enable interactive mode

  Container Creation
  -p PKG         Append package to list of packages to be installed. May be
                 specified more than once. (default: base pacman-contrib)

  -P PKG         Like -p, but override default packages.

  -u USER        Name of target user inside the container. If the user doesn't
                 exist, it will created. to create inside container. Any
                 commands specified with -x will run as this user from the
                 user's home directory. If not specified, the root user will be
                 used. (default: root)

  -g GRP[,GRP..] Add the user to these groups in addition to the default
                 groups. (default: tty,wheel,video,input)

  -G GRP[,GRP..] Like -g, but override default groups. Use -G "" to prevent
                 adding the user to any groups.

  -T             Don't disable securetty (this will likely prevent arch-lwc from
                 running the container and prevent you from obtaining a shell
                 in the container).

  -f SRC[:DST]   Copy the file SRC from the host machine to DST in the container.
                 If DST is omitted, it will be inferred as SRC's absolute path.

  -t             Create the container inside an ephemeral tmpfs device.
                 The device will be unmounted (and the data lost) after arch-lwc
                 exits.
                 (warning: not guaranteed to leave no trace)

  -r             Like -t, but use a ramfs device.
                 (warning: data size may exceed available RAM and cause system
                 instability)
                 (warning: not guaranteed to leave no trace)

  Container Runtime
  -n NSPAWNARG   When running the container, pass NSPAWNARG as a command-line
                 argument to systemd-nspawn. May be specified more than once.
                 Arguments with values, such as for systemd-nspawn's -E /
                 --setenv option, should be passed either as a single -n with
                 no spaces (e.g. -n "--setenv=VAR=VAL") or as two sequential -n
                 arguments (e.g. -n "-E" -n "VAR=VAL").

                 See systemd-nspawn(1) for more information on systemd-nspawn
                 arguments. Note that certain systemd-nspawn arguments may conflict
                 with the behavior of arch-lwc, such as --boot.

                 For example, -n "--bind=/etc".

                 For convenience, the following systemd-nspawn options have arch-lwc aliases:
                   arch-lwc opt    systemd-nspawn opt
                   -b ARG          --bind=ARG
                   -B ARG          --bind-ro=ARG
                   -e ARG          --setenv=ARG

  -x CMD         Run CMD inside the container after setup is complete. CMD will
                 be interpreted by bash and can contain multiple statements
                 separated by semicolons and/or newlines.

About
  Version:    0.0.2
  Repository: https://github.com/b0o/arch-lwc
  Issues:     https://github.com/b0o/arch-lwc/issues
  License:    MIT (https://mit-license.org)
  Copyright:  2021 Maddison Hellstrom <github.com/b0o>

```

<!-- /USAGE -->

## TODO

- [ ] Installation instructions
- [ ] AUR Package
- [ ] Separate create and run functionality

## License

<!-- LICENSE -->

&copy; 2021 Maddison Hellstrom

Released under the MIT License.

<!-- /LICENSE -->
