---
title: Testing
date: 2024-11-30 13:18 -0500
categories:
  - blogging
tags:
  - rant
toc: false
---
# This is a header

This should create a markdown file in the directory
The first few lines should show above the fold as the description

## This is a smaller header
Most of the markdown should work between Obsidian and GHA I hope

This is a cool set of snippet expansion
` ```shell 
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
{: file="bin/borg.sh" }
