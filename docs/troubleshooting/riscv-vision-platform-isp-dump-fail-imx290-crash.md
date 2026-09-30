# RISC‑V Vision Platform: ISP Dump Fail and System Crash With IMX290 Sensor
> Troubleshooting note for our in‑house RISC‑V embedded vision platform

## Overview
On our RISC‑V vision platform, the system runs stably with the OV5647 image sensor.
After switching to the IMX290 sensor, `dump fail` errors appear rapidly, and the system crashes shortly afterwards.

## Initial Suspects & Verification
We initially suspected a hardware clock problem related to the 37 MHz crystal oscillator.
Measurement confirmed the 37 MHz crystal oscillator signal is normal and stable.

We captured raw and YUV data via vicap interface.
Both raw and YUV outputs from vicap are valid and look correct.
This indicates the sensor itself and the vicap receiving path work properly.

## Root Cause
Further troubleshooting narrowed the issue down to the internal ISP module.
Even though vicap receives valid image data from IMX290, incompatibility or mis‑configuration inside the ISP triggers dump failure and eventually causes system crash.

## Key observation & reminder
Different image sensors carry distinct timing characteristics and signal behaviors, even when the physical hardware interface is compatible.

Valid raw / YUV data output from vicap does **not** guarantee the ISP can process the incoming video stream correctly.

When porting a new sensor on this RISC‑V vision platform:
1. Do not rely only on vicap raw/YUV validation.
2. Pay close attention to ISP status and configuration if you meet `dump fail` and unexpected system crash.
3. A working crystal clock does not exclude ISP‑related faults.

---
**Tags**: `RISC‑V` `ISP` `IMX290` `OV5647` `VICAP` `EmbeddedVision` `CameraSensor`
