## Installing Genymotion

1. Download and install [Genymotion Desktop](https://www.genymotion.com/product-desktop/download/).
2. Follow the [installation instructions](https://docs.genymotion.com/desktop/Get_started/013_Linux_install/) to install Genymotion in the directory of your choice.
3. Launch Genymotion Desktop and follow the on-screen instruction to create an account (note: personal use is free as of writing this guide).

## Creating & Configuring a Virtual Device

1. Follow the instructions for [creating a new device](https://docs.genymotion.com/desktop/Get_started/014_Basic_steps/#create-a-new-device)
   - NOTE: When selecting the Android version, use 9.0 (Pie) so you can add compatibility for ARM-based games (i.e. most games)
   - If you're not sure what to choose, here's my setup:

     | Form Factor / Type | Name | Processor(s) | Memory Size | VM Heap Size | Resolution | Density |
     | ------------------ | ---- | ------------ | ----------- | ------------ | ---------- | ------- |
     | Phone | Google Pixel 7 Pro | 8 | 6000 MB | 256 MB | 1440 x 2560 (9:16) | 640 - XXXHDPI |

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

With that, your virtual device will now have plenty of extra space to store applications!
