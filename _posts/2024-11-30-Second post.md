---
title: Reworking backups on the new PC
date: 2024-11-30 13:41 -0500
categories:
  - computers
tags:
  - setup
toc: true
---
# What's changed that I need a new strategy?
New PC build now has a main drive and a second drive used just for different kinds of projects (code, music, whatever). This means I can separate standard home dir style data (email, documents, and so on) from things that I'm working on in different states. It also means I can set up different sets of rules and exclusions based on the contents and keep that tracking simpler.

## The basics with borg and rclone
I've been using borg to do local backups to a USB drive, then syncing those offsite with rclone. This is a pretty basic combination, and there's not a lot to it. I have a script that controls the borg backup so I can run it manually or from a systemd timer, and a one-shot rclone service also on a timer.

The relevant part of the borg control flow: 

```shell 
# Backup all of /home except a few excluded directories and files
borg create -v --stats  --list --filter=E --compression auto,zstd \
--one-file-system --exclude-from /home/mrman/bin/borg-home-excludes.txt \
"$HOME_REPO"::${NOW} /home/mrman

# Backup all of Projects except a few excluded github directories and files
borg create -v --stats  --list --filter=E --compression auto,zstd \
--one-file-system --exclude-from /home/mrman/bin/borg-projects-excludes.txt \
"$PROJ_REPO"::${NOW} /home/mrman/Projects

# Prune the repos of extra backups
echo "Repository: $HOME_REPO"
borg prune -v --list "$HOME_REPO" \
--keep-daily=7 \
--keep-weekly=4 \
--keep-monthly=-1

echo "Repository: $PROJ_REPO"
borg prune -v --list "$PROJ_REPO" \
--keep-daily=7 \
--keep-weekly=4 \
--keep-monthly=-1

``` 
{: file="~/bin/borg.sh" }

The `/home/mrman/Projects` directory is the mount point for the second drive, so I can limit the backup to single filesystems for each run. Not sure it's really needed but it seems reasonable.

The rclone service is logs stats to the journal as it runs and 10 transfers uses about half my upload pipe, which means even if it triggers during normal use I don't really feel it doing typical internet things. Of course, the timer runs in the early morning when I'm not online.

```shell
ExecStart=/usr/bin/rclone --stats 30s --stats-one-line --stats-log-level NOTICE --transfers=10 \
-v sync /media/Vault <rclone:remote>  --include='/{butcher-home,butcher-proj}/**'
```
{: file="~/.config/systemd/user/rclone.service" }

I also added the USB drives to the `fstab` to make sure they're always available, and not just when I'm logged into a Gnome session. These are still owned by me and show up in Nautilus.

```shell
UUID=4AF28D65F28D55DB /media/Vault ntfs nosuid,nodev,nofail,X-fstrim.notrim,x-gvfs-show,uid=1000,gid=1000 0 0
UUID=5C6EC14E6EC1221E /media/Media ntfs nosuid,nodev,nofail,X-fstrim.notrim,x-gvfs-show,uid=1000,gid=1000 0 0
```
{: file="/etc/fstab" }