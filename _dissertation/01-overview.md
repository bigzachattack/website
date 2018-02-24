---
title: "Overview"
slug: "overview"
classes:
 - wide
---

### Abstract ### 
Operating system virtualization tools such as VMWare, XEN, and Linux KVM export only minimally capable SVGA graphics adapters. This paper describes the design and implementation of system that virtualizes high-performance graphics cards of arbitrary design to support the construction of authentic device drivers. Drivers written for the virtual cards can be used verbatim, without special function calls or kernel modifications, as drivers for real cards, should real cards of the same design exist. While this allows for arbitrary design, it is not able to model performance characteristics. We describe a new kernel system that allows for arbitrarily changing the performance of a device. These virtual performance throttles (VPTs) use the framework provided by the virtual device architecture and a simple linear service model a physical drive to simulate the relative performance characteristics of the physical disk. The applications of the system include instruction in device driver and disk scheduler design, allowing device driver design to proceed in parallel with new hardware development, and for relative performance measurements without needing access to the physical device being modeled. 


