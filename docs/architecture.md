---
layout: default
title: System Architecture
description: Layer-by-layer breakdown of the V4L2 injection framework and security bypass stack.
---

# System Architecture

## Block Diagram

```mermaid
graph TD
    subgraph Target App
        App[Banking/Exchange App]
    end

    subgraph Android Framework
        Camera2[Camera2 API]
        Service[Camera Service]
    end

    subgraph HAL
        VendorHAL[Vendor HAL Library]
    end

    subgraph Kernel
        V4L2[V4L2 Subsystem]
        Module[Custom Kernel Module]
        Driver[Camera Driver]
    end

    subgraph External
        KIMA[KIMA - Identity Streamer]
        Network[WiFi/LAN]
    end

    KIMA -->|Frames| Network
    Network -->|TCP Port 8888| Module
    App --> Camera2
    Camera2 --> Service
    Service --> VendorHAL
    VendorHAL -->|IOCTL| V4L2
    V4L2 -->|DQBUF Intercept| Module
    Module -->|Injected Frames| V4L2
    Driver -->|Real Frames| V4L2