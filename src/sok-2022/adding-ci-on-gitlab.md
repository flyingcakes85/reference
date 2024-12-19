# Adding CI Pipelines on GitLab

Hey there!

This is my fifth status update for Season of KDE 2022.

This time, I have updates on the automation side of things. I got a preliminary version of the required GitLab pipelines working.

## Pipeline for Updating Manifests

I got my hands dirty with Docker and added the Flatpak External Data Checker image over on [Quay](https://quay.io). The image was used over on my Gitlab repositories. This let me run "actions", similar to what we have on GitHub, but on GitLab too. Since we're not relying on the official FEDC image, I can control what all goes into the image and add extra dependencies that would be required. (like curl)

Currently, the script is in bash. I plan to port it to Python, as it will be much easier to maintain and work with.

The script is expected to loop through all manifests in the repository and run FEDC on each one of them while capturing FEDC output to detect which packages have been updated. It then does some cleanup and sorts it to get the final list. With all the changes, it will finally commit and create a merge request over on the repository. To avoid creating multiple merge requests for the same set of updates, the script will check if there is an existing merge request already. To help in this, it adds a short hash in the merge request title, which lets it cross-check the set of updates and decide whether to create a new merge request or not.

The repository can be found here -> [https://gitlab.com/flyingcakes/kde-flathub-master](https://gitlab.com/flyingcakes/kde-flathub-master). Actual update script is in the `updater.sh` file in this repo.

## Pipeline for Application CI

To goal here is to do a Flatpak build for KDE applications whenever they receive a new commit or merge request. We currently have similar CI pipelines running, but they are only for FreeBSD and OpenSuse. I plan to add a third job there, which will build the application inside Flatpak environment, and provide a way to install and test those builds on a local machine.

The inspiration for this has been taken from similar pipelines that run on Gnome GitLab. In essence, on every run, we fetch the application's Flatpak manifest, replace its source link with the latest commit and try a build. If the build succeeds, we upload its `.flatpakref` file as an artefact.

My implementation for this is still in an alpha phase and will need some more work to become viable for regular use on KDE applications.

The testing repository for this can be found here -> [https://gitlab.com/flyingcakes/kdiff-v2/](https://gitlab.com/flyingcakes/kdiff-v2/)

## Follow up on Checker Data Additions on GitHub

In my last post, I described how I'm adding checker data to manifests hosted on GitHub. I have added the data to _most_ applications. The bigger ones like Krita and KdenLive are still missing data. This should be fixed in due time.

We noticed some issues with it, which seems to be related to FEDC itself. It sometimes creates repeated pull requests for the same package.

Other issues were caused because I had inadvertently provided wrong links in checker data.

None of the issues caused any inconvenience though. The maintainers have been thoroughly verifying pull requests to ensure nothing unexpected goes into the actual application. I fixed those minor issues as and when we got to know about them.

## Links to Pull Requests

### Adding checker data

- Index : [https://github.com/flathub/org.kde.index/pull/3](https://github.com/flathub/org.kde.index/pull/3)
- Itinerary : [https://github.com/flathub/org.kde.itinerary/pull/9](https://github.com/flathub/org.kde.itinerary/pull/9)
- KAlgebra : [https://github.com/flathub/org.kde.kalgebra/pull/3](https://github.com/flathub/org.kde.kalgebra/pull/3)
- KDiamond : [https://github.com/flathub/org.kde.kdiamond/pull/44](https://github.com/flathub/org.kde.kdiamond/pull/44)
- KCalc : [https://github.com/flathub/org.kde.kcalc/pull/23](https://github.com/flathub/org.kde.kcalc/pull/23)
- KCacheGrind : [https://github.com/flathub/org.kde.kcachegrind/pull/29](https://github.com/flathub/org.kde.kcachegrind/pull/29)
- KGeoTag : [https://github.com/flathub/org.kde.kgeotag/pull/2](https://github.com/flathub/org.kde.kgeotag/pull/2)
- KFourInLine : [https://github.com/flathub/org.kde.kfourinline/pull/2](https://github.com/flathub/org.kde.kfourinline/pull/2)
- KDiff3 : [https://github.com/flathub/org.kde.kdiff3/pull/2](https://github.com/flathub/org.kde.kdiff3/pull/2)
- KFind : [https://github.com/flathub/org.kde.kfind/pull/3](https://github.com/flathub/org.kde.kfind/pull/3)
- KGeography : [https://github.com/flathub/org.kde.kgeography/pull/44](https://github.com/flathub/org.kde.kgeography/pull/44)
- Kid3 : [https://github.com/flathub/org.kde.kid3/pull/3](https://github.com/flathub/org.kde.kid3/pull/3)
- Kasts : [https://github.com/flathub/org.kde.kasts/pull/2](https://github.com/flathub/org.kde.kasts/pull/2)
- KolorPaint : [https://github.com/flathub/org.kde.kolourpaint/pull/53](https://github.com/flathub/org.kde.kolourpaint/pull/53)
- Labplot2 : [https://github.com/flathub/org.kde.labplot2/pull/4](https://github.com/flathub/org.kde.labplot2/pull/4)
- KTrip : [https://github.com/flathub/org.kde.ktrip/pull/5](https://github.com/flathub/org.kde.ktrip/pull/5)
- Kile : [https://github.com/flathub/org.kde.kile/pull/2](https://github.com/flathub/org.kde.kile/pull/2)
- Kongress : [https://github.com/flathub/org.kde.kongress/pull/3](https://github.com/flathub/org.kde.kongress/pull/3)
- Kompare : [https://github.com/flathub/org.kde.kompare/pull/1](https://github.com/flathub/org.kde.kompare/pull/1)
- Kiten : [https://github.com/flathub/org.kde.kiten/pull/21](https://github.com/flathub/org.kde.kiten/pull/21)
- Koko : [https://github.com/flathub/org.kde.koko/pull/4](https://github.com/flathub/org.kde.koko/pull/4)
- Konversation : [https://github.com/flathub/org.kde.konversation/pull/23](https://github.com/flathub/org.kde.konversation/pull/23)
- KPhotoAlbum : [https://github.com/flathub/org.kde.kphotoalbum/pull/2](https://github.com/flathub/org.kde.kphotoalbum/pull/2)
- Konquest : [https://github.com/flathub/org.kde.konquest/pull/22](https://github.com/flathub/org.kde.konquest/pull/22)
- Kontrast : [https://github.com/flathub/org.kde.kontrast/pull/18](https://github.com/flathub/org.kde.kontrast/pull/18)
- KRDC : [https://github.com/flathub/org.kde.krdc/pull/2](https://github.com/flathub/org.kde.krdc/pull/2)
- KRename : [https://github.com/flathub/org.kde.krename/pull/2](https://github.com/flathub/org.kde.krename/pull/2)
- KStars : [https://github.com/flathub/org.kde.kstars/pull/1](https://github.com/flathub/org.kde.kstars/pull/1)
- KTorrent : [https://github.com/flathub/org.kde.ktorrent/pull/30](https://github.com/flathub/org.kde.ktorrent/pull/30)
- KSquares : [https://github.com/flathub/org.kde.ksquares/pull/42](https://github.com/flathub/org.kde.ksquares/pull/42)
- KRuler : [https://github.com/flathub/org.kde.kruler/pull/3](https://github.com/flathub/org.kde.kruler/pull/3)
- KSudoku : [https://github.com/flathub/org.kde.ksudoku/pull/44](https://github.com/flathub/org.kde.ksudoku/pull/44)
- KTuberling : [https://github.com/flathub/org.kde.ktuberling/pull/54](https://github.com/flathub/org.kde.ktuberling/pull/54)
- KTurtle : [https://github.com/flathub/org.kde.kturtle/pull/21](https://github.com/flathub/org.kde.kturtle/pull/21)
- KUIViewer : [https://github.com/flathub/org.kde.kuiviewer/pull/2](https://github.com/flathub/org.kde.kuiviewer/pull/2)
- KWordQuiz : [https://github.com/flathub/org.kde.kwordquiz/pull/43](https://github.com/flathub/org.kde.kwordquiz/pull/43)
- Lokalize : [https://github.com/flathub/org.kde.lokalize/pull/29](https://github.com/flathub/org.kde.lokalize/pull/29)
- Marble : [https://github.com/flathub/org.kde.marble/pull/22](https://github.com/flathub/org.kde.marble/pull/22)
- Massif Visualizer : [https://github.com/flathub/org.kde.massif-visualizer/pull/7](https://github.com/flathub/org.kde.massif-visualizer/pull/7)
- Kontact : [https://github.com/flathub/org.kde.kontact/pull/62](https://github.com/flathub/org.kde.kontact/pull/62)
- Minuet : [https://github.com/flathub/org.kde.minuet/pull/33](https://github.com/flathub/org.kde.minuet/pull/33)
- NeoChat : [https://github.com/flathub/org.kde.neochat/pull/13](https://github.com/flathub/org.kde.neochat/pull/13)
- Kubrick : [https://github.com/flathub/org.kde.kubrick/pull/42](https://github.com/flathub/org.kde.kubrick/pull/42)
- KTouch : [https://github.com/flathub/org.kde.ktouch/pull/36](https://github.com/flathub/org.kde.ktouch/pull/36)
- Nota : [https://github.com/flathub/org.kde.nota/pull/1](https://github.com/flathub/org.kde.nota/pull/1)
- Okular : [https://github.com/flathub/org.kde.okular/pull/147](https://github.com/flathub/org.kde.okular/pull/147)
- Okteta : [https://github.com/flathub/org.kde.okteta/pull/9](https://github.com/flathub/org.kde.okteta/pull/9)
- Pix : [https://github.com/flathub/org.kde.pix/pull/4](https://github.com/flathub/org.kde.pix/pull/4)
- Skrooge : [https://github.com/flathub/org.kde.skrooge/pull/23](https://github.com/flathub/org.kde.skrooge/pull/23)
- Subtitle Composer : [https://github.com/flathub/org.kde.subtitlecomposer/pull/2](https://github.com/flathub/org.kde.subtitlecomposer/pull/2)
- Tellico : [https://github.com/flathub/org.kde.tellico/pull/2](https://github.com/flathub/org.kde.tellico/pull/2)
- Palapeli : [https://github.com/flathub/org.kde.palapeli/pull/45](https://github.com/flathub/org.kde.palapeli/pull/45)
- KLettres : [https://github.com/flathub/org.kde.klettres/pull/20](https://github.com/flathub/org.kde.klettres/pull/20)
- KNetWalk : [https://github.com/flathub/org.kde.knetwalk/pull/43](https://github.com/flathub/org.kde.knetwalk/pull/43)
- KNavalBattle : [https://github.com/flathub/org.kde.knavalbattle/pull/42](https://github.com/flathub/org.kde.knavalbattle/pull/42)
- KLines : [https://github.com/flathub/org.kde.klines/pull/42](https://github.com/flathub/org.kde.klines/pull/42)
- Klickety : [https://github.com/flathub/org.kde.klickety/pull/43](https://github.com/flathub/org.kde.klickety/pull/43)
- KJumpingCube : [https://github.com/flathub/org.kde.kjumpingcube/pull/44](https://github.com/flathub/org.kde.kjumpingcube/pull/44)
- KillBots : [https://github.com/flathub/org.kde.killbots/pull/42](https://github.com/flathub/org.kde.killbots/pull/42)
- Kigo : [https://github.com/flathub/org.kde.kigo/pull/41](https://github.com/flathub/org.kde.kigo/pull/41)
- Kig : [https://github.com/flathub/org.kde.kig/pull/21](https://github.com/flathub/org.kde.kig/pull/21)
- KGoldRunner : [https://github.com/flathub/org.kde.kgoldrunner/pull/41](https://github.com/flathub/org.kde.kgoldrunner/pull/41)
- KGet : [https://github.com/flathub/org.kde.kget/pull/4](https://github.com/flathub/org.kde.kget/pull/4)
- Tokodon : [https://github.com/flathub/org.kde.tokodon/pull/1](https://github.com/flathub/org.kde.tokodon/pull/1)
- Vvave : [https://github.com/flathub/org.kde.vvave/pull/2](https://github.com/flathub/org.kde.vvave/pull/2)

### Other minor fixes

- Nota - Fix for filename without extension : [https://github.com/flathub/org.kde.nota/pull/3](https://github.com/flathub/org.kde.nota/pull/3)
- Pix - Add filename for applet window button : [https://github.com/flathub/org.kde.pix/pull/15](https://github.com/flathub/org.kde.pix/pull/15)
- Labplot2 - Remove checker data for labplot and cfitsio : [https://github.com/flathub/org.kde.labplot2/pull/12](https://github.com/flathub/org.kde.labplot2/pull/12)
- Itinerary - Fix link for poppler : [https://github.com/flathub/org.kde.itinerary/pull/11](https://github.com/flathub/org.kde.itinerary/pull/11)
- Koko - Fix exiv2 checker data : [https://github.com/flathub/org.kde.koko/pull/6](https://github.com/flathub/org.kde.koko/pull/6)
