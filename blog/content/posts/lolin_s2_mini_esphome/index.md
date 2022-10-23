---
title: "ESPHome on a WeMos / Lolin S2 mini"
date: 2022-10-23T02:22:45+01:00
draft: false
---

# TL;DR
To successfully program an S2 mini with ESPHOME,

use the following starter yaml and build your firmware:

```yaml
esphome:
  name: s2minitest

esp32:
  board: lolin_s2_mini
  variant: esp32s2
  framework:
    type: esp-idf
    version: recommended
    platform_version: 4.4.0
```

and then upload it using esptool:

```ps
esptool --port <PORT> --baud 1000000 write_flash -z 0x0 firmware-factory.bin
```


# Why
I recently got some S2 mini boards, and wanted to use them in an esphome project. I however quickly realized that wasn't going to be as easy as I had hoped. 

## Board support
The first issue I ran in to, is that ESPHome at the time of writing use the 3.5 version of the esp32 platform. Support for the lolin S2 mini was added in 4.3. 

I dug around a bit and found the `platform_version` config, and set that to `4.4.0` leading to the next issue

***I specifically use 4.4.0, as 5.2.0 failed to link the binary file***

## Toolchain
The next (rather annoying :( ) issue I ran in to is that currently the toolchain to compile for the esp32s2 chips isn't supported on the arm architecture used by the raspberry pi. 
This means you have to set up and use esptool on a different non-arm computer. I tried getting it to work in windows, but ran in to some issues with libraries/dependencies not properly downloading, and so I moved to docker/linux

Refer to [the esphome documentation on how to use this with docker](https://esphome.io/guides/getting_started_command_line.html)

This should allow you to build a binary, and once done will likely error that OTA failed, but before that should've generated a binary file in `<the folder you have your yaml in>/.esphome/build/<device_name>/.pioenvs/<device_name>/firmware-factory.bin`

Crucially, the output says: `Wrote <some amount of> bytes to file <...>/firmware-factory.bin, ready to flash to offset 0x0`

so it has to be flashed to 0x0

## Flashing

Esptool usually flashes to 0x1000, but this firmware has to be flashed to 0x0. *Knowing* this, it's easy to flash the firmware to the right offset, but it took me quite a while to remember how to read, and see that :P

to flash you first put the board into upload mode by holding the `0` button and pressing the reset button. The board should then show up as a serial port

Then run:

```ps
esptool --port <serial port> --baud 1000000 write_flash -z 0x0 ./firmware-factory.bin
```

important thing here is the offset of 0x0.

Remember to replace the `<serial port>` with the port your board shows up as, and provide the right path to the firmware binary


This hopefully can help someone out there :) 