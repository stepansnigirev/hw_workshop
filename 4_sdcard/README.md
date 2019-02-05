# Step 4. SD card.

Code: [https://os.mbed.com/users/stepansnigirev/code/4_sdcard/](https://os.mbed.com/users/stepansnigirev/code/4_sdcard/)

SD card is a bit annoying on this board. Sometimes you need to unplug the device from power and plug it back to make SD card to work. Not my fault, conflicts of the latests mbed builds and SD card driver by STMicroelectronics... I hope it will be fixed soon.

**Assignment**: Load electrum transaction from the SD card, parse, sign and write back to SD card or display as base43 QR code.

## How to work with SD card

Here is an example of how to read and write files. Look at `saveFile` and `loadFile` functions.

TODO: write more

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

void showMessage(string header, string msg); // forward declaration

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
    sprintf(msg, "File size: %d bytes", size);
    showMessage("File is too large", msg);
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

int saveFile(const char * fname, const char * content){
  int error;
  error = fs.mount(&bd);
  if(error){
    return -1;
  }

  FILE* fd = fopen(fname, "w");
  if(fd == NULL){
    return -2;
  }

  fprintf(fd, content);
  fclose(fd);

  error = fs.unmount();
  if(error){
    return -4;
  }
  return 0;
}

/* Load file from SD button callback */
lv_res_t loadCallback(lv_obj_t * btn){
  string content = loadFile("/sd/hello.txt");
  if(content == ""){ // failed to load
    return LV_RES_OK;
  }
  lbl.text(content);
  qr.text(content);
  return LV_RES_OK;
}

/* Save file to SD button callback */
lv_res_t saveCallback(lv_obj_t * btn){
  int error = saveFile("/sd/hello.txt", "Hello workshop!");
  if(error < 0){ // failed to save
    string header = "Fail";
    string message = "Something went wrong";
    if(error == -1){
        message = "Failed to mount SD card";
    }
    if(error == -2){
        message = "Failed to open file for writing";
    }
    if(error == -4){
        message = "Failed to unmount";
    }
    showMessage(header, message);
    return LV_RES_OK;
  }
  showMessage("Success!", "File saved");
  return LV_RES_OK;
}

/* Shows QR code, address and buttons */
void showMainScreen(){
  gui.clear();

  /* Create a QR code to display addresses and xpub */
  qr = QR("Nothing here yet");
  qr.size(300);
  qr.position(0, 50);
  qr.align(ALIGN_CENTER);

  /* Create a label to display addresses and xpub */
  lbl = Label("Nothing here yet");
  lbl.size(gui.width(), 100); // full width
  lbl.position(0, 400);
  lbl.alignText(ALIGN_TEXT_CENTER);

  Button load_btn(loadCallback, "Load file\nfrom SD card");
  load_btn.size(gui.width()/2-20, 100);
  load_btn.position(10, gui.height() - 120);

  Button save_btn(saveCallback, "Save file\nto SD card");
  save_btn.size(gui.width()/2-20, 100);
  save_btn.position(gui.width()/2 + 10, gui.height() - 120);
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

## Solution

[Solution code](https://os.mbed.com/users/stepansnigirev/code/4_sdcard_solved/), [explanation](./solved.md)
