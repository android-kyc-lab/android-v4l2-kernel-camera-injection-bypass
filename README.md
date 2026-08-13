# Android V4L2 Kernel Camera Injection Bypass – Open Source Security Research (2026)

[![License](https://img.shields.io/badge/license-MIT-blue)](LICENSE)
[![Platform](https://img.shields.io/badge/platform-Android%2010+-green)](https://developer.android.com/about/versions/10)
[![Research Status](https://img.shields.io/badge/status-Public_Release-orange)](docs/research.md)
[![GitLab CI/CD](https://gitlab.com/android-v4l2-kernel-camera-injection-bypass/badges/main/pipeline.svg)](https://gitlab.com/android-v4l2-kernel-camera-injection-bypass/-/pipelines)

> 🛑 **This project is for educational purposes only.**  
> The techniques documented may violate local laws or platform ToS.  
> Use at your own risk.

## 🧠 What Is This?

A **fully documented proof-of-concept** demonstrating how the Linux kernel's Video4Linux2 (V4L2) subsystem can be exploited to inject arbitrary video frames into Android's camera pipeline — effectively bypassing hardware-backed integrity checks used in financial KYC systems.

This work was presented at *underground security conferences* in late Q3–Q4 2025.

## 🔍 Verified Platforms Affected

| Category        | Example Apps |
|------------------|-------------|
| Cryptocurrency   | Binance • Coinbase • Kraken • Bybit • BitPay |
| Fintech Banking | Revolut • Nexo • Monese • Starling • N26 |
| Payment Processors | Stripe • PayPal Zettle • Square |

Full details in [Research Paper](docs/research.md).

## 📚 Documentation Hub

👉 [https://android-v4l.gitlab.io/android-v4l2-kernel-camera-injection-bypass/](https://android-v4l.gitlab.io/android-v4l2-kernel-camera-injection-bypass/)

## ❗ Disclaimer

This project does **not endorse or support illegal activity**. By accessing this repo, you agree not to misuse this knowledge.

## 🤝 Contributing

We welcome pull requests improving documentation. Contact us via PGP key `0xDEADBEEFCAFEF00D`.

---

*Maintained by the V4L2 Research Group.*