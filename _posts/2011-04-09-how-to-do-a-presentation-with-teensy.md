---
layout: post
title: How to do a presentation with Teensy
post_id: 90
categories: 
- hack
- lol
- presentations
- teensy
- tools
---

**Note: I do not care if you want to share or translate the document, but please, quote me 
![;)](http://s1.wp.com/wp-includes/images/smilies/icon_wink.gif?m=1221158483g) . Thanks!**


### Brief

I am going to tell you how to do a presentation with 
[Teensy](http://www.pjrc.com/store/teensy.html), a USB development board that let automatize keystrokes and mouse movements. There are a lot of tools for doing presentations, like Beamer (LaTeX), MS PowerPoint, LibreOffice Presentation, 
[Prezi](http://prezi.com/), ... but I wanted to expose a bit more creative.

### What do you need?

This is the toolkit we need:

[![](/images/2011/04/teensy_tools.png?w=300)](/images/2011/04/teensy_tools.png)

As you can see, the most important thing is the Teensy board, that you will have to buy it in the official webpage. The other things are just to do it better. But we need some software tools too. I will be using the 
[Teensy Loader Application](http://www.pjrc.com/teensy/first_use.html) and the 
[Teensyduino IDE](http://www.pjrc.com/teensy/teensyduino.html). (I am sorry but I cannot explain here all that you need to configure it. You can find all the instructions in the links. It is easy.)

### How to do it

Well, Teensy does it a bit hard when you do not have a U.S. keyboard, so you will be have to write less code if you have this keyboard. I will send any special key with a ALT+XX combination (lol, yeah).

I will paste a code to print some lines and you will understand what is the idea:

```cpp
#include <usb_private.h>

void setup() { }

void loop() {

// Hold until the keyboard is initiallized
while(Keyboard.isInit()){
Keyboard.set_key1(KEY_NUM_LOCK);
Keyboard.send_now();
delay(500);
}

// We open the notepad ( Gui + R -> notepad -> enter )
Keyboard.set_modifier(MODIFIERKEY_GUI);
Keyboard.set_key1(KEY_R);
Keyboard.send_now();
sendNull();
delay(1000);

Keyboard.print("notepad");
enter();

// maximize window ( Alt + Space -> x )
Keyboard.set_modifier(MODIFIERKEY_ALT);
Keyboard.set_key1(KEY_SPACE);
Keyboard.send_now();
sendNull();
delay(200);

Keyboard.set_key1(KEY_X);
Keyboard.send_now();
sendNull();
delay(200);

// we change the font-size ( Alt + o -> f -> Alt + m -> "50" -> Enter )
Keyboard.set_modifier(MODIFIERKEY_ALT);
Keyboard.set_key1(KEY_O);
Keyboard.send_now();
sendNull();
delay(200);

Keyboard.set_key1(KEY_F);
Keyboard.send_now();
sendNull();
delay(200);

Keyboard.set_modifier(MODIFIERKEY_ALT);
Keyboard.set_key1(KEY_M);
Keyboard.send_now();
sendNull();
delay(200);

Keyboard.print("50");
enter();

// here starts the presentation

Keyboard.print("I am Martes13");
enter();

numCapsOn();
while (isNumCapsOn());

Keyboard.print("and this presentation is quite simple");
enter();

numCapsOn();
while (isNumCapsOn());

Keyboard.print("isnt it");

// We need to send the character "?" . It is ALT+63

Keyboard.set_modifier(MODIFIERKEY_ALT);
Keyboard.set_key1(KEYPAD_6);
Keyboard.send_now();
Keyboard.set_key1(KEYPAD_3);
Keyboard.send_now();
sendNull();

enter();

while(true);
}

// Release the key
void sendNull() {
Keyboard.set_modifier(0);
Keyboard.set_key1(0);
Keyboard.send_now();
}

// Sends the Enter key
void enter() {
delay(200);
Keyboard.set_key1(KEY_ENTER);
Keyboard.send_now();
sendNull();
delay(200);
}

// Checks if the NUM is on
bool isNumLockOn() {
return bitRead(int(keyboard_leds), 0);
}

// Checks if the CAPS is on
bool isCapsLockOn() {
return bitRead(int(keyboard_leds), 1);
}

// Sends the CAPS LOCK key
void putCapsLockOn() {
Keyboard.set_key1(KEY_CAPS_LOCK);
Keyboard.send_now();
delay(1000);
}
```

Ok, there are some important things to tell:

- This is a simple example. 
**You can do WHATEVER you could do with a KEYBOARD and a MOUSE**
. It is really useful if you want to do a live presentation through some different applications. From now, you cannot have an excuse to say "I tested in home and it worked". Noo.

- 
**How to stop the presentation is the most important detail**
. I am using the CAPS LOCK KEY. When I want to code it, I have to activate the key and wait ( while (isNumCapsOn()); ) until the user deactivates it. It is the WHY of the Bluetooth keyboard ;).

- You can forget the last line (how to stop it) and do it ALL with a delay(-(int)time-); function, but if you have any problem and it goes too fast 
**I hope you will have an alternative presentation**
.

It is only an introduction to this kind of live-awesome presentations, but you will have spend quite time coding it. It should be nice to have a good library... but it does not exist yet.

Greets!

and if you have any question, contact me, of course!!


**Note: If you see any spell mistakes, you can say me it. I would appreciate it 
![;)](http://s1.wp.com/wp-includes/images/smilies/icon_wink.gif?m=1221158483g) . Thanks!**