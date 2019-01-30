# DIY Hardware wallet workshop

During this workshop we will do an air-gapped hardware wallet with SD card support based on [32F469IDISCOVERY](https://www.st.com/en/evaluation-tools/32f469idiscovery.html) board from STMicroelectronics. [Demo video](https://youtu.be/mE-J0Y05qHs).

We will use [mbed](https://www.mbed.com/en/) for development, but in principle you can use any OS / framework and hardware. Bitcoin functionality will also work on Arduino (but only 32-bit boards). You may need to change SD card and display libraries though.

If you want to start a bitcoin hobby project, feel free to contact me and ask questions. I will be glad to help. Email: stepan@cryptoadvance.io, Telegram: https://t.me/stepansnigirev

## Setting up environment

There are two pretty convenient ways to start with mbed:
- Online: register on [mbed.com](https://www.mbed.com/en/) and click on [Compiler](https://ide.mbed.com/compiler/). You will get to web IDE. Don't forget to add the [board](https://os.mbed.com/platforms/ST-Discovery-F469NI/) to the compiler ("Add to compiler" button).
- Offline: install [PlatformIO](https://platformio.org/platformio-ide) for Atom or Visual Studio Code.

## Step 1. Blinky

First thing that everyone does on a new board - blinks with LEDs. Our board has 4, so let's blink with all of them!

This is a simple example of how to blink with one LED in mbed:

[hw_workshop_1-blinky](https://github.com/stepansnigirev/hw_workshop_1-blinky)

Instead of blinking with 1 LED make it cycle over all 4 of them one after another ([solution](https://github.com/stepansnigirev/hw_workshop_1-blinky_solved)).

Advanced: Make it react on the user button: when we hold the button the direction of the LEDs cycle should be reversed ([solution](https://github.com/stepansnigirev/hw_workshop_1-blinky_solved_btn)).

## Step 2. Driving the display

## Step 3. Working with Bitcoin library

## Step 4. Working with SD card

## Step 5. Putting all together 