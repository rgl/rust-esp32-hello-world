# About

[![Build status](https://github.com/rgl/rust-esp32-hello-world/workflows/build/badge.svg)](https://github.com/rgl/rust-esp32-hello-world/actions?query=workflow%3Abuild)

[AZ-Delivery ESP-32 Dev Kit C V4 board](https://www.az-delivery.de/en/products/esp-32-dev-kit-c-v4) Hello World.

[ESP32](https://en.wikipedia.org/wiki/ESP32) uses a dual core 32-bit Xtensa LX6 CPU. It's supported by the [xtensa-esp32-none-elf rust target](https://doc.rust-lang.org/stable/rustc/platform-support/xtensa.html).

**NB** Also see https://github.com/rgl/rust-esp32c6-hello-world.

**NB** Also see https://github.com/rgl/platformio-esp32-arduino-hello-world.

## Usage (Ubuntu 22.04 host)

This ESP32 board connects to the computer as a USB serial device, as such, you should add your user to the `dialout` group:

```bash
# add yourself to the dialout group to be able to write to the USB serial
# device without root permissions.
sudo usermod -a -G dialout $USERNAME
# enter a new shell with the dialout group enabled.
# NB to make this permanent its easier to reboot the system with sudo reboot
#    or to logout/login your desktop.
su - $USER
```

Install the dependencies:

* [Docker](https://docs.docker.com/engine/install/).
* [Visual Studio Code](https://code.visualstudio.com).
* [Dev Container plugin](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers).

Connect the ESP32 board USB to your computer.

**NB** The dev container expected the USB device to be at `/dev/ttyUSB0`.

Open this directory with the Dev Container plugin.

Open `bash` inside the Visual Studio Code Terminal.

List the installed rust tools chains:

```bash
rustup toolchain list -v
```

You should see something alike:

```
1.90.0-x86_64-unknown-linux-gnu (default) /home/vscode/.rustup/toolchains/1.90.0-x86_64-unknown-linux-gnu
esp (active) /home/vscode/.rustup/toolchains/esp
```

Dump the information about the esp attached to the `/dev/ttyUSB0` serial port defined in [espflash_ports.toml](espflash_ports.toml) and allowed in [devcontainer.json](.devcontainer/devcontainer.json):

```bash
espflash --version
espflash list-ports
espflash board-info
```

You should see something alike:

```
espflash 4.2.0
/dev/ttyUSB0  EA60:10C4  Silicon Labs  CP2102 USB to UART Bridge Controller
[2025-10-24T21:05:30Z INFO ] Serial port: '/dev/ttyUSB0'
[2025-10-24T21:05:30Z INFO ] Connecting...
[2025-10-24T21:05:34Z INFO ] Using flash stub
Chip type:         esp32 (revision v1.0)
Crystal frequency: 40 MHz
Flash size:        4MB
Features:          WiFi, BT, Dual Core, 240MHz, VRef calibration in efuse, Coding Scheme None
MAC address:       24:62:ab:e0:29:3c
Security features: None
```

Build, flash and run:

```bash
cargo build --release
espflash flash target/xtensa-esp32-none-elf/release/rust-esp32-hello-world --monitor
```

**NB** This project was initially bootstrapped using [esp-generate](https://github.com/esp-rs/esp-generate) as:

```bash
esp-generate \
  --headless \
  --chip esp32 \
  --option alloc \
  --option unstable-hal \
  --option embassy \
  --option defmt \
  --option esp-backtrace \
  --option ci \
  --option vscode \
  rust-esp32-hello-world
```
