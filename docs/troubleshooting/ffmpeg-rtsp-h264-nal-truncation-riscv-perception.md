# FFmpeg RTSP H.264 NAL‑Unit Truncation Issue on RISC‑V Road‑Side Perception Unit
> Troubleshooting note for EnerTraffic radar‑vision all‑in‑one roadside perception unit

## Overview
During field‑site debugging of our radar‑vision roadside perception device based on RISC‑V vision SoC, we ran into a subtle interoperability problem with H.264 RTSP streaming.

When pulling RTSP video stream from our custom embedded RTSP server using FFmpeg‑based video capture, we observed random stream corruption and garbled video frames. Some decoders played the stream normally, while others showed broken output.

## Root Cause
FFmpeg RTSP demuxer fails to correctly process H.264 streams served by our embedded custom RTSP server. It randomly truncates incoming NAL units. Truncated NAL units result in corrupted frames on decoder side.

This issue does not occur across all media players, which makes it hard to reproduce and diagnose.

## Workaround
The capture code originally used `cv2.VideoCapture` from OpenCV, which depends internally on FFmpeg for RTSP streaming.

We replaced the FFmpeg‑based capture backend:
> Switch from OpenCV `cv2.VideoCapture(FFmpeg)` → libVLC with live555 demuxer.

After switching to libVLC / live555 for RTSP receiving, the NAL‑truncation symptom disappeared completely, and video streams became stable.

## Key observation & reminder
This is a typical interoperability pitfall between custom embedded RTSP server implementations and different RTSP demuxer libraries.

If you develop traffic‑sensing embedded devices with self‑implemented RTSP streaming, pay attention to cross‑demuxer compatibility testing. Different demuxers may behave differently against the same H.264 stream.

---
**Tags**: `EmbeddedVision` `RTSP` `H264` `RISC‑V` `RadarVision` `VideoStreaming` `TrafficPerception`
