# Completing a milestone

Hey!

This is my third post for SoK 2022. Let's go through what I've done since my last post, and what I plan to do next.

## Packaging

I had already submitted a good number of packages to Flathub early on, because I had time, and didn't want my schedule to become too packed all of a sudden later on.

<!-- more -->

One month into Season of KDE, and almost every high priority and medium priority packages have been submitted. Most have been accepted to Flathub, while some are awaiting review. I'll list down the submissions accepted after my last post.

### New packages on Flathub

1. Ikona
   - Flathub listing : [https://flathub.org/apps/details/org.kde.Ikona](https://flathub.org/apps/details/org.kde.Ikona)
   - Repo : [https://github.com/flathub/org.kde.Ikona](https://github.com/flathub/org.kde.Ikona)
   - Pull request : [https://github.com/flathub/flathub/pull/2829](https://github.com/flathub/flathub/pull/2829)
2. KUIViewer
   - Flathub listing : [https://flathub.org/apps/details/org.kde.kuiviewer](https://flathub.org/apps/details/org.kde.kuiviewera)
   - Repo : [https://github.com/flathub/org.kde.kuiviewer](https://github.com/flathub/org.kde.kuiviewer)
   - Pull request : [https://github.com/flathub/flathub/pull/2838](https://github.com/flathub/flathub/pull/2838)
3. KRDC
   - Flathub listing : [https://flathub.org/apps/details/org.kde.krdc](https://flathub.org/apps/details/org.kde.krdc)
   - Repo : [https://github.com/flathub/org.kde.krdc](https://github.com/flathub/org.kde.krdc)
   - Pull request : [https://github.com/flathub/flathub/pull/2859](https://github.com/flathub/flathub/pull/2859)

### Updates to existing packages

1. Update Akregator to 21.12.2 : [https://github.com/flathub/org.kde.akregator/pull/1](https://github.com/flathub/org.kde.akregator/pull/1)
2. Minor updates to KRename : [https://github.com/flathub/org.kde.krename/pull/1](https://github.com/flathub/org.kde.krename/pull/1)

### Upstream appdata patches

1. Skanlite : [https://invent.kde.org/graphics/skanlite/-/merge_requests/29](https://invent.kde.org/graphics/skanlite/-/merge_requests/29)
2. Heaptrack : [https://invent.kde.org/sdk/heaptrack/-/merge_requests/10](https://invent.kde.org/sdk/heaptrack/-/merge_requests/10)
3. KWave : [https://invent.kde.org/multimedia/kwave/-/merge_requests/8](https://invent.kde.org/multimedia/kwave/-/merge_requests/8)
4. Ikona : [https://invent.kde.org/sdk/ikona/-/merge_requests/8](https://invent.kde.org/sdk/ikona/-/merge_requests/8)

## Pending submissions

There are 5 packages currently under review.

- **Skanlite** seems to be in limbo for now, since I don't have a scanner to test it. Arranging for one is possible, but it won't be a long term solution if I am to maintain the package.
- **Zanshin** has issues with the migrator, which blocks starting the application. I've put it on the back-burner for the time being.
- **Nota** is mostly done. It uses a custom implementation for file picker, which means it can not use portal. We could have it as a beta package for now. I might look into patching the source code to use Qt file picker so that Nota can utilize portals. This again is a task for later.
- **Kwave** needs a minor fix to make one of its dependencies play well with `aarch64`. Otherwise, it is done.
- **Heaptrack** needs to be updated with extra permissions. I'll get this done soon.

## Rest of the packages

- **Kile** isn't detecting Texlive during runtime, which has become a mystery movie now. I'm hopeful to fix it pretty soon.
- **KAlarm** relies on KAuth for certain features, which cannot be run inside Flatpak environment. While I could come up with the relevant patches to CMake files, so as to not build KAuth, the application itself needs some patching to disable those features when running inside Flatpak environment.
- **K3b** has the same issues as KAlarm. Again, I have made the patches for build, but I'm yet to patch the source code to disable those features.

I plan to work on KAlarm and K3b source code during the second week of March if we decide to make them a priority.

With majority of the packaging work done, I should thank all the reviewers who vetted my submissions to Flathub, especially [Hubert Figuière](https://github.com/hfiguiere) who has been helpful and patient even when I seem to do stupid mistakes :)

## Plans for the next milestone

As I mentioned at the beginning, packaging work is almost sorted out, barring a couple of apps here and there. This leaves me with about a month and a half to work on other things I planned to.

I am playing around with [Flatpak External Data Checker](https://github.com/flathub/flatpak-external-data-checker), which can bring about some automation in the update process.

I'm still in the "exploration phase" for this tool. While there are multiple ways I could use it for KDE Flatpak manifests, I want to take up the one that best suits the release process KDE has.

Taking my leave for this post. I hope to publish more updates and improvements in my next one!
