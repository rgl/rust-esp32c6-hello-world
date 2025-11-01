# About

[![Build status](https://github.com/rgl/rust-esp32c6-hello-world/workflows/build/badge.svg)](https://github.com/rgl/rust-esp32c6-hello-world/actions?query=workflow%3Abuild)

[ESP32-C6 DevKitC board](https://docs.espressif.com/projects/esp-dev-kits/en/latest/esp32c6/esp32-c6-devkitc-1/user_guide.html) Hello World.

ESP32-C6 uses a 32-bit [RISC-V](https://en.wikipedia.org/wiki/RISC-V) CPU. It implements the RV32I ISA base and the Multiplication, Atomics, Compressed instructions (MAC) ISA extensions. It's supported by the [riscv32imac-unknown-none-elf rust target](https://doc.rust-lang.org/stable/rustc/platform-support/riscv32-unknown-none-elf.html).

**NB** Also see https://github.com/rgl/rust-esp32-hello-world.

**NB** Also see https://github.com/rgl/platformio-esp32-arduino-hello-world.

## Usage (Ubuntu 22.04 host)

Install the dependencies:

* [Docker](https://docs.docker.com/engine/install/).
* [Visual Studio Code](https://code.visualstudio.com).
* [Dev Container plugin](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers).

The ESP32-C6 board connects to the computer as a USB composite device (ESP32 USB-JTAG-SERIAL peripheral), as such, you should add your user to the `plugdev` group:

```bash
# add yourself to the plugdev group.
sudo usermod -a -G plugdev $USERNAME
# enter a new shell with the plugdev group enabled.
# NB to make this permanent its easier to reboot the system with sudo reboot
#    or to logout/login your desktop.
su - $USER
```

Ensure [udev rules are installed](https://probe.rs/docs/getting-started/probe-setup/).

Connect the ESP32-C6 USB-C labeled port to the computer, the USB device for the ESP32-C6 USB-JTAG-SERIAL peripheral should show up:

```bash
sudo dmesg --follow-new
```
```
usb 1-2.4: new full-speed USB device number 8 using xhci_hcd
usb 1-2.4: New USB device found, idVendor=303a, idProduct=1001, bcdDevice= 1.02
usb 1-2.4: New USB device strings: Mfr=1, Product=2, SerialNumber=3
usb 1-2.4: Product: USB JTAG/serial debug unit
usb 1-2.4: Manufacturer: Espressif
usb 1-2.4: SerialNumber: 98:A3:16:9F:0C:94
cdc_acm 1-2.4:1.0: ttyACM0: USB ACM device
```

See the USB tree:

```bash
lsusb -t
```
```
Port 4: Dev 8, If 2, Class=Vendor Specific Class, Driver=, 12M
Port 4: Dev 8, If 0, Class=Communications, Driver=cdc_acm, 12M
Port 4: Dev 8, If 1, Class=CDC Data, Driver=cdc_acm, 12M
```

Open this directory with Visual Studio Code and as a Dev Container.

Open `bash` inside the Visual Studio Code Terminal.

List the available debug probes:

```bash
probe-rs list
```

You should see something alike:

```
The following debug probes were found:
[0]: ESP JTAG -- 303a:1001:98:A3:16:9F:0C:94 (EspJtag)
```

Show information about the debug probe:

```bash
probe-rs info --protocol jtag
```

You should see something alike:

```
RISC-V Chip:
  IDCODE: 000000dc25
    Version:      0
    Part:         13
    Manufacturer: 1554 (Espressif Systems (Shanghai)  Co Ltd)
```

List the installed rust tools chains:

```bash
rustup toolchain list -v
```

Build, flash and run:

```bash
# build.
#cargo build --release

# build and flash.
#cargo flash --release

# build, flash, and run.
# NB press ctrl+c to exit the probe monitor.
# NB use one of the following.
cargo embed --release
cargo run --release

# attach to the probe monitor.
probe-rs attach target/riscv32imac-unknown-none-elf/release/rust-esp32c6-hello-world
```

**NB** This project was initially bootstrapped using [esp-generate](https://github.com/esp-rs/esp-generate) as:

```bash
esp-generate \
  --headless \
  --chip esp32c6 \
  --option alloc \
  --option unstable-hal \
  --option embassy \
  --option probe-rs \
  --option defmt \
  --option panic-rtt-target \
  --option ci \
  --option vscode \
  rust-esp32c6-hello-world
```
