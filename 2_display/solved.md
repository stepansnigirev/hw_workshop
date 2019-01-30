# Solution to Step 2: Use GUI to drive LEDs

[Code](https://os.mbed.com/users/stepansnigirev/code/2_display_solved/)

We will create 4 buttons, one for every LED. We will assign an id for every button and use the same callback for all the buttons.

Creating 4 buttons:
```cpp
  for(int i=0; i<4; i++){
    char btn_text[10];
    sprintf(btn_text, "LED %d", i);
    Button btn(callback, btn_text);
    btn.id(i);
    btn.size(100, 100);
    btn.position(10+120*i, 300);
  }
```

Callback has a default lvgl style and the argument is a pointer to lvgl object (button in our case). We can convert it to an instance of the Button class and then access all methods and parameters as before.

Button id stores information about the LED this button drives.

```cpp
static lv_res_t callback(lv_obj_t * btn){
  Button b(btn);
  int i = b.id();
  char msg[40];
  sprintf(msg, "Toggle LED %d!", i);
  lbl.text(msg);
  leds[i] = !leds[i]; // toggle
  return LV_RES_OK;
}
```

In total:

```cpp
#include <mbed.h>
#include <gui.h>

GUI gui;     /* our GUI instance */
Label lbl;   /* label in the global scope, we will change text from the button callback */

/* our LEDs array */
DigitalOut leds[] = { (LED1), (LED2), (LED3), (LED4) }; /* LED1 may not work for some reason */

/* button callback */
/* this is a standard lvgl callback, but we can transform lv_obj_t* to a Button */
static lv_res_t callback(lv_obj_t * btn){
  Button b(btn);
  int i = b.id();
  char msg[40];
  sprintf(msg, "Toggle LED %d!", i);
  lbl.text(msg);
  leds[i] = !leds[i]; // toggle
  return LV_RES_OK;
}

int main() {

  gui.init();
  for(int i=0; i<4; i++){
    leds[i] = 1;
  }

  /* Create a label to log clicks */
  lbl = Label("Hello display!");
  lbl.size(gui.width(), 100); // full width
  lbl.position(0, 200);
  lbl.align_text(ALIGN_TEXT_CENTER);

  /* Create buttons */
  for(int i=0; i<4; i++){
    char btn_text[10];
    sprintf(btn_text, "LED %d", i);
    Button btn(callback, btn_text);
    btn.id(i);
    btn.size(100, 100);
    btn.position(10+120*i, 300);
  }

  while(1) {
    gui.update();
  }
}
```