# Solution to Step 3: Navigate between bitcoin addresses

[Code](https://os.mbed.com/users/stepansnigirev/code/3_bitcoin_solved/)

First, we will be working on the same account, and as children key derivation is heavy it makes sense to calculate account key only once.

```cpp
/* we gonna use testnet here, will be used in address generation and key derivation */
/* to switch to the mainnet just change this to false */
#define USE_TESTNET true

/* init the key. handy tool: https://iancoleman.io/bip39/ */
HDPrivateKey hd("rhythm witness display knock head cable era exact submit boost exile seek topic pool sound", "my secret password", USE_TESTNET);

/* init account key. We will use BIP84 to generate bech32 addresses */
/* derivation path is "m/84'/0'/0'" for mainnet and "m/84'/1'/0'" for testnet */
HDPrivateKey account = hd.hardenedChild(84).hardenedChild(USE_TESTNET).hardenedChild(0);
```

We will use two more global variables to determine current derivation path:

```cpp
int change = 0; // 0 for receive addresses, 1 for change addresses
int current_child = 0;
```

Now, 3 of our buttons do the same thing - derive a new child and show the address. So we will re-use the same callback and slightly change the behaviour based on the button index. As button id is an unsigned integer, we can use `1` for previous address, `3` for next and `2` to switch between change / receive.

```cpp
/* button callback */
static lv_res_t addr_cb(lv_obj_t * btn){
  Button b(btn);
  int i = b.id();
  if(i==2){ // change/receive button
      change = !change;
  }
  current_child += i-2; /* we have id=1 and id=3, so we can use (id-2) to decrease or increase current child */
  if(current_child < 0){
    current_child = 0;
  }
  char derivation_path[40];
  sprintf(derivation_path, "\nm/84'/%d'/0'/%d/%d", USE_TESTNET, change, current_child);
  string addr = account.child(change).child(current_child).address()+"\n";
  addr += derivation_path;
  lbl.text(addr);
  return LV_RES_OK;
}
```

And then we just create the label and the buttons in the `main` function:

```cpp
  /* Create a label to display addresses and xpub */
  lbl = Label(account.xpub());
  lbl.size(gui.width()-40, 100); // full width
  lbl.position(20, 200);
  lbl.align_text(ALIGN_TEXT_CENTER);

  Button next_btn(addr_cb, "Next");
  next_btn.id(3);
  next_btn.size(200, 100);
  next_btn.position(gui.width()/2 + 20, gui.height() - 240);

  Button prev_btn(addr_cb, "Previous");
  prev_btn.id(1);
  prev_btn.size(200, 100);
  prev_btn.position(gui.width()/2 - 220, gui.height() - 240);

  Button change_btn(addr_cb, "Receive / Change");
  change_btn.id(2);
  change_btn.size(200, 100);
  change_btn.position(gui.width()/2 - 220, gui.height() - 120);

  Button xpub_btn(xpub_cb, "Show xpub");
  xpub_btn.size(200, 100);
  xpub_btn.position(gui.width()/2 + 20, gui.height() - 120);
```

In total:

```cpp
#include <mbed.h>
#include "gui.h"
#include "Bitcoin.h"

/* to drop std:: part in the code */
using std::string;

GUI gui;     /* our GUI instance */
Label lbl;   /* label in the global scope, we will change text from the button callback */

/* we gonna use testnet here, will be used in address generation and key derivation */
/* to switch to the mainnet just change this to false */
#define USE_TESTNET true

/* init the key. handy tool: https://iancoleman.io/bip39/ */
HDPrivateKey hd("rhythm witness display knock head cable era exact submit boost exile seek topic pool sound", "my secret password", USE_TESTNET);

/* init account key. We will use BIP84 to generate bech32 addresses */
/* derivation path is "m/84'/0'/0'" for mainnet and "m/84'/1'/0'" for testnet */
HDPrivateKey account = hd.hardenedChild(84).hardenedChild(USE_TESTNET).hardenedChild(0);

int change = 0; // 0 for receive addresses, 1 for change addresses
int current_child = 0;

/* button callback */
static lv_res_t addr_cb(lv_obj_t * btn){
  Button b(btn);
  int i = b.id();
  if(i==2){ // change/receive button
      change = !change;
  }
  current_child += i-2; /* we have id=1 and id=3, so we can use (id-2) to decrease or increase current child */
  if(current_child < 0){
    current_child = 0;
  }
  char derivation_path[40];
  sprintf(derivation_path, "\nm/84'/%d'/0'/%d/%d", USE_TESTNET, change, current_child);
  string addr = account.child(change).child(current_child).address()+"\n";
  addr += derivation_path;
  lbl.text(addr);
  return LV_RES_OK;
}

static lv_res_t xpub_cb(lv_obj_t * btn){
  lbl.text(account.xpub());
  return LV_RES_OK;
}

int main() {

  gui.init();

  /* Create a label to display addresses and xpub */
  lbl = Label(account.xpub());
  lbl.size(gui.width()-40, 100); // full width
  lbl.position(20, 200);
  lbl.align_text(ALIGN_TEXT_CENTER);

  Button next_btn(addr_cb, "Next");
  next_btn.id(3);
  next_btn.size(200, 100);
  next_btn.position(gui.width()/2 + 20, gui.height() - 240);

  Button prev_btn(addr_cb, "Previous");
  prev_btn.id(1);
  prev_btn.size(200, 100);
  prev_btn.position(gui.width()/2 - 220, gui.height() - 240);

  Button change_btn(addr_cb, "Receive / Change");
  change_btn.id(2);
  change_btn.size(200, 100);
  change_btn.position(gui.width()/2 - 220, gui.height() - 120);

  Button xpub_btn(xpub_cb, "Show xpub");
  xpub_btn.size(200, 100);
  xpub_btn.position(gui.width()/2 + 20, gui.height() - 120);

  while(1) {
    gui.update();
  }
}
```