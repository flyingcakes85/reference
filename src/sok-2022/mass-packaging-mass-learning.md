# Mass packaging and mass learning

Hey!

This is my second post for SoK 2022. Its going to be lot more technical than my last one. Lets dive into all that we did in past few days.


## Learning packaging

As I had mentioned in my [last post](@/posts/kde/sok-2022/beginning-with-sok-2022.md), I already knew the basics of packaging. Flatpak is certainly new for me though. I have used Flatpaks, but never published them.

It was easy to learn packaging and writing manifests. I submitted a couple of easy applications to Flathub before the contribution period started, so as to get a good idea of what I'll be doing during the coming weeks.

## Challenges faced

### Incomplete Appdata info

Many upstream packages did not have a complete appdata.xml file. Most noticeably, the [OARS](https://hughsie.github.io/oars/) data was missing. However, I did not get to know about it until I submitted my first package, and [it failed CI test](https://github.com/flathub/flathub/pull/2748#issuecomment-1008280898).

Logs made it clear the appdata was at fault. Poking around, I fixed the appdata, but I needed a quick way to verify appdata for future packages. I made an alias in my shell, which would do the job for me.

```sh
alias ad="flatpak run --env=G_DEBUG=fatal-criticals org.freedesktop.appstream-glib validate \$(find . -name \"*appdata.xml\")"
```

You need to first `cd` into your build directory. And then simply run `ad`. It will find all appdata files, and validate them.

### Using right sources

My first three submissions used a git source rather than release tarballs. While using a git source doesn't pose major issues, release tarballs are easier to work with.

### Using right permissions

Back when I started, I did not know which permissions I had to use for different packages. All it required was to read the [documentation on Flatpak website](https://docs.flatpak.org/en/latest/sandbox-permissions.html). Running an app with missing permissions usually printed errors in the console, which could be used to verify permissions. Nearly every GUI application requires `--socket=fallback-x11`, `--socket=ipc`, `--socket=wayland`, and for applications using OpenGL, `--device=dri`. If application required network access, add `--share=network`.

For filesystem access, you can use `--filesystem=host`, but most of the times, you don't need such a broad permission. You have options like `--filesystem=home`, `--filesystem=/path/to/dir` and `--filesystem=xdg-name`, where `name` is one of the XDG directories. These can be used to limit filesystem access, and be more closer to the idea of sandboxing applications.

### Other minor issues

There were a couple of other minor issues with my initial few manifests. Reviewers suggested changes, and I gradually understood them and avoided them.

## Portal exists

So, I had zero idea about Portals, which allow a Flatpak application to access files on the user filesystem, without requiring explicit permissions in manifest. I only got to know about it during reviews.

## Submissions list

I'm listing only the accepted submissions here.

### Submissions to Flathub

1. KFind
   - Flathub listing : [https://flathub.org/apps/details/org.kde.kfind](https://flathub.org/apps/details/org.kde.kfind)
   - Repo : [https://github.com/flathub/org.kde.kfind](https://github.com/flathub/org.kde.kfind)
   - Pull request : [https://github.com/flathub/flathub/pull/2748](https://github.com/flathub/flathub/pull/2748)
2. KRuler
   - Flathub listing : [https://flathub.org/apps/details/org.kde.kruler](https://flathub.org/apps/details/org.kde.kruler)
   - Repo : [https://github.com/flathub/org.kde.kruler](https://github.com/flathub/org.kde.kruler)
   - Pull request : [https://github.com/flathub/flathub/pull/2750](https://github.com/flathub/flathub/pull/2750)
3. Cantor
   - Flathub listing : [https://flathub.org/apps/details/org.kde.cantor](https://flathub.org/apps/details/org.kde.cantor)
   - Repo : [https://github.com/flathub/org.kde.cantor](https://github.com/flathub/org.kde.cantor)
   - Pull request : [https://github.com/flathub/flathub/pull/2762](https://github.com/flathub/flathub/pull/2762)
4. KDiff3
   - Flathub listing : [https://flathub.org/apps/details/org.kde.kdiff3](https://flathub.org/apps/details/org.kde.kdiff3)
   - Repo : [https://github.com/flathub/org.kde.kdiff3](https://github.com/flathub/org.kde.kdiff3)
   - Pull request : [https://github.com/flathub/flathub/pull/2764](https://github.com/flathub/flathub/pull/2764)
5. KColorChooser
   - Flathub listing : [https://flathub.org/apps/details/org.kde.kcolorchooser](https://flathub.org/apps/details/org.kde.kcolorchooser)
   - Repo : [https://github.com/flathub/org.kde.kcolorchooser](https://github.com/flathub/org.kde.kcolorchooser)
   - Pull request : [https://github.com/flathub/flathub/pull/2766](https://github.com/flathub/flathub/pull/2766)
6. KAlgebra
   - Flathub listing : [https://flathub.org/apps/details/org.kde.kalgebra](https://flathub.org/apps/details/org.kde.kalgebra)
   - Repo : [https://github.com/flathub/org.kde.kalgebra](https://github.com/flathub/org.kde.kalgebra)
   - Pull request : [https://github.com/flathub/flathub/pull/2771](https://github.com/flathub/flathub/pull/2771)
7. KPhotoAlbum
   - Flathub listing : [https://flathub.org/apps/details/org.kde.kphotoalbum](https://flathub.org/apps/details/org.kde.kphotoalbum)
   - Repo : [https://github.com/flathub/org.kde.kphotoalbum](https://github.com/flathub/org.kde.kphotoalbum)
   - Pull request : [https://github.com/flathub/flathub/pull/2775](https://github.com/flathub/flathub/pull/2775)
8. Falkon
   - Flathub listing : [https://flathub.org/apps/details/org.kde.falkon](https://flathub.org/apps/details/org.kde.falkon)
   - Repo : [https://github.com/flathub/org.kde.falkon](https://github.com/flathub/org.kde.falkon)
   - Pull request : [https://github.com/flathub/flathub/pull/2781](https://github.com/flathub/flathub/pull/2781)
9. KGeoTag
   - Flathub listing : [https://flathub.org/apps/details/org.kde.kgeotag](https://flathub.org/apps/details/org.kde.kgeotag)
   - Repo : [https://github.com/flathub/org.kde.kgeotag](https://github.com/flathub/org.kde.kgeotag)
   - Pull request : [https://github.com/flathub/flathub/pull/2814](https://github.com/flathub/flathub/pull/2814)

### Upstream code patches

Thanks to help from Albert Cid, I was able to push some minor updates to KDF.

KDF has option to open disk with a custom command from the user. By default, this opens disk in Dolphin file manager. However, this fails when Dolphin is not installed on system. The issue arose when packaging KDF for Flathub required including Dolphin in the manifest, which was not ideal. As a result, KDF was put on hold, and I was suggested to add a feature that would open drives in system default file manager. Thanks to Aleix Pol, I already had an idea about the changes to make in source code. My first pull request was closed because I removed the feature for using custom file manager command.

In my second pull request, I added the feature to open disk in system default file manager, and retained the option to specify a custom file manager command. User can easily switch them in settings. Albert suggested I send a patch to disable option to specify custom command, as they aren't supported in Flatpak environment.

- Pull request adding feature to open drive in default file manager : [https://invent.kde.org/utilities/kdf/-/merge_requests/5](https://invent.kde.org/utilities/kdf/-/merge_requests/5)
- Pull request disabling file manager command option in Flatpak environment : [https://invent.kde.org/utilities/kdf/-/merge_requests/7](https://invent.kde.org/utilities/kdf/-/merge_requests/7)
- Minor patch to update condition check to be more idiomatic : [https://invent.kde.org/utilities/kdf/-/merge_requests/6](https://invent.kde.org/utilities/kdf/-/merge_requests/6)

### Updates pushed

1. Falkon update to 3.2 : [https://github.com/flathub/org.kde.falkon/pull/2](https://github.com/flathub/org.kde.falkon/pull/2)
2. Exiv2 update for Koko : [https://github.com/flathub/org.kde.koko/pull/2](https://github.com/flathub/org.kde.koko/pull/2)
3. Exiv2 update for Kdenlive : [https://github.com/flathub/org.kde.kdenlive/pull/165](https://github.com/flathub/org.kde.kdenlive/pull/165)
4. Exiv2 update for Krita : [https://github.com/flathub/org.kde.krita/pull/51](https://github.com/flathub/org.kde.krita/pull/51)
5. Exiv2 update for GwenView : [https://github.com/flathub/org.kde.gwenview/pull/22](https://github.com/flathub/org.kde.gwenview/pull/22)
6. Exiv2 update for KPhotoAlbum : [https://github.com/flathub/org.kde.kphotoalbum/pull/1](https://github.com/flathub/org.kde.kphotoalbum/pull/1)
7. Exiv2 update to Flatpak master repo : [https://invent.kde.org/packaging/flatpak-kde-applications/-/merge_requests/74](https://invent.kde.org/packaging/flatpak-kde-applications/-/merge_requests/74)
8. Eigen update to Flatpak master repo: [https://invent.kde.org/packaging/flatpak-kde-applications/-/merge_requests/72](https://invent.kde.org/packaging/flatpak-kde-applications/-/merge_requests/72)
9. Boost update to Flatpak master repo : [https://invent.kde.org/packaging/flatpak-kde-applications/-/merge_requests/71
   ](https://invent.kde.org/packaging/flatpak-kde-applications/-/merge_requests/71)

### Upstream appdata patches

1. KFloppy : [https://invent.kde.org/utilities/kfloppy/-/merge_requests/1](https://invent.kde.org/utilities/kfloppy/-/merge_requests/1)
2. KRuler : [https://invent.kde.org/graphics/kruler/-/merge_requests/5](https://invent.kde.org/graphics/kruler/-/merge_requests/5)
3. Cantor : [https://invent.kde.org/education/cantor/-/merge_requests/37](https://invent.kde.org/education/cantor/-/merge_requests/37)
4. KFind : [https://invent.kde.org/utilities/kfind/-/merge_requests/8](https://invent.kde.org/utilities/kfind/-/merge_requests/8)
5. KDiff3 : [https://invent.kde.org/sdk/kdiff3/-/merge_requests/37](https://invent.kde.org/sdk/kdiff3/-/merge_requests/37)
6. KAlgebra : [https://invent.kde.org/education/kalgebra/-/merge_requests/22](https://invent.kde.org/education/kalgebra/-/merge_requests/22)
7. Akregator : [https://invent.kde.org/pim/akregator/-/merge_requests/22](https://invent.kde.org/pim/akregator/-/merge_requests/22)
8. KPhotoAlbum : [https://invent.kde.org/graphics/kphotoalbum/-/merge_requests/18](https://invent.kde.org/graphics/kphotoalbum/-/merge_requests/18)
9. KGeoTag : [https://invent.kde.org/graphics/kgeotag/-/merge_requests/13](https://invent.kde.org/graphics/kgeotag/-/merge_requests/13)
10. KDF : [https://invent.kde.org/utilities/kdf/-/merge_requests/3](https://invent.kde.org/utilities/kdf/-/merge_requests/3)
11. Dolphin : [https://invent.kde.org/system/dolphin/-/merge_requests/325](https://invent.kde.org/system/dolphin/-/merge_requests/325)
12. Falkon : [https://invent.kde.org/network/falkon/-/merge_requests/27](https://invent.kde.org/network/falkon/-/merge_requests/27)

Thanks to the KDE team for being patient with reviews and helping me in the roadblocks I faced!
