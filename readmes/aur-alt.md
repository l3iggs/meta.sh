# aur.sh(1) - alternate version

make packages from the [Arch User Repository](https://aur.archlinux.org/)
via the mirror on pkgbuild.com

This is the alternate version of the main aur.sh, that gets package files from
the pkgbuild.com AUR mirror using `wget` instead of getting package files
directly from the AUR using `curl` and `tar`. If the main Arch User Repository,
or `curl` or `tar`, is having issues, this alternate version is called for:
otherwise, the mainstream version at meta.sh/aur is probably a better choice.

## USAGE

aur.sh will download every package from the AUR specified as a parameter,
and will run makepkg on it with any hyphen-prefixed options. Package files will
be downloaded to `$BUILDDIR`, or the working directory if `$BUILDDIR` has no
value.

### Setting your build directory

If you do not want packages to download to your current directory,
you can source `/etc/makepkg.conf` to set any configured `BUILDDIR`:

    $ source /etc/makepkg.conf

and/or change to a temporary directory first:

    $ cd `mktemp -d`

### Prerequisites

Before installing any packages, you should always ensure that your installation
is up to date:

    # pacman -Syu

Also, to build packages from the AUR, you are expected to have the `base-devel`
package installed. If you aren't sure that you've done so already:

    # pacman -S --needed base-devel

### Installing packages from the AUR

To install any number of AUR packages and their main repo dependencies, run
aur.sh (the site root can be directed straight to bash via curl) with the `-s`
(install package dependencies) and `-i` (install package after building)
options for makepkg:

    $ bash <(curl meta.sh/aur-alt) -si [package ...]

## ADVANCED TRICKS

### Updating AUR packages

To reinstall the latest version of any installed AUR packages, you can pipe the
results of a query of non-repo package names to aur.sh, which will then
redownload them, build them, and install any builds newer than what you
currently have:

    $ pacman -Qqm | xargs bash <(curl meta.sh/aur-alt) -si --needed

## CAVEATS

The `-s` option to makepkg will not download any package dependencies that come
from the AUR, so you will need to make any AUR dependencies you don't already
have installed before you try to install their dependants. For example, the
`yaourt` package in the AUR depends on the AUR package `package-query`, so to
install `yaourt` with aur.sh, you need to specify `package-query` first:

    $ bash <(curl meta.sh/aur-alt) -si package-query yaourt
