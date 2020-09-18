# Portpack Mayhem build instructions

Since the performance is really bad under Docker, let's configure our mac so we can build the firmware directly on it.

**Spoiler alert:** Goes from 8 minutes to 4 seconds compilation time 🕺

## Clean directory

The Docker build will leave pre-compilation steps files (`*.a`, `*.d`, `*.o`) that we don't need. Clean everything before moving from the Docker procedure to the native one.

```bash
find . -name "*.d" -type f -delete
find . -name "*.o" -type f -delete
find . -name "*.a" -type f -delete

rm -rf build/*
```


## ARM toolchain

Download the `arm-none-eabi-xxx` compilers and likers from the official website (https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm) and extract the files into your home repository.

## macOS tools

**Homebrew** must be installed on your machine, obviously.

```bash
brew install cmake
brew install ccache
brew install dfu-util
```

## Python 3.x setup

Under macOS, Python 2.7 will be linked to the `/usr/bin/python` instead of the 3.x version (if you have both).

Because the `/usr/bin/python` is protected, even the `sudo rm`won't work, neither the `chattr` or `chflags`.

Quick fix is to modify the Hackrf's firmware script that uses the Python 3 version, `hackrf/firmware/libopencm3/scripts/irq2nvic_h` (from the *libopencm3* project):

*Replace*

```python
#!/usr/bin/env python
```

*With*

```python
#!/usr/bin/env python3
```

**Hey 👋** Don't commit this file, we'll keep the changes on your laptop only.

## Compilation time!

### From scratch:

```bash
cd build
cmake ..
make firmware
```

### Once the firmware has been built at least once

```bash
cd build
make firmware
```

## Flash it!

```bash
hackrf_spiflash -w firmware/portapack-h1_h2-mayhem.bin
```