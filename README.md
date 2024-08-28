# GHost

> **GHost: G*raphical* Host**


> [!Tip]
> *If you know what you're doing,  
> the ToC is all you need.*

## Overview

### What does it do?

**GHost** is a type-2 hypervisor-workstation (*hyperstation?* 🫣) designed to seamlessly orchestrate multiple PCIe devices (GPU, TPU…), across ad hoc environments (Python, Windows, servers…), in both combined and discrete operations. It can power as many seats as you can fit GPUs (2 is a good maximum on most consumer platforms).

In plain English, it's an always-on single box that virtualizes whatever infrastructure you want to throw at it. This guide offers a number of working examples.  
This notably includes full-fledged "native" GPU-powered workstation VMs for graphical or AI applications, with their own display, keyboard, mouse…

Tested on Kubuntu 24.04 🡪 *should* thus work on most recent Debian-based distros.

> [!Tip]
> There's a headless/CLI variant called **SHost: S*erver* Host**.[^SHost]


### Hardware requirements

1. Exactly **1 PC** (x64 platform)
   - … with a motherboard that supports **IOMMU** virtualization features,
   - … has *enough™* **PCIe ≥ 4.0 lanes** (usually 20 on consumer platforms),
   - … and *enough™* **RAM** & CPU **cores** (I like 64 GB over 16 physical cores)
2. At least **2 GPU** (counting iGPU, if any)
   - … with a chipset/motherboard that sports 
3. NVMe storage (at least for all OS)

**Recommended** for convenience, notably during setup:

- Any solution to display 2 machines concurrently (2 physical displays, some picture-by-picture feature…).
- 2 keyboard-mouse combos. Alternatively, a KVM switch (hardware device), or a Synergy[^synergy] license (software utility, ideal if you have multiple displays).
- A second computer to SSH into the host.


### About this guide

This procedure gets you there *as fast as possible*.

- No talk
- Atomic steps
- Direct links
- One-size-fits-*most!*
   Need variations? 🡪 See [Resources](#resources) for links to docs, repos, guides, discussions…

To make sense of these instructions, and dig deeper, see [`disc.md`](disc.md) — it's my little book about this kind of virtualized infra, and probably contains answers you seek.

> [!Note]
> #### Hardware used
>
> - Chipset: AMD AM5 X670E (req. PCIe ≥ 4.0)
> - DDR5: 64 GB
> - CPU: AMD Ryzen 7950X (16/32 cores)
> - GPU 0: AMD Raphael (integrated, shared memory)
> - GPU 1: Nvidia RTX 3090 (24 GB)
> - NVMe 0: host OS (2 TB)
> - NVMe 1-3: ZFS datasets for VMs & data (12 TB)
>
> **Soon™**
>
> Pick one:
> - TPU: Tenstorrent Wormhole (2×12 GB)
> - GPU: whatever outputs 8K@60Hz (to free the 3090 from graphics duty and run it on compute while still enjoying 8K)
> 
> Nice things:
> - HDD: hot backup (16 TB)



## Setup

> [!Tip]
> Footnote = **Help!**
> 🡪 *If some* `thing`[^footnote] *doesn't work, check out its footnote!*

### Kubuntu



1. Download the **Kubuntu [`.iso`](https://cdimage.ubuntu.com/kubuntu/releases/24.04/release/kubuntu-24.04-desktop-amd64.iso)** file.

1. Download the latest **Balena Etcher [release](https://github.com/balena-io/etcher/releases)** for your *current* OS (where you will flash the `.iso` to USB).

1. Install Etcher.

   ```bash
   sudo apt install ./balena-etcher_******_amd64.deb
   ```

1. Launch it (GUI app in your usual menu).

1. Flash `kubuntu-24.04-desktop-amd64.iso` onto your USB stick.

1. ⚠️ Shutdown the PC and **unplug (physically) all video outputs, except the host's.**  
   *In this guide, the iGPU is dedicated to the host, so at this point we remove any video cable going out of the Nvidia GPU.*[^2]

1. Boot to USB to setup Kubuntu. Most defaults are OK, except:

   1. **Btrfs** on the root partition (`/`) is **required** for some features (local snapshots, remote backups, easy rollback, and more).
   1. Agree to install `virt-manager` and the other thing [???] (not Krita).
   
1. Remove the USB stick when asked to, then press <kbd>Enter</kbd>.
   You'll reboot on the freshly installed system, to be greeted by the KDE welcome wizard.



### Security & Access (1)

1. Upgrade packages.

   ```bash
   sudo apt update
   sudo apt upgrade
   ```

1. Optionally install your browser of choice (I use [Brave](https://brave.com/linux/#debian-ubuntu-mint)).

   ```bash
   sudo apt install curl

   sudo curl -fsSLo /usr/share/keyrings/brave-browser-archive-keyring.gpg https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg

   echo "deb [signed-by=/usr/share/keyrings/brave-browser-archive-keyring.gpg] https://brave-browser-apt-release.s3.brave.com/ stable main"|sudo tee /etc/apt/sources.list.d/brave-browser-release.list

   sudo apt update

   sudo apt install brave-browser
   ```

2. 

#### Secrets



#### SSH server



### Terminal (1)

### IOMMU

From this point on, we mostly rely on Bryan Steiner's excellent [tutorial](https://github.com/bryansteiner/gpu-passthrough-tutorial/).

### Libvirt hooks

### VM (1) creation

### Performance tweaks










## Resources

- [Kubuntu](https://kubuntu.org/)
- [Etcher](https://etcher.io/)
- [Btrfs](https://btrfs.readthedocs.io/en/latest/)


- [Synergy](https://symless.com/synergy)

- [Rsync](https://rsync.samba.org/)
   - [Repository](https://github.com/RsyncProject/rsync)
   - [Tutorial](https://www.digitalocean.com/community/tutorials/how-to-use-rsync-to-sync-local-and-remote-directories)




[^footnote]: Click the ending link to go back up where you were:

[^synergy]: Control multiple machines (Linux, Mac, Windows) with one keyboard+mouse combo, as if they were multiple displays connected to the same PC.  
  License cost per user (pay once, keep forever): \$29 for up to 3 machines, or \$49 for 15.

[^SHost]: `SHost` (Server Host) is the **CLI/headless** variant.
   - It's closer to a Type-I hypervisor.
   - Both `GHost` & `SHost` allow for 'native' graphical guests with plugged-in display, keyboard, mouse…
   - Both are part of my tentative [***Ultra***structure]() computing paradigm.

[]


[^2]: Generally, unplug all non-host devices during host OS installation. This ensures that, later on:
  - proper *graphics* drivers will get installed on the host;
  - auto-configs (Xorg…) work well;
  - guest GPU is available for passthrough. *In this guide, it's the Nvidia dGPU.*




[^?]: Consider using PCIe splitters if you don't have enough slots. Keep in mind that expensive PLX chips won't help for concurrent use, so I'd avoid them for GHost.














