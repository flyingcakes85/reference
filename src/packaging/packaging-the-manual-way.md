# Packaging the manual way

We just saw `makepkg` do its magic. But, can you do all that _manually_? What exactly is `makepkg` doing behind the scenes?

Come to think of it, you're already specifying the build steps. The extra steps by `makepkg` are

- generating metadata files
  - `.MTREE`
  - `.PKGINFO`
  - `.BUILDENV`
- creating an archive from all the files

Lets do this manually!

## Our application binary

We'll use a very simple hello world program in C language.

```c
#include<stdio.h>

int main(){
    printf("hello world\n");
    return 0;
}
```

Save it as, let's say, `hello.c`. Compile it.

```sh
gcc -O2 hello.c -o hello
chmod +x hello
```

This gives you a `hello` binary which prints `hello world`.

Create a directory `pkg` where we shall copy files that eventually will go into our final package. Inside it, create `usr/bin` directory where our binary shall reside.

```sh
mkdir pkg
cd pkg
mkdir -p usr/bin
cp ../hello ./usr/bin/hello
```

At this point of time, directory tree for `pkg` should look like this:

```
.
└── usr
    └── bin
        └── hello
```

## Writing `.PKGINFO`

This is rougly what `.PKGINFO` should look like. Save it to the file.

```sh
pkgname = hello-c
pkgbase = hello-c
xdata = pkgtype=pkg
pkgver = 0.1.1-1
pkgdesc = home cooked hello world in c
url = https://github.com/flyingcakes/hello-world-c
builddate = 1735307439
packager = flyingcakes
size = 14424
arch = x86_64
makedepend = gcc
```

- `pkgname` and `pkgbase` can be set to your package name
- `pkgver` is of the format `<version>-<revision>`
- `url` could be anything
- `builddate` is the unix timestamp when package was built
  - run `date +%s` to find present timestamp and copy paste that
- to get the size (in bytes), simply run `ls -l usr/bin/hello`

## Package it all together!

In `pkg` directory, run `tree -a` and ensure the hierarchy is same as following.

```
.
├── .PKGINFO
└── usr
    └── bin
        └── hello
```

If you're followed correctly till here, use `tar` to make an archive out of the files.

```sh
tar -cjvf hello.tar $(ls -A)
```

This finally results in `hello.tar` package. This can now be installed using `pacman`.

```
$ sudo pacman -U hello.tar
loading packages...
resolving dependencies...
looking for conflicting packages...

Package (1)  New Version  Net Change

hello-c      0.1.1-1        0.01 MiB

Total Installed Size:  0.01 MiB

:: Proceed with installation? [Y/n] y
(1/1) checking keys in keyring                     [------------------------] 100%
(1/1) checking package integrity                   [------------------------] 100%
(1/1) loading package files                        [------------------------] 100%
(1/1) checking for file conflicts                  [------------------------] 100%
(1/1) checking available disk space                [------------------------] 100%
:: Processing package changes...
(1/1) installing hello-c                           [------------------------] 100%
:: Running post-transaction hooks...
(1/1) Arming ConditionNeedsUpdate...
```

This can now be run.

```
$ hello
hello world
```

You can verify the binary being called is indeed from the package we installed.

```
$ which hello
/usr/bin/hello

$ pacman -Qo /usr/bin/hello
/usr/bin/hello is owned by hello-c 0.1.1-1
```

## Generating `.BUILDINFO`

Notice how we didn't have this file already. As per the Arch wiki, this is used only for reproducible builds and pacman won't check this file during installation itself. Thats why, we could install our `hello-c` package without `.BUILDINFO` file. But for the sake of completeness of this tutorial, lets see how this is generated.

First is some metadata, which we can manually write down.

```sh
format = 2
pkgname = hello-git
pkgbase = hello-git
pkgver = 2
pkgarch = x86_64
pkgbuild_sha256sum = eeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeee
packager = flyingcakes
builddate = 1735304913
builddir = /tmp/c/pkg
startdir = /tmp/c
buildtool = makepkg
buildtoolver = 7.0.0
```

`pkgbuild_sha256sum` should have been set to an actual value. But since we aren't using any `PKGBUILD` at all, I've set it to an obviously fake string.

Next comes `buildenv`. This is defined in `/etc/makepkg.conf`. You can get these by grepping and then format printing.

```sh
eval "$(grep '^BUILDENV' /etc/makepkg.conf)"
printf "buildenv = %s\n" "${BUILDENV[@]}"
```

This should print the following.

```sh
buildenv = !distcc
buildenv = color
buildenv = !ccache
buildenv = check
buildenv = !sign
```

You can redirect it to your existing `.BUILDINFO` file.

```sh
printf "buildenv = %s\n" "${BUILDENV[@]}" >> .BUILDINFO
```

`OPTIONS` too are defined in `/etc/makepkg.conf` and can be extraced in a similar manner.

```sh
eval "$(grep '^OPTIONS' /etc/makepkg.conf)"
printf "options = %s\n" "${OPTIONS[@]}" >> .BUILDINFO
```

For the final thing - installed packages - you can get those via `pacman`.

```sh
pkglist=($(pacman -Q | sed "s# #-#"))
printf "installed = %s\n" "${pkglist[@]}" >> .BUILDINFO
```

## Generating `.MTREE`

```sh
bsdtar -czf .MTREE --format=mtree --options='!all,use-set,type,uid,gid,mode,time,size,md5,sha256,link' usr .PKGINFO .BUILDINFO
```

This will generate `.MTREE` file. You can verify contents using `zcat`.

```
$ zcat .MTREE
#mtree
/set type=file uid=1000 gid=1000 mode=644
./.BUILDINFO time=1735327948.61170402 size=45046 md5digest=85d3dc8d2df6cfc10a51e2e73d8b3ade sha256digest=6a5730f309470f8253a2e33d1d6284cc55bf64a1d621f1910177b1e87ea64e1c
./.PKGINFO time=1735318522.194493505 size=239 md5digest=d1d0ded3b9f3459f63714f4d0ec858f9 sha256digest=2a35e7b8b6a0c93d22f180666a20352a9d5a0953991a7199e4c5d545858cdc55
./usr time=1735317631.312675756 mode=755 type=dir
./usr/bin time=1735317634.369344816 mode=755 type=dir
./usr/bin/hello time=1735318539.567874863 mode=755 size=14424 md5digest=e33063b83acf1c5270197ba18a504209 sha256digest=67b23e0fb3a79c0a0fb9ec312c8885c51445509a311a34b8fc28eb38f7f6629b
```

The command i just used to generate mtree file is from an older version of `makepkg`. Newer version has a slightly heavier command. Nonetheless, result is same.

You can now package it again. Delete `hello.tar` if it exist from older step. Then run the tar command again.

```sh
tar -cjvf hello.tar $(ls -A)
```