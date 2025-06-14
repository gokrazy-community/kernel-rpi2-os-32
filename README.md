# Kernel for Raspberry Pi 2, 32 bits (in sync with official bullseye repo)

This repository holds a pre-built 32 bits Linux kernel image for the Raspberry Pi 2, compiled from https://github.com/raspberrypi/linux, for usage by the [gokrazy](https://github.com/gokrazy/gokrazy) project.

> [!INFO]
> For the Raspberry Pi 1 or Pi Zero, see: https://github.com/gokrazy-community/kernel-rpi-os-32

To use the files in this repository (as well as the corresponding firmware), set the `KernelPackage` and `FirmwarePackage` of your gokrazy instance's `config.json`:

```jsonc
{
    // ...
    "KernelPackage": "github.com/gokrazy-community/kernel-rpi2-os-32/dist",
    "FirmwarePackage": "github.com/gokrazy-community/firmware-rpi/dist",
}
```

When building, make sure to set the appropriate `GOARCH` and `GOARM` environment variables:

```
GOARCH=arm GOARM=7 gok -i <instance-name> update
```

## How does it differ from https://github.com/gokrazy/kernel ?

|              | gokrazy-community/kernel-rpi2-os-32 | gokrazy/kernel |
| ------------ | ----------------------------------- | -------------- |
| architecture | ARMv7 32-bit                        | ARMv8 64-bit   |
| upstream     | linux Raspberry Pi OS fork          | linux mainline |

This kernel can be used by the Raspberry Pi 2 and follows the release cycles of the Raspberry Pi foundation.

## Manual compilation

```
go run cmd/compile/main.go
```

It will compile the kernel located in `linux-sources` using a [crossbuild docker image](https://github.com/gokrazy-community/crossbuild-armhf) and copy the resulting files in the `dist` folder.

It uses default kernel config (`make bcm2709_defconfig`), as recommended by the [official documentation](https://www.raspberrypi.com/documentation/computers/linux_kernel.html#native-build-configuration), with the addition of the SquashFS module (`CONFIG_SQUASHFS`, which is required for gokrazy) and `CONFIG_IPV6`.

> [!TIP]
> If you want to use your locally-compiled kernel, use the [replace directive](https://gokrazy.org/development/modules/#building-local-code-the-replace-directive). To prevent gokrazy from [complaining about `CheckDir(...): ... malformed file path`](https://github.com/gokrazy/tools/issues/72), add an empty `go.mod` file in the `linux-sources` folder.

## Update check

```
go run cmd/check-update/main.go
```

It will compare the kernel version distributed on https://archive.raspberrypi.org/debian/ with the `linux-sources` submodule current HEAD.

## Licenses

- The `vmlinuz` and `*.dtb` files are built from [Linux kernel sources](https://github.com/raspberrypi/linux), released under the GPL (see `linux-sources/COPYING`)
- The rest of the repository is released under BSD 3-Clause License (see `LICENSE`)
