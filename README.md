Linux on TrekStor SurfTab wintron 7.0
=====================================

# Linux 4.12~rc6

## Get Linux 4.12~rc6 from kernel.org

### Fixing timing problems
Add ```clocksource=tsc``` to your kernel cmdline to fix issues with timing, timekeeping and a lot of other time related things

## Installing wireless drivers
By now the rtl8723bs driver made it into the staging area of linux. Just enable the staging driver or use my kernel config from [here](linux4.12-rc6/Kconfig) and it will work out of the box.

The wireless driver seems to be stable and gives me acceptable throughput at around 20 Mbit/s.

## Getting the touchscreen working
The touchscreen driver is now part of the kernel, too. Again, either enable the driver yourself or use my kernel config.

You will still need firmware for it though. The firmware is pretty easy to find eg. [here](https://github.com/onitake/gsl-firmware/blob/master/firmware/linux/silead/gsl1686-surftab-wintron70-st70416-6.fw) will do. Just move it to ```/lib/firmware/silead/```, reboot and you should be ready to go.


# Linux 4.4.0
## Get Linux 4.4.0 from kernel.org
### Fixing timing problems
Add ```clocksource=tsc``` to your kernel cmdline to fix issues with timing, timekeeping and a lot of other time related things

## Installing wireless drivers
1. Get driver for SDIO WiFi card from ```https://github.com/hadess/rtl8723bs```
2. Apply patches from 'patches' directory of driver to kernel
3. Build and install the driver

Theoretically this card also supports Bluetooth via hciattach but I couldn't get it working. Supposably [this](https://github.com/lwfinger/rtl8723bs_bt) driver works but as I said I had no luck with it. More precisely I couldn't get the module to talk to me via the serial bus. I'm always getting

```
Realtek Bluetooth init uart with init speed:115200, final_speed:115200, type:HCI UART H5
Realtek Bluetooth :Realtek hciattach version 2.5
Realtek Bluetooth :3-wire sync pattern resend : 1, len: 8
Realtek Bluetooth :3-wire sync pattern resend : 2, len: 8
Realtek Bluetooth :3-wire sync pattern resend : 3, len: 8
...
```

## Getting the touchscreen working
1. Get touchscreen driver from ```https://github.com/onitake/gslx680-acpi```
2. Run ```./fwtool -c GSL_TS_CFG_NEW_WINTRON_70.h -2 -m 1680 -w 1024 -h 600 -t 10 /lib/firmware/silead_ts.fw``` to convert and install the firmware for the touchscreen driver
3. Build and install the driver
4. By default the touchscreen is not calibrated so you will have to calibrate the touchscreen using xinput_calibrator.

# TODO
## Sound
The sound chip on this SoC is a rt5651 which seems to be somewhat supported in the newest kernel git 4.5-rc1+ but it still doesn't work on the wintron though because of some missing DAIs. @Manawyrm tested a recent kernel. You can take a look at the bootlog [here](https://gist.github.com/Manawyrm/70d90e95e9c578a7fb26). We wrote an ALSA mailinglist post about this. You can view it [here](http://mailman.alsa-project.org/pipermail/alsa-devel/2016-January/103696.html)

Upate:
I compiled a new kernel with a [patch](http://www.spinics.net/lists/alsa-devel/msg45910.html) provided by an Intel developer (thanks Pierre!) and now we are getting somewhere. You can find the new logs [here] (https://github.com/TobleMiner/wintron7.0/tree/master/logs/4.5.0-rc2-ARCH-tsys-audio-intel-dirty)
There are still some errors but the compress DAI is now properly initialized. But as soon as I try to use ALSA the intel_sst_acpi driver fails to download required firmware to the audio chipset. We got in touch with the people on alsa-devel again and are now waitig for a response.

## Bluetooth
Look above @Installing wireless drivers

## Camera
The camera is of very low quality and I've not looked into getting it working yet. It doesn't look like the camera is a USB camera thus getting it working might be pretty hard and is of low priority to me.

## Power management
While the battery is being detected there is zero information about the status of the battery. Unluckily the same applies for external power input.
