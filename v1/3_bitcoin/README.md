# Step 3. Bitcoin!

Code on mbed: https://os.mbed.com/users/stepansnigirev/code/3_bitcoin/

F746 board: https://os.mbed.com/users/stepansnigirev/code/3_bitcoin_F7/

Based on what we've learned so far we can now dive into bitcoin. There are a few libraries for embedded systems, we will use [micro-bitcoin](https://github.com/micro-bitcoin/uBitcoin) today. It works with Arduino, mbed and other frameworks and uses OOP approach.

The goal is to display the first receiving address of the wallet.

**Assignment**: Improve the GUI - add 4 buttons: "Next address", "Previous address", "Change / Receive" and "Show xpub".
- "Next address" should increase the counter, derive next children and show the next address.
- "Previous address" should decrease the counter. It shouldn't go below 0.
- "Change/receive" should switch between displaying receiving and change addresses
- "Show xpub" should display master public key (`key.xpub()`)

**Advanced**: Display both segwit and nested segwit addresses.

## Explanation

Don't forget to import the library: [https://github.com/micro-bitcoin/uBitcoin](https://github.com/micro-bitcoin/uBitcoin)

First we generate a master private key from a mnemonic and a password. For now we will use a fixed mnemonic, we will come back to generation of it in the last section. You can generate a new mnemonic phrase here: [https://iancoleman.io/bip39/](https://iancoleman.io/bip39/). We will also use this site to verify that all our addresses and master keys are correct.

```cpp
HDPrivateKey hd("rhythm witness display knock head cable era exact submit boost exile seek topic pool sound", "my secret password");
```

Now we can derive it's children to get the key for the first address. We will use BIP84:

```cpp
HDPrivateKey first_key = hd.hardenedChild(84).hardenedChild(1).hardenedChild(0).child(0).child(0);
std::string address = first_key.address();
```

Here the path is `m/84'/1'/0'/0/0/`. These numbers mean:
- `84'` for BIP84, the key will automatically set it's type to native segwit addresses
- `1'` for testnet (put `0` here for mainnet)
- `0'` for account - xpub of this key we can use in our watch-only wallet on the computer. All children down are not hardened - so we can derive their public keys on the PC.
- `0` for receiving address, put `1` here to get a change address
- `0` for the first address, to get other addresses just increase this number.

And now we can display our address on the screen:

```cpp
Label lbl(address);
```

To add QR codes we can use a `QR` class. It works very similar to `Label`:

```cpp
QR qr("bitcoin:"+address);
qr.size(300);
``` 

As QR code is a square, we use only one size in `size()` method.

Full code:

```cpp
#include <mbed.h>
#include "gui.h"
#include "Bitcoin.h"

/* to drop std:: part in the code */
using std::string;

GUI gui;     /* our GUI instance */
Label lbl;   /* label in the global scope, we will change text from the button callback */
QR qr;       /* QR in the global scope, we will change text from the button callback */

/* we gonna use testnet here, will be used in address generation and key derivation */
/* to switch to the mainnet just change this to false */
#define USE_TESTNET true

int main() {

  /* init the key. handy tool: https://iancoleman.io/bip39/ */
  HDPrivateKey hd("rhythm witness display knock head cable era exact submit boost exile seek topic pool sound", "my secret password");
  HDPrivateKey first_key = hd.hardenedChild(84).hardenedChild(USE_TESTNET).hardenedChild(0).child(0).child(0);
  string address = first_key.address();

  gui.init();

  /* Create a label to display addresses and xpub */
  lbl = Label(address);
  lbl.size(gui.width()-40, 100); // full width
  lbl.position(20, 400);
  lbl.align_text(ALIGN_TEXT_CENTER);

  qr = QR("bitcoin:"+address);
  qr.size(300);
  qr.position(0, 50);
  qr.align(ALIGN_CENTER);

  while(1) {
    gui.update();
  }
}
```

## Solution

[Solution code](https://os.mbed.com/users/stepansnigirev/code/3_bitcoin_solved/), [explanation](./solved.md)

Solution for F746: https://os.mbed.com/users/stepansnigirev/code/3_bitcoin_solved_F7/
