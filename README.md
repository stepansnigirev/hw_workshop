# DIY Hardware wallet workshop

During this workshop we will do an air-gapped hardware wallet with SD card support based on [32F469IDISCOVERY](https://www.st.com/en/evaluation-tools/32f469idiscovery.html) board from STMicroelectronics. [Demo video](https://youtu.be/mE-J0Y05qHs).

We will use [mbed](https://www.mbed.com/en/) for development, but in principle you can use any OS / framework and hardware. Bitcoin functionality will also work on Arduino (but only 32-bit boards). You may need to change SD card and display libraries though.

If you want to start a bitcoin hobby project, feel free to contact me and ask questions. I will be glad to help. Email: stepan@cryptoadvance.io, Telegram: https://t.me/stepansnigirev

## Setting up environment

There are two pretty convenient ways to start with mbed:
- Online: register on [mbed.com](https://www.mbed.com/en/) and click on [Compiler](https://ide.mbed.com/compiler/). You will get to web IDE. Don't forget to add the [board](https://os.mbed.com/platforms/ST-Discovery-F469NI/) to the compiler ("Add to compiler" button).
- Offline: install [PlatformIO](https://platformio.org/platformio-ide) for Atom or Visual Studio Code.

## Table of contents

- [Step 1. Blinky.](./1_blinky/README.md) Blink with LEDs.
- [Step 2. Display.](./2_display/README.md) Drive a display and make a very simple GUI (label + button)
- [Step 3. Bitcoin!](./3_bitcoin/README.md) Import wallet from mnemonic phrase, generate addresses and show them on screen.
- [Step 4. QR codes.](./4_qrcode/README.md) Export master public key to electrum, show receiving addresses as QR codes.
- [Step 5. SD card.](./5_sdcard/README.md) Parse unsigned transaction from electrum, show it on screen, sign and save back to SD card.
- [Step 6. Final polish](./5_final/README.md) Show signed transaction as a QR code.
