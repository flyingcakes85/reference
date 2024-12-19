# FEDC and Update Automation

Hello!

This is my fourth status update for Season of KDE 2022.

In my [last post](https://snehit.dev/posts/kde/sok-2022/completing-a-milestone/) I told how packaging was almost done and I was looking out to automate updates.

## Finalizing the checker data

There are multiple backends that can be used with Flatpak External Data Checker. I initially planned to use the HTML checker, for it seemed logical at that time. But by the time I finalized it, I was already gravitating towards Anitya Release Monitoring. Since Anitya already has version tracking for most packages, it seemed a bit easier than the HTML checker. With Anitya, all I need to know is the project id, which is just a simple search on their website.

The packages that don't exist on Anitya are quite easy to submit. A Fedora account is all it takes. (you can use any Open ID). For example, I added [Falkon](https://release-monitoring.org/project/242801/) on Anitya.

### Roadblocks

There are certain corner cases where FEDC didn't work as expected. GPGME is the first example. The download server for GPGME is incorrectly setting Content-Encoding as `text/*`, which FEDC discards, as it _could_ be an error page. The right way out will be to get GPGME developers in the loop and inform them of this issue.

Another package, SQLite, uses the current year in its URL, making it difficult to predict via a script.

## Experimenting with GitHub Actions

I made a [test repository](https://github.com/flyingcakes85/kontact-fedc/) to demonstrate the working of FEDC via a GitHub action. We were planning to get an access token added to KDE repos on Flathub GitHub organization so that we could utilize our GitHub Action. One of my submissions had erroneous checker data. It went unnoticed, until the Flathub bot made an unexpected pull request on a repo. That made us realise that we don't actually need to add an access token. Flathub runs the updater and makes pull requests automatically.

Happy accident!

So, all I am required to do is to add checker data to as many manifests as possible. The update for 21.12.3 was done manually, but the updates next month should be mostly automated!

Links to all my pull requests are at end of this post.

## Next steps

I plan to add checker data to all the remaining applications (~80) in the next 2-to 3 days. I'm also parallelly looking over to use FEDC on KDE Invent to automatically update non KDE dependencies on master manifest repo.

## Links to pull requests

### Adding checker data

- Akregator : [https://github.com/flathub/org.kde.akregator/pull/5](https://github.com/flathub/org.kde.akregator/pull/5)
- Artikulate : [https://github.com/flathub/org.kde.artikulate/pull/9](https://github.com/flathub/org.kde.artikulate/pull/9)
- Blinken : [https://github.com/flathub/org.kde.blinken/pull/22](https://github.com/flathub/org.kde.blinken/pull/22)
- Bomber : [https://github.com/flathub/org.kde.bomber/pull/17](https://github.com/flathub/org.kde.bomber/pull/17)
- Bovo : [https://github.com/flathub/org.kde.bovo/pull/18](https://github.com/flathub/org.kde.bovo/pull/18)
- Falkon : [https://github.com/flathub/org.kde.falkon/pull/3](https://github.com/flathub/org.kde.falkon/pull/3)
- Granatier : [https://github.com/flathub/org.kde.granatier/pull/16](https://github.com/flathub/org.kde.granatier/pull/16)
- Filelight : [https://github.com/flathub/org.kde.filelight/pull/2](https://github.com/flathub/org.kde.filelight/pull/2)
- KColorChooser : [https://github.com/flathub/org.kde.kcolorchooser/pull/3](https://github.com/flathub/org.kde.kcolorchooser/pull/3)
- Kalzium : [https://github.com/flathub/org.kde.kalzium/pull/44](https://github.com/flathub/org.kde.kalzium/pull/44)
- Kamoso : [https://github.com/flathub/org.kde.kamoso/pull/5](https://github.com/flathub/org.kde.kamoso/pull/5)
- Kanagram : [https://github.com/flathub/org.kde.kanagram/pull/21](https://github.com/flathub/org.kde.kanagram/pull/21)
- Kapman : [https://github.com/flathub/org.kde.kapman/pull/42](https://github.com/flathub/org.kde.kapman/pull/42)
- KAtomic : [https://github.com/flathub/org.kde.katomic/pull/41](https://github.com/flathub/org.kde.katomic/pull/41)
- KBlackbox : [https://github.com/flathub/org.kde.kblackbox/pull/17](https://github.com/flathub/org.kde.kblackbox/pull/17)
- KBlocks : [https://github.com/flathub/org.kde.kblocks/pull/42](https://github.com/flathub/org.kde.kblocks/pull/42)
- KBounce : [https://github.com/flathub/org.kde.kbounce/pull/43](https://github.com/flathub/org.kde.kbounce/pull/43)
- KBreakout : [https://github.com/flathub/org.kde.kbreakout/pull/2](https://github.com/flathub/org.kde.kbreakout/pull/2)
- KBruch : [https://github.com/flathub/org.kde.kbruch/pull/40](https://github.com/flathub/org.kde.kbruch/pull/40)
- KDF : [https://github.com/flathub/org.kde.kdf/pull/2](https://github.com/flathub/org.kde.kdf/pull/2)

### Updates for 21.12.3

- KGeoTag : [https://github.com/flathub/org.kde.kgeotag/pull/1](https://github.com/flathub/org.kde.kgeotag/pull/1)
- Akregator : [https://github.com/flathub/org.kde.akregator/pull/4](https://github.com/flathub/org.kde.akregator/pull/4)
- KRuler : [https://github.com/flathub/org.kde.kruler/pull/2](https://github.com/flathub/org.kde.kruler/pull/2)
- KFind : [https://github.com/flathub/org.kde.kfind/pull/2](https://github.com/flathub/org.kde.kfind/pull/2)
- KDevelop : [https://github.com/flathub/org.kde.kdevelop/pull/16](https://github.com/flathub/org.kde.kdevelop/pull/16)
- KUIViewer : [https://github.com/flathub/org.kde.kuiviewer/pull/1](https://github.com/flathub/org.kde.kuiviewer/pull/1)
- KRDC : [https://github.com/flathub/org.kde.krdc/pull/1](https://github.com/flathub/org.kde.krdc/pull/1)
- KColorChooser : [https://github.com/flathub/org.kde.kcolorchooser/pull/2](https://github.com/flathub/org.kde.kcolorchooser/pull/2)
- KGet : [https://github.com/flathub/org.kde.kget/pull/3](https://github.com/flathub/org.kde.kget/pull/3)
- KDF : [https://github.com/flathub/org.kde.kdf/pull/1](https://github.com/flathub/org.kde.kdf/pull/1)
- Koko : [https://github.com/flathub/org.kde.koko/pull/3](https://github.com/flathub/org.kde.koko/pull/3)
- GwenView : [https://github.com/flathub/org.kde.gwenview/pull/24](https://github.com/flathub/org.kde.gwenview/pull/24)
- Kdenline : [https://github.com/flathub/org.kde.kdenlive/pull/168](https://github.com/flathub/org.kde.kdenlive/pull/168)
- Okular : [https://github.com/flathub/org.kde.okular/pull/144](https://github.com/flathub/org.kde.okular/pull/144)
- Itineary : [https://github.com/flathub/org.kde.itinerary/pull/8](https://github.com/flathub/org.kde.itinerary/pull/8)
- KTorrent : [https://github.com/flathub/org.kde.ktorrent/pull/29](https://github.com/flathub/org.kde.ktorrent/pull/29)
- KTuberling : [https://github.com/flathub/org.kde.ktuberling/pull/53](https://github.com/flathub/org.kde.ktuberling/pull/53)
- Konversation : [https://github.com/flathub/org.kde.konversation/pull/22](https://github.com/flathub/org.kde.konversation/pull/22)
- Kontact : [https://github.com/flathub/org.kde.kontact/pull/61](https://github.com/flathub/org.kde.kontact/pull/61)
- KCalc : [https://github.com/flathub/org.kde.kcalc/pull/22](https://github.com/flathub/org.kde.kcalc/pull/22)
- Ark : [https://github.com/flathub/org.kde.ark/pull/38](https://github.com/flathub/org.kde.ark/pull/38)
- Kontrast : [https://github.com/flathub/org.kde.kontrast/pull/17](https://github.com/flathub/org.kde.kontrast/pull/17)
- Kamoso : [https://github.com/flathub/org.kde.kamoso/pull/4](https://github.com/flathub/org.kde.kamoso/pull/4)
- Elisa : [https://github.com/flathub/org.kde.elisa/pull/57](https://github.com/flathub/org.kde.elisa/pull/57)
- Dolphin : [https://github.com/flathub/org.kde.dolphin/pull/72](https://github.com/flathub/org.kde.dolphin/pull/72)
- Filelight : [https://github.com/flathub/org.kde.filelight/pull/1](https://github.com/flathub/org.kde.filelight/pull/1)

I will see you in next post with more updates!
