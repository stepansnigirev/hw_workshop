# DIY Hardware Wallet Workshop

Old version of the workshop docs is in the [v1 folder](./v1/)

[Slides](./slides.pdf)

## Required hardware

- 32F469I-Discovery dev board from ST Microelectronics from any electronics store like [Mouser](https://www.mouser.com/ProductDetail/STMicroelectronics/STM32F469I-DISCO?qs=kWQV1gtkNndotCjy2DKZ4w%3D%3D), [Digikey](https://www.digikey.com/product-detail/en/stmicroelectronics/STM32F469I-DISCO/497-15990-ND/5428811), [Aliexpress](https://www.aliexpress.com/wholesale?catId=200214206&initiative_id=AS_20190730173121&SearchText=32f469idiscovery&switch_new_app=y), [Waveshare](https://www.waveshare.com/stm32f469i-disco.htm)
- SD card (16 GB works for sure)

## Quick start

1. Register on [mbed.com](https://www.mbed.com/en/)
2. Add hardware to your compiler [here](https://os.mbed.com/platforms/ST-Discovery-F469NI/)
3. Import the [workshop template](https://os.mbed.com/users/stepansnigirev/code/workshop_template/)
4. Complete the TODO list
5. Or check out the [complited version](https://os.mbed.com/users/stepansnigirev/code/workshop_completed/)
6. Start playing with it and Bitcoin Core using command line interface or this simple [web interface](https://github.com/stepansnigirev/minicore)

## Useful notes and references

Libraries for microcontrollers worth mentioning:

- [littlevgl](https://littlevgl.com/) - nice graphics library for embedded systems
- [qrcode](https://github.com/ricmoo/QRCode) library

Theory:

- [mnemonics - bip39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki)
- [HD keys - bip32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki)
- Derivation pathes for [legacy](https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki) and [nested](https://github.com/bitcoin/bips/blob/master/bip-0049.mediawiki) and [native](https://github.com/bitcoin/bips/blob/master/bip-0084.mediawiki) segwit
- [Partially signed transaction format - bip174](https://github.com/bitcoin/bips/blob/master/bip-0174.mediawiki)

Iteresting platforms and devboards:

- [list of mbed-compatible boards](https://os.mbed.com/platforms/)
- [32-bit boards from Adafruit](https://www.adafruit.com/categories)
- [ESP-32 boards from M5Stack](https://m5stack.com/)
- [RISC-V dev boards](https://www.seeedstudio.com/catalogsearch/result/?cat=&q=Risc-V)

Languages and frameworks:

- [ARM Mbed](https://www.mbed.com/en/) ( C++ )
- [Arduino](https://www.arduino.cc/) ( C++ )
- [Micropython](http://micropython.org/)
- [Circuit Python](https://circuitpython.readthedocs.io/)
- [Embedded Rust](https://www.rust-lang.org/what/embedded)
- [Tock OS](https://www.tockos.org/)

Bitcoin libraries:

- [micro-bitcoin](https://github.com/micro-bitcoin/uBitcoin)
- [trezor-crypto](https://github.com/trezor/trezor-firmware)
- [secp256k1](https://github.com/bitcoin-core/secp256k1) from Bitcoin Core
- [libwally](https://github.com/ElementsProject/libwally-core) and [esp32 demo](https://github.com/greenaddress/8bkc-wally/)

