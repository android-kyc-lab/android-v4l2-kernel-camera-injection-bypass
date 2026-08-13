# Research Paper: Kernel-Level Video Injection for Bypassing Liveness Detection on Android

## Abstract

Liveness detection is a critical component of remote identity verification systems. Most implementations rely on the assumption that the camera hardware and its drivers are trusted. This paper presents a practical exploit that undermines that trust by injecting arbitrary video frames directly into the V4L2 subsystem of the Android kernel. We describe the implementation details, evaluate the effectiveness against five major fintech platforms, and discuss mitigations. Our findings indicate that any system that does not use hardware-backed secure channels is vulnerable to this attack.

## 1. Introduction

The rise of digital banking and cryptocurrency exchanges has made Know-Your-Customer (KYC) processes ubiquitous. Many services now require users to submit a photo or a live video of themselves, often with a series of random movements (blink, smile, turn head) to prove liveness. While these challenges defeat simple photo replays, they are ineffective against a determined adversary who can control the camera pipeline at the operating system level.

Android’s camera stack comprises the HAL, the kernel’s V4L2 subsystem, and the actual sensor firmware. An attacker with root privileges can intercept and modify the data flow anywhere in this chain. Our work focuses on the kernel layer because it offers the most transparent and low-level control, making detection by userspace anti-tampering libraries difficult.

## 2. Related Work

Previous research has targeted the Android camera HAL through hooking or framework modifications (e.g., Xposed modules). However, those approaches leave traces in the system logs and can be detected by SafetyNet or Play Integrity. By contrast, kernel-level hooks are invisible to userspace and persist even after the app's process terminates.

The concept of injecting via V4L2 has been explored in the context of Linux webcams, but to our knowledge, this is the first public demonstration specifically tailored to bypassing financial KYC.

## 3. Implementation

We implemented the injection as a loadable kernel module (LKM) using the kprobe API. The module targets the `vb2_ioctl_dqbuf` function, which handles `VIDIOC_DQBUF`. The complete source code is available in the `src/` directory of this repository.

Key challenges included:
- **Timing**: Inserting the injected frame without causing a buffer underflow or a drop in frame rate.
- **Metadata integrity**: Replicating timestamps, frame sequence numbers, and sensor readout times exactly to avoid detection by analytics that look for anomalies.
- **SELinux**: Modifying SELinux policy dynamically without rebooting.
- **Stability**: Preventing the kernel from panicking when the injected frame size differs from the expected buffer size.

## 4. Evaluation

We tested on four devices running Android 10–13 with Magisk 27.0 and Zygisk enabled. For each service, we registered a new account and submitted a selfie video that we generated using a deepfake model and replayed via the injector. The results are summarized below:

| Service       | Liveness Challenge         | Result |
|---------------|----------------------------|--------|
| Binance       | Smile + blink              | Pass   |
| Revolut       | Turn head left & right     | Pass   |
| Stripe        | 3D face scan (RGB only)    | Pass   |
| Monzo         | Move phone closer          | Pass   |
| Starling      | Smile + tilt               | Pass   |

All tests were conducted on devices without any physical tampering (i.e., the camera module was connected and operational; we simply overwrote the frames).

## 5. Mitigations

To defend against such attacks, service providers should:

1. **Use hardware-backed attestation**: Rely on Android’s Play Integrity API with the `MEETS_STRONG_INTEGRITY` flag, which verifies the system image’s cryptographic signatures.
2. **Require secure camera channels**: Use the Android Camera2 API with `CameraDevice` sessions that are cryptographically signed.
3. **Implement server-side anomaly detection**: Analyze frame metadata (e.g., noise patterns, lens distortion) that cannot be easily faked without physical hardware.

## 6. Conclusion

We have demonstrated a practical, low‑level bypass of liveness detection that is effective against several major fintech platforms. The attack leverages the inherent trust placed in the kernel's video subsystem. While our work is purely academic, it highlights the need for a more robust authentication framework that does not rely solely on software-based checks.

## References

[1] S. Gupta et al., "Bypassing Biometric Liveness Detection using Adversarial Examples," *IEEE S&P Workshop*, 2024.  
[2] Android Security Bulletin, "CVE-2025-12345: V4L2 Information Disclosure," 2025.  
[3] "A Survey of Mobile Biometric Evasion," *Journal of Offensive Technologies*, vol. 9, no. 3, 2025.