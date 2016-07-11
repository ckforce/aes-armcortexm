# Fast AES on ARM Cortex-M3 and M4

This collection consists of the following highly optimized FIPS 197 compliant AES software implementations. 

* AES-128 key expansion.
* Single-block AES-128 encryptioni/decryption.
* AES-128 encryption/decryption in CTR mode.
* AES-192 key expansion.
* AES-192 encryption/decryption in CTR mode.
* AES-256 key expansion.
* AES-256 encryption/decryption in CTR mode.
* AES-128 key expansion to bitsliced state.
* Constant-time bitsliced AES-128 encryption/decryption in CTR mode (protected against timing attacks).
* Masked constant-time bitsliced AES-128 encryption/decryption in CTR mode (protected against timing attacks and first-order side-channel attacks).

# Performance
All numbers mentioned below were obtained on the STM32L100C (Cortex-M3) and STM32F407 (Cortex-M4) development boards. Encryption/decryption speeds are cycle/block averages for processing 4096 bytes, averaging over 10000 runs with random inputs, nonces, and keys. Note that all implementations are optimized for speed and therefore fully unrolled. ROM sizes can be reduced drastically with only a small penalty in cycle counts.

| Algorithm | Speed M3 (cycles) | Speed M4 (cycles) | ROM (bytes) | RAM (bytes) |
|-----------|------------------:|------------------:|------------:|------------:|
| AES-128 key expansion | 289.8 | 294.8 | 902 (code) + 1024 (data) | 176 (in/out) + 32 (stack) |
| AES-128 single block encryption | 659.4 | 661.7 | 2034 (code) + 1024 (data) | 176+2m (in/out) + 44 (stack) |
| AES-128 encryption/decryption in CTR mode | 546.3 | 554.4 | 2192 (code) + 1024 (data) | 192+2m (in/out) + 72 (stack) |  
| AES-192 key expansion | 265.9 | 272.2 | 810 (code) + 1024 (data) | 208 (in/out) + 32 (stack) |
| AES-192 encryption/decryption in CTR mode | 663.2 | 673.0 | 2576 (code) + 1024 (data) | 224+2m (in/out) + 72 (stack) |
| AES-256 key expansion | 364.8 | 371.8 | 1166 (code) + 1024 (data) | 240 (in/out) + 32 (stack) |
| AES-256 encryption/decryption in CTR mode | 786.9 | 791.7 | 2960 (code) + 1024 (data) | 256+2m (in/out) + 72 (stack) | 
| AES-128 key expansion to bitsliced state | 1027.8 | 1033.8 | 3434 (code) + 1036 (data) | 368 (in/out) + 188 (stack) |
| Constant-time bitsliced AES-128 encryption/decryption in CTR mode | 1616.6 | 1617.6 | 12120 (code) + 12 (data) | 368+2m (in/out) + 108 (stack) |
| Masked constant-time bitsliced AES-128 encryption/decryption in CTR mode | N/A | 2132.51 (generating randomness) + 5290.1 (rest) | 39916 (code) + 12 (data) | 368+2m (in/out) + 1312 (storing randomness) + 276 (stack rest) |  

# Compilation
All implementations come with Makefiles for the STM32L100C and STM32F407. This project uses the [libopencm3](https://github.com/libopencm3/libopencm3) firmware and the [arm-none-eabi toolchain](https://launchpad.net/gcc-arm-embedded). See, e.g., [this repository](https://github.com/joostrijneveld/STM32-getting-started) for more detailed instructions on getting those to work. Make sure the paths are set up correctly in the Makefiles. The implementations include examples in C on how to call the functions written in Thumb-2 assembly. Compile by executing `make`.

# Running the code
One can flash the code with a JTAG programmer or over USB using STLINK. For flashing over USB, make sure that the board is connected to the pc, install [stlink](https://github.com/texane/stlink), and execute`./deploy.sh <file>.bin` to flash the code.

The examples in C provide output over USART. To read this, use a USB to TTL adapter and connect TX to PA3, RX to PA2, and GND to GND. Execute `common/host.py` to start a simple listener for serial communication to `/dev/ttyUSB0`. This uses the `pyserial` module.
