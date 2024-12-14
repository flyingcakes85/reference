# Lets look inside an Arch Linux package

For the purpose of this demonstration, I'm going to be showing contents of 2 packages:

- first, we see **mdbook**, which is the tool I'm using to build this reference website
  - I've chosen it, beacuse the package for mdbook is very simple
- second is **btop**, a system resource monitor
  - I've chosen this to show what extra items a GUI application usually bundles

## tl;dr

The purpose of this page is to show that a package contains files necessary for the software to work, placed nicely in relevant directories. Along with these files, package has some metadata files, which are required by package manager.

If you can appreciate these two facts, then you may skip this page.

## Look inside: mdbook

### Download the package

I'm downloading **mdbook** version 0.4.43, the latest as of composing this document. Depending on when you're reading, this version could be outdated or even removed from mirrors. You'll always find the latest version here: [https://archlinux.org/packages/extra/x86_64/mdbook/download/](https://archlinux.org/packages/extra/x86_64/mdbook/download/)

```sh
cd /tmp
wget https://london.mirror.pkgbuild.com/extra/os/x86_64/mdbook-0.4.43-1-x86_64.pkg.tar.zst
```

### Extracting the package

```sh
mkdir mdbook
tar -C mdbook -xf mdbook-0.4.43-1-x86_64.pkg.tar.zst
```

At this point, you can see the contents of folder `mdbook`

```sh
cd mdbook
ls -alh mdbook
```

It should give the following output

```
lain@wired /tmp/mdbook
$ ls -alh
total 16K
drwxr-xr-x  3 lain lain  120 Dec 12 15:27 .
drwxrwxrwt 19 root root  560 Dec 12 15:26 ..
-rw-r--r--  1 lain lain 5.3K Nov 26 01:30 .BUILDINFO
-rw-r--r--  1 lain lain  597 Nov 26 01:30 .MTREE
-rw-r--r--  1 lain lain  409 Nov 26 01:30 .PKGINFO
drwxr-xr-x  4 lain lain   80 Nov 26 01:30 usr
```

Those familier with [Filesystem Hierarchy Standard](https://refspecs.linuxfoundation.org/FHS_3.0/fhs/index.html) will already recognize the `usr` folder. We'll come to that later.

### Exploring the package

Lets first explore the `usr` folder.

```
lain@wired /tmp/mdbook
$ tree usr
usr
├── bin
│   └── mdbook
└── share
    ├── bash-completion
    │   └── completions
    │       └── mdbook
    ├── doc
    │   └── mdbook
    │       └── README.md
    ├── fish
    │   └── vendor_completions.d
    │       └── mdbook.fish
    └── zsh
        └── site-functions
            └── _mdbook
```

Normally, you'd find more folders under `/usr` on your system, but since this package needs to place files in only two of them (`bin` and `share`), it has only those two folders.

#### The main binary

Under `usr/bin` we can see the binary, which is our software itself.

```
lain@wired /tmp/mdbook
$ file usr/bin/mdbook
usr/bin/mdbook: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=7f0d0368fa7949be803463b0b426fb8655febfb6, for GNU/Linux 4.4.0, stripped
```

Explaining this output is beyond the scope of this reference. All you need to be convinced of as of now is that the file I just discussed is actually a binary, because `file` reports it to be of ELF format.

#### Documentation

Under `usr/share/doc/mdbook` you see a bundled `README.md` file which has a basic starting point to using `mdbook`.

Documentations need not be markdown files. HTML files as documentation are very common too. Note that these are different from manpages, which are supposed to be stored in `/usr/share/man`.

#### Shell completions

Finally, we're left with 3 folders

- `usr/share/bash-completion`
- `usr/share/zsh`
- `usr/share/fish`

These folders bundle shell completions for `bash`, `zsh` and `fish` respectively. These are not necessary to be bundled, but nice to have nonetheless.

### Metadata files

We see 3 files not discussed yet: `.BUILDINFO`, `.MTREE` and `.PKGINFO`. These are metadata files, which aren't directly required by the software itself, but are needed nonetheless for installing and managing the package.

#### .MTREE

This file contains hashes and timestamps of all files which are in the package. This is used by package manager to verify integrity of package.

Since its a compressed file, you can't directly `cat` it.

```
lain@wired /tmp/mdbook
$ file .MTREE
.MTREE: gzip compressed data, from Unix, original size modulo 2^32 1584
```

`file` reports it to be gzip compressed. Use `zcat` to print out contents.

```
lain@wired /tmp/mdbook
$ zcat .MTREE
#mtree
/set type=file uid=0 gid=0 mode=644
./.BUILDINFO time=1732564809.0 size=5338 sha256digest=39d286ee40b577d2386b97104cb14b0ce01c98a42ae031300017202237f81872
./.PKGINFO time=1732564809.0 size=409 sha256digest=adc6a6de09619057285d9d76cbdb8ee9f3e99fd4e39ce41b0bdd924dbe1eab60
/set mode=755
./usr time=1732564809.0 type=dir
./usr/bin time=1732564809.0 type=dir
./usr/bin/mdbook time=1732564809.0 size=12027696 sha256digest=885ad1dd57886aea81f9d1b3fb0e56a4202a4a82b11481d4ea3259591dc4ac8f
./usr/share time=1732564809.0 type=dir
./usr/share/bash-completion time=1732564809.0 type=dir
./usr/share/bash-completion/completions time=1732564809.0 type=dir
./usr/share/bash-completion/completions/mdbook time=1732564809.0 mode=644 size=12922 sha256digest=4693cccac54ccf2c98f1ba4595a89028454c0222f568b7db1461ccc48ce7f868
./usr/share/doc time=1732564809.0 type=dir
./usr/share/doc/mdbook time=1732564809.0 type=dir
./usr/share/doc/mdbook/README.md time=1732564809.0 mode=644 size=1046 sha256digest=44e4086a85125c978b6c56be6beeb5d5b24c7394c96e281c1c589217eff4ca5f
./usr/share/fish time=1732564809.0 type=dir
./usr/share/fish/vendor_completions.d time=1732564809.0 type=dir
./usr/share/fish/vendor_completions.d/mdbook.fish time=1732564809.0 mode=644 size=7822 sha256digest=19374afc581279c2a31579171096e3fb03615b417431466c829e39a7a8d3b9cb
./usr/share/zsh time=1732564809.0 type=dir
./usr/share/zsh/site-functions time=1732564809.0 type=dir
./usr/share/zsh/site-functions/_mdbook time=1732564809.0 mode=644 size=10795 sha256digest=86db03752e01a5090041d67ff5b9824eaf59b02b93e2eb5bb7e00ef784bac13e
```

Just for example, you can get hash of a file and compare with the value in `.MTREE`.

```
lain@wired /tmp/mdbook
$ sha256sum usr/bin/mdbook
885ad1dd57886aea81f9d1b3fb0e56a4202a4a82b11481d4ea3259591dc4ac8f  usr/bin/mdbook
```

The hash does indeed match with the value in `.MTREE`.

#### .BUILDINFO

This file contains information about the build environment where this package was built. This is necessary for reproducible builds. It contains list of packages that existed in the build environment and specific build flags.

I am intentionally not copying the contents here, since the file is too long.


#### .PKGINFO

This file contains metadata which the package manager needs to install/manage the package.


```
lain@wired /tmp/mdbook
$ cat .PKGINFO
# Generated by makepkg 7.0.0
# using fakeroot version 1.36
pkgname = mdbook
pkgbase = mdbook
xdata = pkgtype=pkg
pkgver = 0.4.43-1
pkgdesc = Create book from markdown files, like Gitbook but implemented in Rust
url = https://github.com/rust-lang/mdBook
builddate = 1732564809
packager = Caleb Maclennan <alerque@archlinux.org>
size = 12060281
arch = x86_64
license = MPL2
depend = gcc-libs
makedepend = cargo
```

You'll notice this includes a key named `depends`. This is where the dependencies are listed and pacman knows which other packages need to be installed prior, in order for the current one to work. In this case, `mdbook` needs `gcc-libs` in order to work.

#### More metadata files

The three metadata files we discussed are compulsory to be included with every package. However, there are two more files which are optionally included.

- `.INSTALL`: this is used to bundle commands which need to be run after install, upgrade or removal of the package
- `.Changelog`: an optional file kept by package maintainer documenting changes of the package

### Libraries

Since `mdbook` is written in Rust, it is *almost* a statically linked executable. All dependencies are statically linked (i.e. part of the binary itself) and the only library it links dynamically is GNU LibC. (and hence the external dependency on `gcc-libs` above) Had it bundled shared libraries, they would have been placed in `usr/lib`.


## Look inside: btop

Similar to the last section, lets get through this quick.

### Download and extract

The latest version of `btop` is always available at https://archlinux.org/packages/extra/x86_64/btop/download/

```sh
cd /tmp
wget https://london.mirror.pkgbuild.com/extra/os/x86_64/btop-1.4.0-4-x86_64.pkg.tar.zst
mkdir btop
tar -C btop -xf btop-1.4.0-4-x86_64.pkg.tar.zst
```

### Exploring the package

The `usr` folder for `btop` looks like this

```
lain@wired /tmp/btop
$ tree usr
usr
├── bin
│   └── btop
└── share
    ├── applications
    │   └── btop.desktop
    ├── btop
    │   ├── README.md
    │   └── themes
    │       ├── adapta.theme
    │       ├── adwaita.theme
    │       ├── ayu.theme
    │       ├── dracula.theme
    │       ├── dusklight.theme
    │       ├── elementarish.theme
    │       ├── everforest-dark-hard.theme
    │       ├── everforest-dark-medium.theme
    │       ├── flat-remix-light.theme
    │       ├── flat-remix.theme
    │       ├── greyscale.theme
    │       ├── gruvbox_dark.theme
    │       ├── gruvbox_dark_v2.theme
    │       ├── gruvbox_light.theme
    │       ├── gruvbox_material_dark.theme
    │       ├── horizon.theme
    │       ├── HotPurpleTrafficLight.theme
    │       ├── kyli0x.theme
    │       ├── matcha-dark-sea.theme
    │       ├── monokai.theme
    │       ├── night-owl.theme
    │       ├── nord.theme
    │       ├── onedark.theme
    │       ├── paper.theme
    │       ├── phoenix-night.theme
    │       ├── solarized_dark.theme
    │       ├── solarized_light.theme
    │       ├── tokyo-night.theme
    │       ├── tokyo-storm.theme
    │       ├── tomorrow-night.theme
    │       └── whiteout.theme
    ├── icons
    │   └── hicolor
    │       ├── 48x48
    │       │   └── apps
    │       │       └── btop.png
    │       └── scalable
    │           └── apps
    │               └── btop.svg
    └── man
        └── man1
            └── btop.1.gz
```

#### Binary

The binary is again located inside `usr/bin`.

```
lain@wired /tmp/btop
$ file usr/bin/btop
usr/bin/btop: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=112225f945ec5d5c28f69c1de22117f957a926f9, for GNU/Linux 4.4.0, stripped
```

We can run `ldd` to check which all libraries it is likned to.

```
lain@wired /tmp/btop
$ ldd usr/bin/btop
	linux-vdso.so.1 (0x00007ffd9e7fd000)
	libstdc++.so.6 => /usr/lib/libstdc++.so.6 (0x000070b648c00000)
	libm.so.6 => /usr/lib/libm.so.6 (0x000070b648b11000)
	libgcc_s.so.1 => /usr/lib/libgcc_s.so.1 (0x000070b648ae3000)
	libc.so.6 => /usr/lib/libc.so.6 (0x000070b6488f2000)
	/lib64/ld-linux-x86-64.so.2 => /usr/lib64/ld-linux-x86-64.so.2 (0x000070b649026000)
```

#### Manpage

Manpage for `btop` is in the Troff format. You can read the bundled manpage file using the `man` command.

```sh
man usr/share/man/man1/btop.1.gz
```


### Desktop file and icons

You'll find a desktop file at `usr/share/applications/btop.desktop` which contains the desktop entry.

```
lain@wired /tmp/btop
$ cat usr/share/applications/btop.desktop
[Desktop Entry]
Type=Application
Version=1.0
Name=btop++
GenericName=System Monitor
GenericName[it]=Monitor di sistema
Comment=Resource monitor that shows usage and stats for processor, memory, disks, network and processes
Comment[it]=Monitoraggio delle risorse: mostra utilizzo e statistiche per CPU, dischi, rete e processi
Icon=btop
Exec=btop
Terminal=true
Categories=System;Monitor;ConsoleOnly;
Keywords=system;process;task
```

The specific icon mentioned here is then searched for inside `/usr/share/icons`. The relevant icons are bundled in package at `usr/share/icons/hicolor/48x48/apps/btop.png` and `usr/share/icons/hicolor/scalable/apps/btop.svg`.

### Btop specific themes

Finally, `btop` also supports loading different theme definitions. The default set of bundled themes is placed inside a dedicated folder at `usr/share/btop/themes`.