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

By default, the data storage capacity of the images provided by Genymotion are quite small and will run out after installing even a single game. To work around this, you need to increase the size of the data partition for your virtual device.

1. If the virtual device is running, shut it down (just hit the close window X, Genymotion takes care of gracefully shutting down the device)

## Expanding the Data Partition

Once the maximum size of `data.qcow2` has been increased, we need to expand the data partition in the disk image to enable it to use that newly added space.

1. Navigate to the directory containing the deployed data of your virtual device
   ```bash
   cd ~/.Genymobile/Genymotion/deployed/<Device Name>
   ```
1. Enable the NBD kernel module
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
1. Set the `New size (MiB)` property to the size you expanded `data.qcow2` to be (i.e. 50000 MiB if you used the suggested size), set the `Label` property to `data` and click `Add`
1. Click the green check mark near the top of the GParted window to `Apply all operations` to the block device
   - Note: GParted may raise more `Can't have a partition outside the disk!` errors, just the `Ignore` button on each pop-up
1. Close GParted

With that, your virtual device will now have plenty of extra space to store applications!
