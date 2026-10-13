---
title: "Sonoff S60 flashing jig"
date: 2024-10-14T14:15:00+02:00
draft: true
author: Mikkel Jeppesen
---

# Introduction

The Sonoff S60 is a power measuring wifi connected socket with switching based on the ESP32-C3 IC.  
It can handle 250V at 16A, for a total power capacitor of 4kW.
Quite impressive in the small footprint that it occupies.

This however comes at a price. It's VERY annoying to take apart for flashing.

[ouinouin](https://github.com/ouinouin) over on github posted
[a long discussion](https://github.com/arendst/Tasmota/discussions/21255) on tasmota, documenting their
experience tearing down an S60, and it was very helpful for my efforts.

First you have to have a long thin screw-driver that can reach into the protective earth hole on the male side of the plug.
at the bottom there's a 1.3mm hex screw, which you need to undo. A torx 6 screw-driver also works.

Once this is removed, the top grey part of the female side of the plug has to be pried off. This is ultra-sonicly welded in spots.

Using a combination of an ifixit metal pryer, and a pair of channellock pliers to squeese around the grey ring to break the welds
this top part then lifts off.

Once this is off, if you want to get any further in, you have to desolder the earthing bar for the female side.
This is fairly simple as it's only soldered in one spot.  
What's less simple is then desoldering the live and neutral conenctions, to remove the PCB. With a good iron, some solderwick,
and some spare time, it however can also fairly simply be removed.

This will leave you with the guts of the plug in your hands, and you should be able to flash it from here.
This however isn't a great experience if you have to do many of these.

# Flashing jig
Looking top down at the partially disassembled plug, you can see the ESP board sitting perpendicularly on the main power board.
{{< imgresize "images/s60-desoldered-in-housing.jpg" "400x" "S60 PCB in desoldered in housing">}}

My hope is that I can design and 3D print two jigs:  
A drill jig that'd let me drill some small ~1mm holes,
exactly above the test points. Hopefully, since the board is spaced a good couple mm from the edge of the housing, this should
also allow the drill to poke through a bit without hitting the PCB.  
And a pogo-pin flashing jig, that'll allow me to easily flash the S60s, without ever taking them apart.


# CAD time

## Test point positioning

First I took a picture of the ESP board with a ruler in the same plane as the bord. I took the picture from a good distance,
centered in the image, to get as flat of an image as I could.  
Then in fusion, this was imported as a canvas, and scaled off of the rulers mm ticks.  
Measuring the test-points on the board with some accurate holes to compare, and with some calipers, I believe they're 1.3mm in 
diameter. With a scaled canvas in F360, this seems to be accurate.

I made a small test print, to verify the testpoint positioning, and printed this. This allows me to check if my assumed positioning
is accurate. 

{{< imgresize "images/f360_test_points.png" "x250" "Fusion 360 screenshot">}}

{{< imgresize "images/hole_test_print.jpg" "x250" "3D printed test card">}}

## 3D placement in housing

The fun part is then figuring out how this is positioned in relation to the housing.
After spending some time in fusion, we have this:
{{< imgresize "images/cad_assembly_1.png" "400x" "Fusion 360 screenshot">}}

This should let me design a fixture that indexes off of the schuko plug, and lets me drill into the housing over the
test points with a depth stop.

{{< imgresize "images/cad_assembly_drill_jig.png" "300x" "3D printed test card">}}
