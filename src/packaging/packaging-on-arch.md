# Packaging on Arch Linux

The official wiki page (highly recommended to read) can be found here: [Creating packages - Arch Wiki](https://wiki.archlinux.org/title/Creating_packages). My page gives a brief overview of the process.

## Package build process

Packages on Arch Linux are built using `makepkg` tool. It takes a `PKGBUILD` as input and follows it to output a package archive.

The `PKGBUILD` is a recipe which lists ingredients (sources) and process (build steps) to build a package.

`makepkg` looks at the sources and downloads them. It then computes hashes of downloaded sources with the checksums provided in `PKGBUILD`. Once the verification is complete, sources are extracted and built using the recipe provided. The said recipe must be executable in `bash`. Finally, upon successful build, the build outputs are archived along with the metadata into a package. This package is what the package manager (`pacman`) later extracts in order to "install" the software.


## PKGBUILD

Arch Linux provides a starting point with bundled protoype for a PKGBUILD.

```
lain@wired ~
cat /usr/share/pacman/PKGBUILD.proto
# This is an example PKGBUILD file. Use this as a start to creating your own,
# and remove these comments. For more information, see 'man PKGBUILD'.
# NOTE: Please fill out the license field for your package! If it is unknown,
# then please put 'unknown'.

# Maintainer: Your Name <youremail@domain.com>
pkgname=NAME
pkgver=VERSION
pkgrel=1
epoch=
pkgdesc=""
arch=()
url=""
license=('GPL')
groups=()
depends=()
makedepends=()
checkdepends=()
optdepends=()
provides=()
conflicts=()
replaces=()
backup=()
options=()
install=
changelog=
source=("$pkgname-$pkgver.tar.gz"
        "$pkgname-$pkgver.patch")
noextract=()
sha256sums=()
validpgpkeys=()

prepare() {
	cd "$pkgname-$pkgver"
	patch -p1 -i "$srcdir/$pkgname-$pkgver.patch"
}

build() {
	cd "$pkgname-$pkgver"
	./configure --prefix=/usr
	make
}

check() {
	cd "$pkgname-$pkgver"
	make -k check
}

package() {
	cd "$pkgname-$pkgver"
	make DESTDIR="$pkgdir/" install
}
```

There are a lot of parameters, although, its not necessary to fill in all of them. You would fill in the file with relevant values. Refer an existing PKGBUILD (for example, [`maptool-bin`](https://aur.archlinux.org/cgit/aur.git/tree/PKGBUILD?h=maptool-bin)) to see what values should go in.

## PKGBUILD functions

### `prepare()`

Steps to prepare sources for building should go here. Normally, this normally includes patches and distribution specific changes.

### `pkgver()`

This step fetches package version. While package version is usually defined in the variable outside, the version for VCS sources needs to be fetched via shell commands that go in this function.

### `build()`

Here comes the actual build step. A very simple `build()` function can look like this:

```sh
build() {
    ./configure --prefix=/usr
    make DESTDIR="$pkgdir/" install
}
```

This is source specific and packager needs to know if there are any build systems needed and what pre build steps need to be ensured. For example, build dependencies must be fetched before building.


### `check()`

The test suite invocation goes here. Classically, this could be as simple as:

```sh
check() {
    make check
}
```

However, modern test suites are more sophisticated and this function needs to be adapted accordingly.


### `package()`

This is the final step, which is also compulsory for all packages to include. Here, the built files are copied into `$pkgdir` to ensure they're copied into the final package archive.


## Sample PKGBUILD

Lets build a simple PKGBUILD for a hello world application. The source for our application is going to be a GitHub repository, sepcifically [memsharded/hello](https://github.com/memsharded/hello).

Filling up the PKGBUILD variables, it should look like this:


```bash
pkgname=hello-git
_pkgname=hello
pkgver=0.0.1
pkgrel=1
pkgdesc='A hello world application'
url='https://github.com/memsharded/hello'
source=("git+https://github.com/memsharded/hello")
arch=('x86_64')
licence=('MIT')
makedepends=('cmake')
sha256sums=(SKIP)
```

We have skipped checksum, because when we're using git as the source, git is doing its own checksum and ensuring integrity.

This application doesn't need any pre-build steps, so we can skip that. The application has a git source, so we do need `pkgver()` function to generate a version. In our case, we will use the short commit hash as the version.

```bash
pkgver() {
    cd "$srcdir/$_pkgname"
    git log --oneline | head -n 1 | cut -d' ' -f1
}
```

Lets move to writing the `build()` function.

The source has provided `CMakeLists.txt` file, which is a clear indication that its using [CMake](https://cmake.org/). So, lets put the relevant CMake build step in here:


```bash
build() {
    cmake -DCMAKE_INSTALL_PREFIX:PATH=/usr hello
    make
}
```

By now, we've built the binary from source. Finally, we must copy over binary to `$pkgdir` so that it ends up in package archive. This is done inside `package()` function.

```bash
package() {
    install -Dm755 "$srcdir/hello/bin/greet" "$pkgdir/usr/bin/greet"
}
```

The final `PKGBUILD` should look like this:

```bash
pkgname=hello-git
_pkgname=hello
pkgver=a707ee9
pkgrel=1
pkgdesc='A hello world application'
url='https://github.com/memsharded/hello'
source=("git+https://github.com/memsharded/hello")
arch=('x86_64')
licence=('MIT')
makedepends=('cmake')
sha256sums=(SKIP)

pkgver() {
    cd "$srcdir/$_pkgname"
    git log --oneline | head -n 1 | cut -d' ' -f1
}

build() {
    cmake -DCMAKE_INSTALL_PREFIX:PATH=/usr hello
    make
}

package() {
    install -Dm755 "$srcdir/hello/bin/greet" "$pkgdir/usr/bin/greet"
}
```

## Building from PKGBUILD

As mentioned before, the tool to build from `PKGBUILD` is `makepkg`.

Lets build the `PKGBUILD`. Change into the directory which has `PKGBUILD` and run:

```sh
makepkg -s
```

This will fetch source, build and package it. Sample output is shown below

```
==> Making package: hello-git 0.0.1-1 (Sat 14 Dec 2024 04:01:21 PM IST)
==> Checking runtime dependencies...
==> Checking buildtime dependencies...
==> Retrieving sources...
  -> Updating hello git repo...
==> Validating source files with sha256sums...
    hello ... Skipped
==> Extracting sources...
  -> Creating working copy of hello git repo...
Reset branch 'makepkg'
==> Starting pkgver()...
==> Updated version: hello-git a707ee9-1
==> Removing existing $pkgdir/ directory...
==> Starting build()...
CMake Warning (dev) at CMakeLists.txt:1 (PROJECT):
  cmake_minimum_required() should be called prior to this top-level project()
  call.  Please see the cmake-commands(7) manual for usage documentation of
  both commands.
This warning is for project developers.  Use -Wno-dev to suppress it.

CMake Deprecation Warning at CMakeLists.txt:2 (cmake_minimum_required):
  Compatibility with CMake < 3.10 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value.  Or, use the <min>...<max> syntax
  to tell CMake that the project requires at least <min> but has been updated
  to work with policies introduced by <max> or earlier.


-- Configuring done (0.0s)
-- Generating done (0.0s)
-- Build files have been written to: /mnt/hd1/home/lain/.cache/aur/hello-git/src
[ 50%] Built target hello
[100%] Built target greet
==> Entering fakeroot environment...
==> Starting package()...
==> Tidying install...
  -> Removing libtool files...
  -> Purging unwanted files...
  -> Removing static library files...
  -> Stripping unneeded symbols from binaries and libraries...
  -> Compressing man and info pages...
==> Checking for packaging issues...
==> Creating package "hello-git"...
  -> Generating .PKGINFO file...
  -> Generating .BUILDINFO file...
  -> Generating .MTREE file...
  -> Compressing package...
==> Leaving fakeroot environment.
==> Finished making: hello-git a707ee9-1 (Sat 14 Dec 2024 04:01:23 PM IST)
```

Your working directory should have a file named `hello-git-a707ee9-1-x86_64.pkg.tar`. This is the built package. Note that the package name contains version, which in this case is the latest commit hash. Hence, this might be different when you build the package.

A summary of useful flags for makepkg:

- `-c, --clean`: clean up leftover files after successful build
- `-e, --noextract`: this will force `makepkg` to not extract source files or run `prepare()`. Useful when manually making changes in `$srcdir` to test.
- `-f, --force`: rebuild the package even if its already built
- `--skipchecksums`: do not verify checksum of source files
- `-i, --install`: install package after successful build
- `-s, --sycdeps`: install missing build/run dependencies

## Installing and running the built package

Installing the built package is very straightforward.

```sh
sudo pacman -U hello-git-a707ee9-1-x86_64.pkg.tar
```

After installation, we can run the application and query `pacman` (the package manager) for details.

```
lain@wired /tmp/build
$ greet
Hello World!
lain@wired /tmp/build
$ pacman -Qo /usr/bin/greet
/usr/bin/greet is owned by hello-git a707ee9-1
```

You can even ask `pacman` to uninstall the package.

```
lain@wired /tmp/build
$ sudo pacman -R hello-git
checking dependencies...

Package (1)  Old Version  Net Change

hello-git    a707ee9-1     -0.01 MiB

Total Removed Size:  0.01 MiB

:: Do you want to remove these packages? [Y/n] y
:: Processing package changes...
(1/1) removing hello-git                           [------------------------] 100%
:: Running post-transaction hooks...
(1/1) Arming ConditionNeedsUpdate...
```

## Extracting our built package

As a final exercise, we shall do what we did in the last page: extracting a package and observing its contents.

```sh
mkdir hello
tar -C hello -xf hello-git-a707ee9-1-x86_64.pkg.tar
cd hello
```

Lets see the directory tree

```
lain@wired /tmp/build/hello
$ tree -a
.
├── .BUILDINFO
├── .MTREE
├── .PKGINFO
└── usr
    └── bin
        └── greet
```

We can see the three metadata files, along with the one binary that we built from source.

Since we have lot less files in this package, compared to `mdbook`, contents of `.MTREE` are lesser too.

```
lain@wired /tmp/build/hello
$ zcat .MTREE
#mtree
/set type=file uid=0 gid=0 mode=644
./.BUILDINFO time=1734184494.0 size=55438 sha256digest=bfc13d6c81cf88b6033753fc080fddbfb02fedd3047b0a325fdb88b84c21ddaa
./.PKGINFO time=1734184494.0 size=313 sha256digest=720c516ff2c14fa965a6b1ed893507622181ad87ba1a011b3ffa5d6e12d70517
./usr time=1734184494.0 mode=755 type=dir
./usr/bin time=1734184494.0 mode=755 type=dir
./usr/bin/greet time=1734184494.0 mode=755 size=15480 sha256digest=0348f619559c5913f255159583c733aaf0799cf27b8d6130e8ee41c11ba8191e
```

## Further resources


- [Creating packages - Arch Wiki](https://wiki.archlinux.org/title/Creating_packages)
- [Arch package guidelines](https://wiki.archlinux.org/title/Arch_package_guidelines)
  - on the top of this page, you'll also find links to language specific guideline pages
- [Arch package guidelines/Security](https://wiki.archlinux.org/title/Arch_package_guidelines/Security)