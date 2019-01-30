# Step 2. Drive the display.

Code on mbed: https://os.mbed.com/users/stepansnigirev/code/2_display/

Here we start with a simple program that creates a label and a button. The label shows a value of a counter and the button increases this counter by 1.

We will use a [littlevgl library](https://littlevgl.com/) to make a GUI. It is a very powerful open-source library with many different components and tools. We can do amazing GUI with it, with animations, antialiasing, custom fonts and components. I recommend looking into that.

As littlevgl is a C library and C is not very user-friendly, for this tutorial I wrote a small C++ library on top that supports only a two basic components - a label and a button. In [Step 4](../4_qrcode/README.md) we will add a QR code to this. That's all we need to start.

**Assignment**: Make 4 buttons that control LEDs.

**Advanced**: Have some fun and do something interesting. Implement another C++ component, for example.

## Explanation

First we need to include the lvgl library and the display driver for it. We will add my small C++ library on top.

In the compiler import the following libraries:

- [https://github.com/stepansnigirev/lvgl-mbed](https://github.com/stepansnigirev/lvgl-mbed)
- [https://github.com/stepansnigirev/f469_lvgl_driver](https://github.com/stepansnigirev/f469_lvgl_driver)
- [https://github.com/stepansnigirev/tiny_lvgl_gui](https://github.com/stepansnigirev/tiny_lvgl_gui)

The tiny gui library hides all the nasty details, so we need only a few lines of code:

- init the gui (`gui.init()`)
- define a button callback - function that will be called when the button is clicked
- create a label
- create a button
- go to the infinite loop to update the gui

```cpp
#include <mbed.h>
#include <gui.h>

GUI gui;     /* our GUI instance */
Label lbl;   /* label in the global scope, we will change text from the button callback */
int cnt = 0; /* click counter */

/* button callback */
static lv_res_t callback(lv_obj_t * btn){
  cnt++;
  char msg[40];
  sprintf(msg, "Button clicked %d times!", cnt);
  lbl.text(msg);
  return LV_RES_OK;
}

int main() {

  gui.init();

  /* Create a label to log clicks */
  lbl = Label("Hello display!");
  lbl.size(gui.width(), 100); // full width
  lbl.position(0, 200);
  lbl.align_text(ALIGN_TEXT_CENTER);

  /* Create a button */
  Button btn(callback, "Click me!");
  btn.size(300, 100);
  btn.position(0, 300);
  btn.align(ALIGN_CENTER);

  while(1) {
    gui.update();
  }
}
```

## Hints

We should create 4 buttons, one for every LED. As every button does the same stuff we don't want to rewrite the function 4 times. We can assign a unique number to the button and then read it using `button.id(i)`. You can get this value from the callback function. This function gets the pointer to the lvgl object, but it can be converted to a Button class using `Button button(btn)`. After this you have access to `button.id()` and other methods we used befode.

## Solution

[Solution code](https://os.mbed.com/users/stepansnigirev/code/2_display_solved/), [explanation](./solved.md)
