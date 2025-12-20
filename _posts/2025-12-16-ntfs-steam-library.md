---
# ===== Required =====
title: "Run Steam Games on a NTFS disk with Linux"
date: 2025-12-16 22:00:00 -0600

# Max 2 levels only
categories: [How-to, PC]

# Flat list, keep consistent spelling
tags: [Steam, Linux, Windows, Games]

# ===== Chirpy Features =====
toc: true           # Table of contents
math: false          # LaTeX equations
mermaid: false       # Flow diagrams
pin: false          # Pin to homepage (true/false)

---
## Introduction
This blog contains diagnose and fix for Steam games stored on a NTFS formatted drive/disk that failed to launch on Linux.

The fix also allow the same Steam games be run in both Linux and Windows (for dual booting system) without reinstalling the games.

## Background
In recent updates, Microsoft brought a bunch of new AI features to Windows 11. They are not optional, take a ton of storage and somewhat make windows feel less stable and more bloated. I think Windows is moving toward to become a giant invasive data collecting and ads feeding software than being an Operating System. Not a welcome change in my opinion.

I decided to dual booting Ubuntu 24.04.3 LTS alongside Windows 11 24H2, as an effort to gradually migrate away from Windows. One reason why I stick with Windows is the variety of games supported on the platform. But thank to Valve and the advancement of compatibility layer [Proton](https://en.wikipedia.org/wiki/Proton_(software)), many Windows games could be run flawlessly on Linux. Time to try them out.

## Overview
I have 3 different physical drives in my PC, one for Ubuntu, one for Windows, and one for Steam games library. I was using Windows when I set up the Steam library drive and so the drive is formatted with Windows' default NTFS.

During set up, I:
- Successfully installed native Steam with:  ```sudo apt install steam```
- Successfully had Steam recognize my game drive.
- Successfully had Steam install and setup Proton and other necessary programs.
- Successfully get the latest NVIDIA driver installed.

Unfortunately, none of my game would launch ...

## What is the problem?
[NTFS](https://en.wikipedia.org/wiki/NTFS) is a native Windows filesystem and enforces Windows naming rules. Windows and Linux use different filename conventions. When Linux creates files or folders that are invalid under Windows naming rules and stores them on a filesystem like NTFS, those files cannot be represented correctly. This can lead to read/write errors or application failures.

Steam runs Windows games on Linux through Proton. Proton stores its runtime and configuration files in a folder called ```compatdata```. Because my Steam game library is located on an NTFS drive, Proton also created the ```compatdata``` directory on the NTFS partition.

As a result, Proton attempted to store Linux-style runtime files on NTFS, which caused filesystem incompatibilities and prevented the game from launching.

## What is the fix?
Instead of letting Proton write ```compatdata``` directly to NTFS, we could use [symlink](https://www.freecodecamp.org/news/symlink-tutorial-in-linux-how-to-create-and-remove-a-symbolic-link/) to redirect Proton to write to a ```compatdata``` on Linux [EXT4](https://en.wikipedia.org/wiki/Ext4) partition.

Then do the following **4 steps**.

### 1. Identify Steam's paths
First we need to search for ```libraryfolders.vdf```, the file contains info on locations of game library folders, by running this command:

    find ~/ -type f -name "libraryfolders.vdf"

![Terminal Screenshot 2](assets/img/find_steam_lib_config.png)

Pick the one under ```config``` folder, copy it and then use following command: 
    
    cat <path to libraryfolders.vdf>
    
![Terminal Screenshot 2](assets/img/steam_lib_paths.png)

- The Steam's EXT4 path is: ```/home/tony/.steam/debian-installation```
- The Steam's NTFS path is: ```/mnt/9CE8D1DBE8D1B3AC/SteamLibrary```

### 2. Remove NTFS compatdata folder
Run this command (you may want to make a back up before remove)

    rm -rf <Steam's NTFS path>/steamapps/compatdata

In my case for example:

```rm -rf /mnt/9CE8D1DBE8D1AC/SteamLibrary/steamapps/compatdata```

### 3. Create EXT4 compatdata folder
Run this command:

    mkdir -p <Steam's EXT4 path>/steamapps/compatdata

In my case for example:

```mkdir -p /home/tony/.steam/debian-installation/steamapps/compatdata```


### 4. Create Symlink
Run this command:

    sudo ln -s "<Steam's EXT4 path>/steamapps/compatdata" "<Steam's NTFS path>/steamapps"

In my case for example:

```sudo ln -s "/home/tony/.steam/debian-installation/steamapps/compatdata" "mnt/9CE8D1DBE8D1B3AC/SteamLibrary/steamapps"```

## Conclude
That is it, the games should be launching. Now when Steam tries to access NTFS path: ```/mnt/9CE8D1DBE8D1AC/SteamLibrary/steamapps/compatdata```. The Linux kernel sees that ```compatdata``` is a symlink. It automatically redirects access to the EXT4 path: ```/home/tony/.steam/debian-installation/steamapps/compatdata```. Proton then executes all files on EXT4 normally. NTFS now contains only the symlink, so its filesystem limitations no longer interfere with game execution.

A running game demo:

![Showcase Screenshot](assets/img/ubuntu_death_stranding_screen_shot.png)