---
# ===== Required =====
title: "Run Steam Games from Windows drive (NTFS) on Linux (Ubuntu)"
date: 2025-12-16 22:00:00 -0600

# Max 2 levels only
categories: [How-to, PC]

# Flat list, keep consistent spelling
tags: [Steam, Linux, Windows, Games]

# ===== Chirpy Features =====
toc: true           # Table of contents
math: true          # LaTeX equations
mermaid: true       # Flow diagrams
pin: false          # Pin to homepage (true/false)

---

## Background
In recent updates, Microsoft brought a bunch of new AI features to Windows 11. They are not optional, take a ton of storage and somewhat make windows feel less stable and more bloated. I think Windows is moving toward to become a giant invasive data collecting and ads feeding software than being an Operating System. Not a welcome change in my opinion.

I decided to dual booting Ubuntu 24.04.3 LTS alongside Windows 11 24H2, and gradually migrate away from Windows. One reason why I stick with Windows is the variety of games supported on the platform. But thank to Valve and the advancement of compatibility layer [Proton](https://en.wikipedia.org/wiki/Proton_(software)), many Windows games could be run flawlessly on Linux, high time to try gaming on this platform.

## Overview
I have 3 different drives, one for Ubuntu, one for Windows, and one for Steam Games Library (the drive is [NTFS](https://en.wikipedia.org/wiki/NTFS) formatted)

I had:
- Successfully installed Steam with:  ```sudo apt install steam```
- Successfully had my games on NTFS drive recognized by Steam.
- Successfully had Steam automatically install and setup runtime and Proton.

Unfortunately, none of my game would launch out of the box...

## What is the problem?
It turns out the NTFS drive is mounted to Ubuntu through [ntfs-3g](https://en.wikipedia.org/wiki/NTFS-3G) driver. Although ntfs-3g provides read and write permission to NTFS partition, it may not have fully support Linux filesystem semantics, including:
- Executable (+x) permission bits
- POSIX-compliant symbolic links
- Case-sensitive paths

Proton requires these features to run Windows games on Linux. The missing Linux semantics may cause Proton to fail to launch the game.

## What is the fix?
We want all executed files on an [EXT4](https://en.wikipedia.org/wiki/Ext4) (Linux native) partition, while keeping large game assets on NTFS.
We can redirect Steam’s NTFS path to Steam's EXT4 path for execution via a [symlink](https://www.freecodecamp.org/news/symlink-tutorial-in-linux-how-to-create-and-remove-a-symbolic-link/).

### Identify Steam's paths
First we need to search for ```libraryfolders.vdf```, the file contains info on locations of game library folders, by running this command:

    find ~/ -type f -name "libraryfolders.vdf"

![Terminal Screenshot 2](assets/img/find_steam_lib_config.png)

Pick the one under ```config``` folder, then use following command: 
    
    cat ~/.steam/debian-installation/config/libraryfolders.vdf

![Terminal Screenshot 2](assets/img/steam_lib_paths.png)

- The Steam's EXT4 path is: ```/home/tony/.steam/debian-installation```
- The Steam's NTFS path is: ```/mnt/9CE8D1DBE8D1B3AC/SteamLibrary```

### Create Symlink

Before create symlink, remove the existing compatdata folder on NTFS (you may want to make a back up before remove):

    rm -rf /mnt/9CE8D1DBE8D1AC/SteamLibrary/steamapps/compatdata

Then create the symlink:

    sudo ln -s "/home/tony/.steam/debian-installation/steamapps/compatdata" "mnt/9CE8D1DBE8D1B3AC/SteamLibrary/steamapps"

Now when Steam tries to access NTFS path: ```/mnt/9CE8D1DBE8D1AC/SteamLibrary/steamapps/compatdata/<AppID>```. The Linux kernel sees that compatdata is a symlink. It automatically redirects access to the EXT4 path: ```/home/tony/.steam/debian-installation/steamapps/compatdata/<AppID>```. Proton then executes all files on EXT4, where executable permissions, POSIX symlinks, and case-sensitive paths work correctly. NTFS now contains only the symlink, so its filesystem limitations no longer interfere with game execution.


A running game demo:

![Showcase Screenshot](assets/img/ubuntu_death_stranding_screen_shot.png)