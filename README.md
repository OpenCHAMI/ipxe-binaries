# iPXE Binaries

Builds of [iPXE](https://ipxe.org/) pinned to a specific upstream release, with an [embedded script](./config/init.ipxe) that supports DHCPv4 and DHCPv6 boot configuration.

[![hydrun CI](https://github.com/OpenCHAMI/ipxe-binaries/actions/workflows/hydrun.yaml/badge.svg)](https://github.com/OpenCHAMI/ipxe-binaries/actions/workflows/hydrun.yaml)

## Overview

This repository builds iPXE binaries from a **pinned upstream version** (currently **v2.0.0**) with an embedded boot script.

The embedded script accounts for both DHCPv4 and DHCPv6. For DHCPv4, it obtains a DHCPv4 lease, then chainloads `/config.ipxe` from the TFTP server identified by DHCP option 17 (`root-path`). For DHCPv6, it explicitly configures IPv6 and chainloads the bootfile URL received in DHCPv6 option 59 (`filename`). DHCPv6 booting is supported by the UEFI binaries; legacy BIOS builds do not include IPv6 support.

### Why v2.0.0?

iPXE v2.0.0 (released 2026-03-06) includes critical improvements over the previously used v0.0.5 build:

- **Broadcom NetXtreme-E (BCM57508) error-recovery fix** — resolves PXE boot failures with BCM57508 NICs running firmware 23.21.13.34 and later.
- UEFI Secure Boot support.
- New CPU architecture support (LoongArch64, RISC-V).
- TLS improvements (DHE, ECDHE, GCM, X25519, P-256/P-384, ECDSA).
- Additional NIC drivers (Intel 100GbE, Marvell AQtion, Google GVE, and more).

### Build Targets

Binaries are built with HTTPS support enabled for the following [iPXE build targets](https://ipxe.org/appnote/buildtargets):

| Binary | Target | Use Case |
|--------|--------|----------|
| `ipxe-i386.kpxe` | `bin-i386-pcbios/ipxe.kpxe` | Legacy BIOS (i386) |
| `ipxe-i386.efi` | `bin-i386-efi/ipxe.efi` | 32-bit UEFI |
| `ipxe-x86_64.efi` | `bin-x86_64-efi/ipxe.efi` | 64-bit UEFI (all drivers) |
| `snponly-x86_64.efi` | `bin-x86_64-efi/snponly.efi` | 64-bit UEFI via SNP (Broadcom BCM57508 etc.) |
| `undionly.kpxe` | `bin-x86_64-pcbios/undionly.kpxe` | Legacy BIOS via UNDI |
| `ipxe-arm32.efi` | `bin-arm32-efi/snp.efi` | ARM 32-bit UEFI |
| `ipxe-arm64.efi` | `bin-arm64-efi/snp.efi` | ARM 64-bit UEFI |
| `snponly-arm64.efi` | `bin-arm64-efi/snponly.efi` | ARM 64-bit UEFI via SNP |

Also included is a [bofied](https://github.com/pojntfx/bofied) configuration file ([config.go](./config/config.go)) and an example iPXE script ([config.ipxe](./config/config.ipxe)).

## Installation

A release `.tar.gz` archive is uploaded to [GitHub releases](https://github.com/OpenCHAMI/ipxe-binaries/releases). The [coresmd](https://github.com/OpenCHAMI/coresmd) container automatically fetches the latest release at build time.

## Building Locally

```shell
$ git clone https://github.com/OpenCHAMI/ipxe-binaries.git
$ cd ipxe-binaries
$ hydrun -o debian:bookworm -i ./Hydrunfile c
```

To build with a different iPXE version:

```shell
$ IPXE_VERSION=v2.0.0 hydrun -o debian:bookworm -e "-e IPXE_VERSION=${IPXE_VERSION}" -i ./Hydrunfile c
```

## Acknowledgements

- [ipxe/ipxe](https://github.com/ipxe/ipxe) provides the sources that the binaries are built from.

## License

iPXE Binaries (c) 2024 Felicitas Pojtinger and contributors

SPDX-License-Identifier: GPL-2.0
