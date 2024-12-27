# Packaging: The broader look

How does everything tie together?

Its the goal: giving users an easy way to pull in a software easily. How you achieve that goal is a design preference. How you define "easy" is a use case preference.

But the goal is to bring in packages to a user's system in an efficient and quick manner.

## Language package registries

Most of us are familiar with NPM or Pip. These are no different in philosophy than the case of Arch Linux we just discussed. Practically, they differ in their target system. While Arch packages cater to installing software on a system running Arch Linux, NPM and Pip cater to installing language dependencies for NodeJS or Python projects respectively.

But the goal remains same: bring in that package easy and quick.

The packaging concepts too remain the same. Arch packages use a shell file based recipe to define its package. NPM uses a JSON file and Pip uses a text file.

## Windows and MacOS

While these operating systems don't come out with a CLI based package manager out of the box, they can be loaded with third party package managers - Chocolatey and Homebrew respectively.

Chocolatey uses a nuspec file and powershell install script to define its packages. Homebrew uses an easy to read "formula" file which defines the package.

## Other Linux distributions.

- Solus uses a Yaml file to define its packages, later installed via its own package manager, eopkg.
- Void Linux uses `xbps-src` to build packages, defined in simple shell scripts, similar to Arch Linux
- ....*every sane distribution has some or the other packaging system*


## Newer packaging systems

### Flatpak

Claiming to be the "future of apps on Linux", this runs applications inside a soft sandbox, using ostree and bubblewrap. This makes the package distribution independent (package once, run everywhere).

By using ostree as its base, it lets each app specify its own dependency and independent versions, while also ensuring deduplicacy for conserving disk space.


### Snap

### Nix