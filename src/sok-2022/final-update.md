# Wrapping Up and Beyond the Event

Hello!

This is my final status update for Season of KDE 2022. There's news on the Flatpak CI builds and my plans for after this event.

<!-- more -->

## Flatpak CI Builds

With help and suggestions from my mentor, Timothée Ravier, I got the CI script finalized from our side. I created a [pull request](https://invent.kde.org/sysadmin/ci-utilities/-/merge_requests/17) on the CI repository.

I initially didn't know that the images on KDE infra are not ephemeral. As a result, I spent a great deal of time on slimming down the Docker image I was using. I started off with an Arch Linux image, which I later switched to Fedora. The script itself went many changes before it was submitted for review. Initially it didn't have flexibility with regards to location of manifest and could only build apps requiring the latest KDE platform. It was updated over and over until we were satisfied.

After I submitted the script for review, Ben Cooksley gave a lot of useful suggestions. The script is now much more flexible with regards to manifest location. It can dynamically decide which platform and SDK to use.

The pull request hasn't been merged, but I will get it done in due time.

## FEDC on KDE Invent

I also planned to have an automatic updater on our master Flatpak repository. While the overall idea of how this is to be accomplished has been clear from the very beginning, the actual implementation however has gone many changes. For now, it is a [shell script](https://gitlab.com/flyingcakes/kde-flathub-master/-/blob/master/updater.sh) which I wanted to move to Python. I _actually_ did rewrite it in Python - and I wasn't yet satisfied.

During the review of my pull request for Flatpak CI script, Ben proposed that we have each manifest in individual application repositories. A side effect is that the script will need to be modified a little bit.

I've kept the script on hold for now, although the basic bits are set up. I'll just need to plug in the final stuff once we decide where the manifests are going to be placed.

## Beyond SoK

Season of KDE has been a great experience for me. I got to know some very cool people. I got to learn cool things. I did cool things. I've been close to open source and SoK was a great booster to my experience.

I'll be taking a break for ~3 weeks to get done with my exams. When I return, I'll fast track the CI stuff that is currently on draft. I'm still available on the KDE Matrix and email.

Bye bye!
