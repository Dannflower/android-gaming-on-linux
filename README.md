# Android Gaming on Linux

The following guide will walk you through using Genymotion to configure an Android emulator for playing games (and using general apps) within a Linux desktop environment. This guide will probably be of particular interest to folks with Nvidia GPUs as Genymotion supports Nvidia GPU acceleration under Wayland.

I wrote this with the following system configurations:
- Ubuntu 24.04 / GNOME 46
- Fedora 40 / GNOME 46

## Disclaimer

The information provided in this guide is for educational purposes only. Emulation is not always 100% stable, and software crashes may occur, potentially resulting in data loss. I strongly suggest using this tool only for apps and games where crashes or data loss would not cause significant loss to yourself or others. I am not responsible for any data loss, damage, or other consequences that may result from following this guide. Proceed at your own risk.

Hopefully this guide saves you some time going down the rabbit hole of Android gaming on Linux!

### ⚠️ About `adb` ⚠️

This guide makes use of a tool called `adb` which could be used to automate anything in the emulator should you be crafty enough.

DO NOT DO THIS IN GAMES.

Any form of automation in games could potentially get your account flagged for botting and eventually suspended or even permanently banned. Use `adb` to launch your apps and then leave it at the door or proceed at your own risk.

## Table of Contents

1. [Installing Genymotion](#installing-genymotion)
1. [Creating & Configuring a Virtual Device](#creating--configuring-a-virtual-device)
1. [Adding Support for ARM-Based Games](#adding-support-for-arm-based-games)
1. [Installing Apps & Games](#installing-apps--games)
1. [Desktop Shortcuts & App Specific Launchers](#desktop-shortcuts--app-specific-launchers)
1. [Increasing Device Storage](#increasing-device-storage)
   - [Resizing the Disk Image for Additional Storage](#resizing-the-disk-image-for-additional-storage)
   - [Expanding the Data Partition](#expanding-the-data-partition)
1. [Troubleshooting](#troubleshooting)


## Installing Genymotion

1. Download and install [Genymotion Desktop](https://www.genymotion.com/product-desktop/download/).
2. Follow the [installation instructions](https://docs.genymotion.com/desktop/Get_started/013_Linux_install/) to install Genymotion in the directory of your choice.
   - For simplicity, this guide installs Genymotion in: `~/bin`
4. Launch Genymotion Desktop and follow the on-screen instruction to create an account (note: personal use is free as of writing this guide).

## Creating & Configuring a Virtual Device

1. Follow the instructions for [creating a new device](https://docs.genymotion.com/desktop/Get_started/014_Basic_steps/#create-a-new-device)
   - NOTE: When selecting the Android version, use 9.0 (Pie) so you can add compatibility for ARM-based games (i.e. most games)
   - If you're not sure what to choose, here's my setup:

     | Form Factor / Type | Name | Processor(s) | Memory Size | VM Heap Size | Resolution | Density |
     | ------------------ | ---- | ------------ | ----------- | ------------ | ---------- | ------- |
     | Phone | Google Pixel 7 Pro | 8 | 6000 MB (Setting this too close to your actual system RAM capacity might make your system unstable, leave headroom!) | 256 MB | 1440 x 2560 (9:16) | 640 - XXXHDPI |

3. [Launch the virtual device](https://docs.genymotion.com/desktop/Get_started/014_Basic_steps/#launch-a-device)
4. [Install Open GApps](https://docs.genymotion.com/desktop/04_Emulating_sensors_and_features/#open-gapps)

## Adding Support for ARM-Based Games

If you are running your Linux OS on a non-ARM processor such as Intel or AMD, then you'll need to install a translation package in your virtual device.

1. Start your virtual device

1. Download the [Genymotion ARM Translation for Android 9.0](https://github.com/m9rco/Genymotion_ARM_Translation/blob/master/package/Genymotion-ARM-Translation_for_9.0.zip)
   - For older versions, see the list [here](https://github.com/m9rco/Genymotion_ARM_Translation)
   - Support for newer versions of Android is limited

1. With your virtual device running, drag the `Genymotion-ARM-Translation_for_9.0.zip` into the emulator window
   
1. Accept Genymotion's prompt to flash the archive

## Installing Apps & Games

With Open GApps installed, you'll now find the Play Store in your list of installed applications in your virtual device. Log into the Play Store with your Google account and you should be able to install any apps and games you want.

   - Note: Not seeing a game or app listed in the store? Check the following:
     
      - Make sure you've installed the ARM translation package as described above, otherwise the Play Store will hide ARM based games as they will be incompatible with your virtual device.
        
      - Are you running a phone-based virtual device or a tablet-based device? Not all apps & games are compatible with both form factors and the Play Store will hide the apps from you.

## Desktop Shortcuts & App Specific Launchers

If you want to be able to launch your virtual device directly into a specific app from your desktop environment, follow the steps below. We'll use Arknights for educational purposes just to demonstrate how you could set this up for any app of your choosing.

1. For Gnome users, you can create a new `.desktop` file in your home directory (we'll use Arknights as an example)
   
   ```bash
   touch ~/.local/share/applications/arknights-genymotion.desktop
   ```
   
1. Populate the `.desktop` file with the following text (replacing <user> with your own user name)
   
   ```
   [Desktop Entry]
   Encoding=UTF-8
   Version=1.0
   Type=Application
   Name=Arknights
   GenericName=Arknights Android Emulator
   Icon=/home/<user>/Pictures/Icons/arknights.webp
   Exec=/home/<user>/Scripts/arknights.sh
   Categories=Development;Emulator;Game;
   ```
   - Note: You'll need to supply the icon yourself (or just reference an existing icon on your system if you prefer)
   
1. In your home directory, add a new directory called `Scripts` and navigate to it
   ```bash
   mkdir ~/Scripts
   cd ~/Scripts
   ```

1. In the `Scripts` directory create a new script

   ```bash
   touch arknights.sh
   ```

1. Populate the script with the following text

   ```bash
   # !/bin/bash
   
   set -e
   
   ~/bin/genymotion/gmtool admin start "Google Pixel 7 Pro"
   
   # Launch the app directly by starting the context by name
   ~/bin/genymotion/tools/adb shell am start -n com.YoStarEN.Arknights/com.u8.sdk.U8UnityContext
   
   # Alternatively, if you don't know the context, you can simulate a tap on the screen for where you have placed the app's icon
   # The sleep is added to allow the virtual device to finish initializing so the tap actually registers correctly
   #sleep 2
   #~/bin/genymotion/tools/adb shell input tap 720 1280
   
   # Rotate the screen to whatever orientation you prefer for this specific app
   ~/bin/genymotion/genyshell -c "rotation setangle 90"
   ```
   #### Notes:
   - If you installed Genymotion into a different path, be sure to update the script to reflect your installation location.
   - Personally, I'm a fan of the tap approach as various apps/games *might* be able to detect that you launched the game via `adb` or some other automation tool and even though you're not using it maliciously, the app might defensively flag your account over zealously.

### ⚠️ About `adb` ⚠️

As you may have inferred from the script, `adb` could be used to automate anything in the emulator should you be crafty enough. DO NOT DO THIS IN GAMES. Any form of automation in games, like Arknights, can get your account flagged for botting and eventually suspended or even permanently banned. Use `adb` to launch your apps and then leave it at the door or proceed at your own risk.

## Increasing Device Storage

By default, the data storage capacity of many of the images provided by Genymotion are quite small and will run out of space after installing even a single game. To work around this, you need to increase the size of the data partition for your virtual device. There are a few device images with larger starting storage sizes (i.e. 64 GB), but even that may be insufficient if you want to install more than a few games.

If you find your device is running out of memory, follow the steps below.

### Resizing the Disk Image for Additional Storage

1. If the virtual device is running, shut it down (just hit the close window X, Genymotion takes care of gracefully shutting down the device)
   
1. Navigate to the directory containing the deployed data of your virtual device

   ```bash
   cd ~/.Genymobile/Genymotion/deployed/<Device Name>
   ```
   
1. Resize the disk image to the maximum size you want your virtual device's storage to be able to use (i.e. 50 GB in this example)

   ```bash
   qemu-img resize data.qcow2 50G
   ```
   - Note: `data.qcow2` will not immediately become the size you select, instead it will slowly grow up to this maximum as your virtual device saves data to the disk image as it is [thinly provisioned](https://en.wikipedia.org/wiki/Thin_provisioning)

### Expanding the Data Partition

Once the maximum size of `data.qcow2` has been increased, we need to expand the data partition in the disk image to enable it to use that newly added space.

1. Navigate to the directory containing the deployed data of your virtual device

   ```bash
   cd ~/.Genymobile/Genymotion/deployed/<Device Name>
   ```

1. Enable the `nbd` kernel module

   ```bash
   sudo modprobe nbd max_part=10
   ```
   
1. Mount the block device

   ```bash
   sudo qemu-nbd -c /dev/nbd0 data.qcow2
   ```

1. Install GParted if needed:

   ```bash
   sudo apt update
   sudo apt install -y gparted
   ```

1. Open the block device in GParted

   ```bash
   gparted /dev/nbd0
   ```
   - Note: If GParted opens with the error: `Can't have a partition outside the disk!` just click the `Ignore` button

1. Right-click the partition with the label `data` (most likely `/dev/nbd0p3`) and select `Delete`
      - ⚠️ Note: This will delete any data in the partion! If you have data in the partition you want to keep, you can try simply resizing the partition instead and skipping to step 10. Just remember, there is always a chance for data loss when altering partitions, so back up anything you don't want to lose regardless of how you adjust the partition.
   
1. Right-click the `unallocated` partition and select `New`
   
1. Configure the new partition with the following properties
   - Set `New size (MiB)` to be the size you expanded `data.qcow2` to be (i.e. 50000 MiB if you used the suggested size)
   - Set `Label` to be `data`
     
1. Click `Add`
   
1. Click the green check mark near the top of the GParted window to `Apply all operations` to the block device
   - Note: GParted may raise more `Can't have a partition outside the disk!` errors, just the `Ignore` button on each pop-up
     
1. Close GParted
   
1. Unmount the block device

   ```bash
   sudo qemu-nbd -d /dev/nbd0
   ```
   
1. (Optional) Unload the `nbd` kernel module

   ```bash
   sudo modprobe -r nbd
   ```
   
With that, your virtual device will now have plenty of extra space to store applications! This process can be repeated later if you find that your device needs more storage space.

## Troubleshooting

### Startup script not working / Genymotion thinks device is booting endlessly

If you create a startup script and it doesn't seem to execute (especially if you are using ADB/gmtool and you notice that in the Genymotion menu it says your device is "booting" despite it having already launched, you may need to [enable USB debugging](https://developer.android.com/studio/debug/dev-options).
