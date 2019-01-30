# Solution to Step 1: Driving all the LEDs

[Code](https://os.mbed.com/users/stepansnigirev/code/1_blinky_solved/)

First, we define an array of leds and a counter that we will use to determine which of the LEDs should shine:

```cpp
DigitalOut leds[] = { (LED1), (LED2), (LED3), (LED4) };
int current_led = 0;
```

Now in the main loop we just increase the counter and shine a corresponding LED:

```cpp
int main(){
  for(int i=0; i<4; i++){
    if(i==current_led){
      leds[i] = HIGH; // turn on the current LED
    }else{
      leds[i] = LOW; // all other LEDs should stay off
    }
  }
  current_led = (current_led + 1) % 4;
  wait(0.1);
}
```

We use `% 4` (modulo 4) to keep the `current_led` variable in a range from `0` to `3`.

# Solution to Step 1: Control direction with a button

[Code](https://os.mbed.com/users/stepansnigirev/code/1_blinky_solved_btn/)

We define our button as `DigitalIn` class:

```cpp
DigitalIn button(USER_BUTTON);
```

Then we change the code above such that we increase the counter if the button is not pressed, and decrease otherwise:

```cpp
int main(){
  for(int i=0; i<4; i++){
    if(i==current_led){
      leds[i] = HIGH; // turn on the current LED
    }else{
      leds[i] = LOW; // all other LEDs should stay off
    }
  }
  if(button){
    current_led = (current_led+3) % 4; // +3 mod 4 is the same as -1 mod 4, but prevents current_led to go negative
  }else{
    current_led = (current_led+1) % 4;
  }
  wait(0.1);
}
```

Adding `3` modulo `4` does the same as substracting `1`, but keeps the `current_led` positive all the time.
