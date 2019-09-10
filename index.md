---
title: Hello
layout: default
---

### Распознование hex-кодов с передатчика
Для этого понадобится следующий скетч, он выводит в консоль код нажатой кнопки:
```c
#include <IRremote.h>

IRrecv irrecv(2);
decode_results results;

unsigned long key_value = 0xF;

void setup(){
  Serial.begin(9600);
  irrecv.enableIRIn();
  // Будет ли встроенный лед реагировать на нажатия
  irrecv.blink13(true);
}

void loop(){
  if(irrecv.decode(&results)){
    // При зажатии кнопки выдаётся нулевой сигнал (0xF...), сие творение позволяет повторять код кнопки
    if(results.value == 0xFFFFFFFF) results.value = key_value;
    Serial.print("0x");
    Serial.println(results.value, HEX);
    irrecv.resume();
    key_value = results.value;
  }
}
```
