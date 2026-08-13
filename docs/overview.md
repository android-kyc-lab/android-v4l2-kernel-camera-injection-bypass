# Technical Overview

## Introduction

The Video4Linux2 (V4L2) subsystem is the standard kernel interface for video capture and output devices on Linux-based systems, including Android. Android's camera HAL (Hardware Abstraction Layer) relies heavily on V4L2 to communicate with camera sensors and process frames. By intercepting and manipulating the V4L2 ioctl calls at the kernel level, an attacker can inject synthetic video frames into the pipeline, effectively replacing the real camera input with arbitrary content (e.g., a pre-recorded video or a stream from OBS).

This project documents a proof-of-concept implementation that hooks into the `VIDIOC_DQBUF` and `VIDIOC_QBUF` calls using the kernel's kprobe mechanism. Once hooked, the module can pause the real frame buffer, copy the current frame metadata (timestamps, sensor parameters), and then insert a crafted frame from a DMA‑mapped buffer. The insertion is transparent to userspace applications because the frame structure and timing are preserved perfectly.

## Key Components

- **Kernel Module (`v4l2_inject.ko`)**: The core driver that registers kprobes and performs frame injection.
- **Userspace Controller (`injectctl`)**: A small CLI tool that communicates with the module via a `/dev/v4l2_inject` character device to enable/disable injection and load custom frame data.
- **Frame Source**: Can be a video file (MP4), a networked stream (RTSP), or a capture card.

## Attack Surface

The vulnerability exploited is not a bug in V4L2 itself, but rather the inherent trust the camera HAL places in the kernel to provide authentic sensor data. Because Android's security model assumes that only privileged (root) processes can interact with `/dev/video*` devices, an attacker who gains root access can bypass any software-based liveness detection. This is particularly effective against:

- Face matching that relies solely on RGB images without depth or IR channels.
- Simple liveness checks that do not verify the integrity of the camera pipeline (e.g., checking for motion, eye blinking, or head movement).

## Effectiveness

In our tests (performed on devices with unlocked bootloaders and Magisk), we achieved a **100% bypass rate** against:

- Binance KYC (selfie verification)
- Revolut Identity Check
- Stripe Identity Verification (standard liveness)
- Monzo and Starling Bank’s document + selfie workflows

We have not tested against advanced biometric systems that use infrared, structured light, or 3D depth sensing (e.g., Face ID on iPhone or Android's face unlock on Pixel). However, the underlying principle could theoretically be extended to those if the hardware supports replay of depth data.