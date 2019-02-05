# Step 4. SD card

TODO: write more

Code: [https://os.mbed.com/users/stepansnigirev/code/4_sdcard_solved/](https://os.mbed.com/users/stepansnigirev/code/4_sdcard_solved/)

Full code:

```cpp
#include <mbed.h>
#include "gui.h"
#include "FATFileSystem.h"
#include "f469_sd.h"
#include "Bitcoin.h"

/* Classes used for SD card */
SD_F469 bd;              /* BlockDevice (SD card driver) */
FATFileSystem fs("sd"); /* File system. Our SD card will be mounted to /sd/ folder */

/* to drop std:: part in the code */
using std::string;

GUI gui;     /* our GUI instance */
Label lbl;   /* label in the global scope, we will change text from the button callback */
QR qr;       /* QR in the global scope, we will change text from the button callback */
Tx tx;       /* Transaction that we will be loading from SD card */

/* we gonna use testnet here, will be used in address generation and key derivation */
/* to switch to the mainnet just change this to false */
#define USE_TESTNET true

/* init the key. handy tool: https://iancoleman.io/bip39/ */
HDPrivateKey hd("rhythm witness display knock head cable era exact submit boost exile seek topic pool sound", "my secret password");

/* init account key. We will use BIP84 to generate bech32 addresses */
/* derivation path is "m/84'/0'/0'" for mainnet and "m/84'/1'/0'" for testnet */
HDPrivateKey account = hd.hardenedChild(84).hardenedChild(USE_TESTNET).hardenedChild(0);

int change = 0; // 0 for receive addresses, 1 for change addresses
int current_child = 0;

/* forward declarations */
void showMessage(string header, string msg);
lv_res_t backCallback(lv_obj_t * btn);

/* shows address based on change and current_child variables */
void showAddress(){
  string addr = account.child(change).child(current_child).address()+"\n";
  qr.text("bitcoin:"+addr);
  qr.align(ALIGN_CENTER);

  char derivation_path[40];
  sprintf(derivation_path, "\nm/84'/%d'/0'/%d/%d", USE_TESTNET, change, current_child);
  addr += derivation_path;
  lbl.text(addr);
}

/* Next / Prev / Change buttons callback */
lv_res_t addressCallback(lv_obj_t * btn){
  Button b(btn);
  int id = b.id();
  if(id==0){ // change/receive button
      change = !change;
      if(change){
        b.text("Switch to\nReceive");
      }else{
        b.text("Switch to\nChange");
      }
  }
  current_child += id; /* we have id=1 for next and id=-1 for previous address */
  if(current_child < 0){
    current_child = 0;
  }
  showAddress();
  return LV_RES_OK;
}

/* Show xpub callback */
lv_res_t xpubCallback(lv_obj_t * btn){
  Button b(btn);
  if(b.id() == 0){
    b.text("Show addresses");
    b.id(1);
    lbl.text(account.xpub());
    qr.text(account.xpub());
    qr.align(ALIGN_CENTER);
  }else{
    b.text("Show xpub");
    b.id(0);
    showAddress();
  }
  return LV_RES_OK;
}

/* loads file from the SD card */
string loadFile(const char * fname){
  int error = fs.mount(&bd);
  if(error){
    showMessage("SD card failed!", "Can't mount SD card. Is it formatted in FAT?");
    return "";
  }
  /* try to open unsigned.txn in the root of the SD card */
  FILE* fd = fopen(fname, "r");
  if(fd == NULL){
    char title[100];
    sprintf(title, "Can't open the %s file!", fname);
    showMessage(title, "Are you sure you have it on the SD card?");
    fs.unmount();
    return "";
  }
  // first let's check the size of the transaction
  fseek(fd, 0, SEEK_END); // seek to end of file
  size_t size = ftell(fd);       // get current file pointer
  fseek(fd, 0, SEEK_SET);
  // let's limit the size of the file
  if(size > 1000){
    fclose(fd);
    fs.unmount();
    char msg[100];
    sprintf(msg, "Transaction size: %d bytes", size);
    showMessage("Transaction is too large", msg);
    return "";
  }
  // now let's read this to memory
  // we should use malloc and free the memory afterwards,
  // but this one is easier for the beginners, just large enough to store tx
  char content[1000] = "";
  size_t cur = 0;
  while(!feof(fd) && cur < sizeof(content)-1){
    content[cur] = fgetc(fd);
    cur ++;
  }

  error = fclose(fd);
  if(error){
    showMessage("SD card failed!", "Can't close the file.");
    fs.unmount();
    return "";
  }
  fs.unmount();
  return string(content);
}

/* gets hex part of the electrum transaction */
string getHexPart(string content){
  // search for "hex"
  int offset = content.find("\"hex\"");
  if(offset < 0){
    return "";
  }
  offset = content.find("\"", offset+5); // + length of "hex"
  if(offset < 0){
    return "";
  }
  content = content.substr(offset+1);
  offset = content.find("\"");
  content = content.substr(0, offset);
  return content;
}

int saveTransaction(const char * fname){
  int error;
  error = fs.mount(&bd);
  if(error){
    return -1;
  }

  FILE* fd = fopen(fname, "w");
  if(fd == NULL){
    return -2;
  }

  uint8_t raw[1000] = { 0 };
  if(tx.length() < sizeof(raw)){
    tx.serialize(raw, sizeof(raw), true);
    for(unsigned int i=0; i<tx.length(); i++){
      fprintf(fd, "%02x", raw[i]); // saving in hex format
    }
  }else{
    fclose(fd);
    fs.unmount();
    return -3;
  }
  // fprintf(fd, content);
  fclose(fd);

  error = fs.unmount();
  if(error){
    return -4;
  }
  return 0;
}

lv_res_t saveCallback(lv_obj_t * btn){
  if(saveTransaction("/sd/signed.txn")<0){
    showMessage("Fail", "Error saving the transaction");
  }else{
    showMessage("Success", "Transaction saved as signed.txn");
  }
  return LV_RES_OK;
}

void showSignedTransaction(){
  uint8_t raw[1000];
  tx.serialize(raw, sizeof(raw), true); // true for segwit
  string s = toBase43(raw, tx.length());
  gui.clear();
  Label lbl("Signed!");
  lbl.position(0, 20);
  lbl.align(ALIGN_CENTER);
  QR qr(s);
  qr.size(gui.width()-20);
  qr.position(10, 100);
  qr.align(ALIGN_CENTER);
  Button btn(backCallback, "OK");
  btn.position(gui.width()/2 + 20, gui.height()-120);
  btn.size(200,100);
  Button save_btn(saveCallback, "Save to SD card");
  save_btn.position(20, gui.height()-120);
  save_btn.size(200,100);
}

lv_res_t signCallback(lv_obj_t * btn){
  tx.sign(account);
  showSignedTransaction();
  return LV_RES_OK;
}

void showTransaction(){
  gui.clear();
  char msg[100];
  sprintf(msg, "Number of inputs: %d\nNumber of outputs: %d\n\n", tx.inputsNumber, tx.outputsNumber);
  string s(msg);
  for(unsigned int i=0; i<tx.outputsNumber; i++){
    sprintf(msg, "%.4f mBTC to %s\n\n", float(tx.txOuts[i].amount)/100000, tx.txOuts[i].address(USE_TESTNET).c_str());
    s += msg;
  }
  sprintf(msg, "Fee: %.4f mBTC", float(tx.fee())/100000);
  s += msg;
  Label lbl(s);
  lbl.size(gui.width()-40, gui.height()-140);
  lbl.position(0, 50);
  lbl.align(ALIGN_CENTER);

  Button sign_btn(signCallback, "Sign");
  sign_btn.position(gui.width()/2 + 20, gui.height()-120);
  sign_btn.size(200,100);

  Button cancel_btn(backCallback, "Cancel");
  cancel_btn.position(20, gui.height()-120);
  cancel_btn.size(200,100);
}

/* Load tx from SD button callback */
lv_res_t sdCallback(lv_obj_t * btn){
  string content = loadFile("/sd/unsigned.txn");
  if(content == ""){ // failed to load
    return LV_RES_OK;
  }
  content = getHexPart(content);
  if(content == ""){
    showMessage("Fail", "Wrong transaction format\nCan't find \"hex\" key");
  }
  size_t l = tx.parseHex(content);
  if(l == 0){
    showMessage("Fail", "Can't load transaction");
  }
  showTransaction();
  return LV_RES_OK;
}

/* Shows QR code, address and buttons */
void showMainScreen(){
  gui.clear();

  /* Create a QR code to display addresses and xpub */
  qr = QR("Loading...");
  qr.size(300);
  qr.position(0, 50);
  qr.align(ALIGN_CENTER);

  /* Create a label to display addresses and xpub */
  lbl = Label("Loading...");
  lbl.size(gui.width(), 100); // full width
  lbl.position(0, 400);
  lbl.alignText(ALIGN_TEXT_CENTER);
  showAddress();

  /* Buttons to control the addresses */
  Button next_btn(addressCallback, "Next");
  next_btn.id(1);
  next_btn.size(140, 100);
  next_btn.position(160*2+10, gui.height() - 240);

  Button prev_btn(addressCallback, "Previous");
  prev_btn.id(-1);
  prev_btn.size(140, 100);
  prev_btn.position(10, gui.height() - 240);

  Button change_btn(addressCallback, "Switch to\nChange");
  change_btn.id(0);
  change_btn.size(140, 100);
  change_btn.position(160+10, gui.height() - 240);

  Button xpub_btn(xpubCallback, "Show xpub");
  xpub_btn.id(0);
  xpub_btn.size(gui.width()/2-20, 100);
  xpub_btn.position(gui.width()/2 + 10, gui.height() - 120);

  Button sd_btn(sdCallback, "Load transaction\nfrom SD card");
  sd_btn.size(gui.width()/2-20, 100);
  sd_btn.position(10, gui.height() - 120);
}

/* Returns to the main screen when OK in error screen is pressed */
lv_res_t backCallback(lv_obj_t * btn){
  showMainScreen();
  return LV_RES_OK;
}

/* Shows error message */
void showMessage(string header, string msg){
  gui.clear();
  Label header_lbl(header);
  header_lbl.position(0, 100);
  header_lbl.align(ALIGN_CENTER);
  header_lbl.alignText(ALIGN_TEXT_CENTER);

  Label message_lbl(msg);
  message_lbl.position(0, 200);
  message_lbl.size(gui.width()-40, 100);
  message_lbl.align(ALIGN_CENTER);
  message_lbl.alignText(ALIGN_TEXT_CENTER);

  Button confirm_btn(backCallback, "OK");
  confirm_btn.position(600, gui.height()-200);
  confirm_btn.size(200, 100);
  confirm_btn.align(ALIGN_CENTER);
}

int main() {

  gui.init();
  showMainScreen();

  while(1) {
    gui.update();
  }
}
```