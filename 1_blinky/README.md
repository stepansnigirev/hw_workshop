# Step 1. Blink with LEDs.

Code on mbed: https://os.mbed.com/users/stepansnigirev/code/1_blinky/

**Assignment**: Change the code and drive all 4 LEDs one after another. Like 1-2-3-4-1-2-3-4...

**Advanced**: Check the state of the `USER_BUTTON`, if it is currently pressed - change the direction of the blinking to 4-3-2-1-4-3-2-1.

## Explanation

When the microcontroller boots it will run the main function, this is the main entry point of our program. Outside of the main function we can define other functions and global variables.

We start by importing the `mbed.h` library. It has a bunch of very convenient classes and definitions of our hardware. To drive the first LED on our board (the green one) we define how we want to call it and then we will be able to can change it's state by assigning `0` or `1` to it's value. All the complicated stuff is hidden from us in the `DigitalOut` class, we don't even need to think what exactly is happening under the hood.

```cpp
#include "mbed.h"

DigitalOut led(LED1); // LED1 is the pin name of our first built-in LED. Others are LED2, LED3 and LED4

int main(){
  // this while loop will run forever
  while(1){
    led = 0; // switch the led value to 0
    wait(1); // wait one second
    led = 1; // switch the led value to 1
    wait(1); // wait another second
  }
}
```

If we run this code we will notice that `led=0` turns the LED on, and `led=1` turns if off. It's just because the LED is connected to the power by default, not to the ground. To make it more convenient we can define `HIGH` and `LOW` constants to `0` and `1` correspondingly, then the code will be more understandable:

```cpp
#include "mbed.h"

#define HIGH 0 // turn the LED on
#define LOW  1 // turn the LED off

DigitalOut led(LED1); // LED1 is the pin name of our first built-in LED. Others are LED2, LED3 and LED4

int main(){
  // this while loop will run forever
  while(1){
    led = HIGH; // switch the led value to 0
    wait(1); // wait one second
    led = LOW; // switch the led value to 1
    wait(1); // wait another second
  }
}
```

## Hints

As we need to drive 4 LEDs now, it makes sense to put the in an array. Otherwise the code will be awful.
We can also define a variable that will store our current active LED.

```cpp
DigitalOut leds[] = { (LED1), (LED2), (LED3), (LED4) };
int current_led = 0;
```

To read the state of the button we can use `DigitalIn` class. We can check if the button is pressed with a simple if statement `if(button){ /* do something */ }`.

```cpp
DigitalIn button(USER_BUTTON);
```

## Solution

[Solution code](https://os.mbed.com/users/stepansnigirev/code/1_blinky_solved/), [explanation](./solved.md)

[Solution with a button](https://os.mbed.com/users/stepansnigirev/code/1_blinky_solved_btn/), [explanation](./solved_btn.md)