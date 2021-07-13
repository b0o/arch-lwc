# arch-lwc [![version](https://img.shields.io/github/v/tag/b0o/arch-lwc?style=flat&color=yellow&label=version&sort=semver)](https://github.com/b0o/arch-lwc/releases) [![License: MIT](https://img.shields.io/github/license/b0o/arch-lwc?style=flat&color=green)](https://opensource.org/licenses/MIT)

Create and run lightweight Arch Linux containers with pacstrap and systemd.

For example, to run a fresh containerized instance of Firefox Developer Edition
from a Wayland host and pass through the GUI:

```sh
sudo arch-lwc run \
  -p firefox-developer-edition -u container-user \
  -b /dev/shm -b /dev/dri -b /dev/snd -b "$XDG_RUNTIME_DIR/$WAYLAND_DISPLAY:/tmp/run/wayland-1" \
  -c 'export WAYLAND_DISPLAY=wayland-1 MOZ_ENABLE_WAYLAND=1
      ln -s /tmp/run/$WAYLAND_DISPLAY $XDG_RUNTIME_DIR/$WAYLAND_DISPLAY
      firefox-developer-edition' \
  ./firefox-container
```

## Usage

<!-- USAGE -->

```

Usage: arch-lwc [OPT...] COMMAND

Create, manage, and run lightweight Arch Linux containers with pacstrap and systemd.

Options
  -h [COMMAND]    Display usage information.
  -v              Display arch-lwc version and copyright information.
  -q              Output debug messages.

COMMANDs
  create   Create a new container.
  exec     Execute a command in a running container.
  help     Display usage information.
  run      Create and start a container.
  start    Start an existing container.
  status   Display container status.
  stop     Stop a running container.
  version  Display arch-lwc version and copyright information.

COMMAND create
  Usage: arch-lwc create [OPT...] CONTAINER
  
  Create a new container at the path CONTAINER.
  Options
    -p PKG          Append package to list of packages to be installed. May be
                    specified more than once. (default: base pacman-contrib)
  
    -P PKG          Like -p, but override default packages.
  
    -u USER         Name of target user inside the container. If the user doesn't
                    exist, it will created. to create inside container. Any
                    commands specified with -x or -c will run as this user from
                    the user's home directory. If not specified, the
                    root user will be used.
  
    -g GRP[,GRP...] Add the user to these groups in addition to the default
                    groups. (default: tty,wheel,video,input)
  
    -G GRP[,GRP...] Like -g, but override default groups. Use -G "" to prevent
                    adding the user to any groups.
  
    -T              Don't disable securetty (this will likely prevent arch-lwc from
                    running the container and prevent you from obtaining a shell
                    in the container).
  
    -f SRC[:DST]    Copy the file SRC from the host machine to DST in the container.
                    If DST is omitted, it will be inferred as SRC's absolute path.
  
    -n NSPAWNARG    When running the container, pass NSPAWNARG as a command-line
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
  
    -x CMD          Run CMD inside the container as the target user after
                    preliminary setup is complete. CMD will be interpreted by bash
                    and can contain multiple statements separated by semicolons
                    and/or newlines.
  
                    Modifications to the container will persist, making -x useful
                    for doing any extra setup needed.
  
                    Note that CMD only runs during the container creation phase, not
                    when the container is run. For that, see -c.
  
    -X CMD          Like -x, but run CMD as the root user. -X CMDs are run before
                    -x CMDs.
  
    -c CMD          Run CMD inside the container when the container is run. CMD
                    will be interpreted by bash and can contain multiple
                    statements separated by semicolons and/or newlines.
  
                    Note that CMD will not run during the creation phase, only when
                    the container is run.
  
                    If specified multiple times, CMDs are run synchronously in
                    the order they are specified. All -c CMDs set with the create
                    command will be run before -c CMDs set with the start command.
  
    -C CMD          Like -c, but run CMD as the root user root. -C CMDs are run before
                    -c CMDs.
  

COMMAND exec
  Usage: arch-lwc exec [OPT...] CONTAINER [CMD] [ARG...]
  
  Run a command in a running container. If no CMD is specified, an interactive
  shell is launched.
  
  Options
  
    -u USER         Name of target user inside the container. Any commands
                    specified with -c will run as this user from the user's home
                    directory. If not specified, the user that the container was
                    created with or the root user will be used.

COMMAND run
  Usage: arch-lwc run [OPT...] CONTAINER
  
  Create and run a new container at the path CONTAINER.
  Options
    -p PKG          Append package to list of packages to be installed. May be
                    specified more than once. (default: base pacman-contrib)
  
    -P PKG          Like -p, but override default packages.
  
    -u USER         Name of target user inside the container. If the user doesn't
                    exist, it will created. to create inside container. Any
                    commands specified with -x or -c will run as this user from
                    the user's home directory. If not specified, the
                    root user will be used.
  
    -g GRP[,GRP...] Add the user to these groups in addition to the default
                    groups. (default: tty,wheel,video,input)
  
    -G GRP[,GRP...] Like -g, but override default groups. Use -G "" to prevent
                    adding the user to any groups.
  
    -T              Don't disable securetty (this will likely prevent arch-lwc from
                    running the container and prevent you from obtaining a shell
                    in the container).
  
    -f SRC[:DST]    Copy the file SRC from the host machine to DST in the container.
                    If DST is omitted, it will be inferred as SRC's absolute path.
  
    -n NSPAWNARG    When running the container, pass NSPAWNARG as a command-line
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
  
    -x CMD          Run CMD inside the container as the target user after
                    preliminary setup is complete. CMD will be interpreted by bash
                    and can contain multiple statements separated by semicolons
                    and/or newlines.
  
                    Modifications to the container will persist, making -x useful
                    for doing any extra setup needed.
  
                    Note that CMD only runs during the container creation phase, not
                    when the container is run. For that, see -c.
  
    -X CMD          Like -x, but run CMD as the root user. -X CMDs are run before
                    -x CMDs.
  
    -c CMD          Run CMD inside the container when the container is run. CMD
                    will be interpreted by bash and can contain multiple
                    statements separated by semicolons and/or newlines.
  
                    Note that CMD will not run during the creation phase, only when
                    the container is run.
  
                    If specified multiple times, CMDs are run synchronously in
                    the order they are specified. All -c CMDs set with the create
                    command will be run before -c CMDs set with the start command.
  
                    If CMD is "-", any -c CMDs specified when the container was
                    created are overridden.
  
    -C CMD          Like -c, but run CMD as the root user root. -C CMDs are run before
                    -c CMDs.
  

COMMAND start
  Usage: arch-lwc start [OPT...] CONTAINER
  
  Start an existing container at the path CONTAINER.
  
  Options
  
    -u USER         Name of target user inside the container. Any commands
                    specified with -c will run as this user from the user's home
                    directory. If not specified, the user that the container was
                    created with or the root user will be used.
  
    -n NSPAWNARG    Pass NSPAWNARG as a command-line argument to systemd-nspawn.
                    May be specified more than once.  Arguments with values, such
                    as for systemd-nspawn's -E / --setenv option, should be passed
                    either as a single -n with no spaces (e.g. -n "--setenv=VAR=VAL")
                    or as two sequential -n arguments (e.g. -n "-E" -n "VAR=VAL").
  
                    See systemd-nspawn(1) for more information on systemd-nspawn
                    arguments. Note that certain systemd-nspawn arguments may conflict
                    with the behavior of arch-lwc, such as --boot.
  
                    For example, -n "--bind=/etc".
  
                    For convenience, the following systemd-nspawn options have arch-lwc aliases:
                      arch-lwc opt    systemd-nspawn opt
                      -b ARG          --bind=ARG
                      -B ARG          --bind-ro=ARG
                      -e ARG          --setenv=ARG
  
    -N NSPAWNARG    Like -n, but override any NSPAWNARGs specified during container creation.
  
    -c CMD          Run CMD inside the container. CMD will be interpreted by bash
                    and can contain multiple statements separated by semicolons
                    and/or newlines.
  
                    All -c CMDs that were specified during container creation will
                    be run before -c CMDs specified with the start command.
  
                    If specified multiple times, CMDs are run synchronously in
                    the order they are specified. All -c CMDs set with the create
                    command will be run before -c CMDs set with the start command.
  
                    If CMD is "-", any -c CMDs specified when the container was
                    created are overridden.
  
    -C CMD          Like -c, but run CMD as the root user root. -C CMDs are run before
                    -c CMDs.

COMMAND status
  Usage: arch-lwc status CONTAINER
  
  Display container status.

COMMAND stop
  Usage: arch-lwc stop CONTAINER
  
  Stop a running container.

COMMAND version
  arch-lwc version 0.0.4
  Repository: https://github.com/b0o/arch-lwc
  Issues:     https://github.com/b0o/arch-lwc/issues
  License:    MIT (https://mit-license.org)
  Copyright:  2021 Maddison Hellstrom <github.com/b0o>

```

<!-- /USAGE -->

## TODO

- [ ] Installation instructions
- [ ] AUR Package
- [x] Separate create and run functionality

## License

<!-- LICENSE -->

&copy; 2021 Maddison Hellstrom

Released under the MIT License.

<!-- /LICENSE -->
