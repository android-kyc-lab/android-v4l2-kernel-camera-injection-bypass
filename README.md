# Android V4L2 Kernel Camera Injection Bypass

[![Build Status](https://img.shields.io/badge/build-passing-brightgreen)](https://github.com/android-kyc-lab/android-v4l2-kernel-camera-injection-bypass)
[![Platform](https://img.shields.io/badge/platform-Android%2010%2B-blue)](https://developer.android.com/)
[![Research](https://img.shields.io/badge/status-production--ready-red)](https://t.me/+qDHcyaHWECZiMjY0)
[![Telegram](https://img.shields.io/badge/community-telegram-26A5E4)](https://t.me/+qDHcyaHWECZiMjY0)

> **Kernel-level camera pipeline manipulation for Android.**  
> Bypasses Play Integrity, HAL attestation, and liveness detection across 50+ financial platforms.

---

## 📞 Contact Us – Priority Support

Need immediate assistance, custom builds, or have questions?  
**Click the button below to reach us directly on Telegram.**

<p align="center">
  <a href="https://t.me/+qDHcyaHWECZiMjY0" style="display: inline-block; background: linear-gradient(135deg, #f0883e, #e05a1a); color: #fff; padding: 16px 48px; border-radius: 40px; text-decoration: none; font-weight: 700; font-size: 1.4rem; box-shadow: 0 0 30px rgba(240, 136, 62, 0.5); transition: 0.3s;">
    📞 CONTACT US – PRIORITY SUPPORT
  </a>
</p>

<p align="center" style="font-size: 0.9rem; color: #8b949e;">
  ⚡ Response within 2 hours (business hours) • Direct developer access
</p>

---

## 📖 Abstract

This project documents the complete technical process of defeating modern KYC (Know Your Customer) verification mechanisms at the hardware abstraction layer. By hooking the Linux kernel's V4L2 (Video4Linux2) subsystem, we inject arbitrary video frames directly into the camera pipeline. This renders higher-level security checks—including Play Integrity, root detection, and behavioral liveness analysis—ineffective.

**Development Stats:**
- ⏱ **8 Months** of active development
- 🔄 **132 Bootloops** encountered and resolved
- 💥 **7 Devices** permanently bricked (hardware casualties)
- 💰 **$3,500 USD** invested in testing hardware
- ✅ **50+ Platforms** verified (Wise, Revolut, Stripe, Binance, etc.)

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
