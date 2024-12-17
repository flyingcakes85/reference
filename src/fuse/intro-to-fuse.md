# Introduction to FUSE

FUSE is an interface that allows users to implement filesystems without needing to touch the bulky kernel code. While we might have heard about ext4 or btrfs, we too can develop our own filesystems!

Get the slides [here](https://files.snehit.dev/FUSE_Talk_Slides.pdf)

## About FUSE

FUSE = **F**ilesystem in **USE**rspace

- filesystem implementation runs in userspace
  - implementation written in a language of user's choice (eg. Python)
  - implementation runs as a normal script or application
- FUSE module provides a bridge from filesystem implementation to kernel interface

FUSE has 3 major components:

- kernel module `fuse.ko`
- userspace library `libfuse`
- a mount utility

## FUSE support on your system

The relevant kernel module, `fuse.ko` is shipped since Linux version 2.6.14, so any FUSE based filesystem implementation can be assumed to work across virtually all Linux systems as of now.

The presence of FUSE kernel module can be checked on any Linux system.

```
[lain@wired ~]$ lsmod | grep fuse
fuse                  212992  5
```

```
[lain@wired ~]$ pkg-config --list-all | grep ^fuse
fuse3                          fuse3 - Filesystem in Userspace
fuse                           fuse - Filesystem in Userspace
```

## How does a virtual filesysetm with FUSE work?

- FUSE library knows about the basic list of operations on filesystems
- the implementation is expected to provide definition for these basic methods
- whenever system performs one of the filesystem operations, FUSE module refers the implementation code for knowing how to service request

## Why userspace?

- ability to utilize the userspace stack
  - python libraries
  - interact with desktop environment
  - interact with network resources
- don't touch the kernel if not needed

## Where is FUSE being utilized?

- [NTFS-3G](https://github.com/tuxera/ntfs-3g) uses FUSE to mount and read NTFS drives on Linux, FreeBSD, macOS and other operating systems
- [gocryptfs](https://github.com/rfjakob/gocryptfs) provides an encrypted overlay filesystem
- [google-drive-ocamlfuse](https://github.com/astrada/google-drive-ocamlfuse) lets you mount your Google Drive onto a local folder and browse/edit files
- [WikipediaFS](https://en.wikipedia.org/wiki/WikipediaFS) let you view and exit MediaWiki articles from your local file browser and text editor
