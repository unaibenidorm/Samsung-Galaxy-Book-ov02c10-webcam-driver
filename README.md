# Samsung Galaxy Book3 Ultra Webcam Driver Fix (OV02C10)

This repository provides a patched **DKMS kernel module** for the **OmniVision OV02C10** image sensor. This sensor is used in the **Samsung Galaxy Book3 Ultra** (and potentially other Galaxy Book models) connected via the Intel IPU6 CSI bridge.

## 🎯 The Problem

Running Linux on the Galaxy Book3 Ultra presents two specific challenges regarding the webcam:

1.  **Unsupported Clock Frequency:** Samsung's hardware implementation uses a **26 MHz** external clock frequency. The mainline Linux kernel driver strictly checks for standard frequencies (like 19.2 MHz) and fails to probe, resulting in the following error in `dmesg`:
    ```text
    ov02c10 i2c-OVTI02C1:00: error -EINVAL: external clock 26000000 is not supported
    probe with driver ov02c10 failed with error -22
    ```

2.  **Deprecated Kernel APIs:** On very recent kernels (such as **6.12+** and the **6.17+** series found in Fedora Rawhide), functions like `devm_v4l2_sensor_clk_get` have been removed or refactored, causing compilation failures with the standard driver source.

## 💡 The Solution

This patchset addresses both issues:
* It whitlists the **26 MHz** frequency in the driver's supported clock rates.
* It updates the source code to use modern kernel clock APIs (`devm_clk_get`), ensuring compatibility with bleeding-edge kernels.

## 💻 Compatibility

* **Primary Test System:** **Fedora 43 (Rawhide)** with Kernel 6.17+.
* **General Compatibility:** This fix is kernel-level, not distro-specific. It should work on **Ubuntu, Arch Linux, Debian, OpenSUSE**, and others, provided you have a recent kernel (5.15+) and the correct IPU6 firmware/software stack installed (libcamera, ipu6-drivers).

## 🛠️ Installation

### 1. Prerequisites

Ensure you have **DKMS** (Dynamic Kernel Module Support) and the **kernel headers** for your running kernel version installed.

**Fedora / RHEL / CentOS:**
```bash
sudo dnf install dkms kernel-devel kernel-headers make gcc
