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

## Increasing Data Storage Capacity

By default, the data storage capacity of the images provided by Genymotion are quite small and will run out after installing even a single game. To work around this, you need to increase the size of the data partition for your virtual device.

1. If the virtual device is running, shut it down (just hit the close window X, Genymotion takes care of gracefully shutting down the device)
2. Navigate to the directory containing the deployed data of your virtual device:

     ```
     cd ~/.Genymobile/Genymotion/deployed/<Device Name>
     ```

