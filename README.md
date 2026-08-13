# Android V4L2 Kernel Camera Injection Framework – Open Source Security Research (2026)

[![License](https://img.shields.io/badge/license-MIT-blue)](LICENSE)
[![Platform](https://img.shields.io/badge/platform-Android%2010+-green)](https://developer.android.com/about/versions/10)
[![Research Status](https://img.shields.io/badge/status-Public_Release-orange)](docs/research.md)
[![GitLab CI/CD](https://gitlab.com/v4l2-framework/android-v4l2-injection-framework/badges/main/pipeline.svg)](https://gitlab.com/v4l2-framework/android-v4l2-injection-framework/-/pipelines)

> 🛑 **This project is for educational purposes only.**  
> The techniques documented may violate local laws or platform ToS.  
> Use at your own risk.

---

## 🧠 What Is This?

A **fully documented proof-of-concept** demonstrating how the Linux kernel's Video4Linux2 (V4L2) subsystem can be exploited to inject arbitrary video frames into Android's camera pipeline — effectively bypassing hardware-backed integrity checks used in financial KYC systems.

This work was presented at *underground security conferences* in late Q3–Q4 2025.

---

## 🧠 Technical Architecture

The attack targets the `VIDIOC_DQBUF` ioctl within the V4L2 driver. This ioctl is responsible for delivering captured frames from the kernel to userspace (HAL). Our kernel module intercepts this call, replaces the buffer contents with pre-rendered frames, and manages cache coherency to avoid detection.

### High-Level Stack
```mermaid
graph LR
    A[Target App] --> B[Camera2 API]
    B --> C[Framework]
    C --> D[HAL]
    D --> E[V4L2 Driver]
    E --> F[Injection Module]
    F --> E