---
title: Page2
layout: default
sidebar: test
nav:
  level_1:
    - title: 'aztitle'
      url: '#h1'
      level_2:
        - title: 'azsubtitle'
          url: '#h2'
---
# Приёмник ИК-сигнала

::: warning
Это заготовка страницы
:::













[Источник](http://www.circuitbasics.com/arduino-ir-remote-receiver-tutorial/)
Примеры Хрен знает какие ибо я уже не помню чё к чему. К чему прерывания и прочее

## Подключение

[![Схема подключения сенсора к arduino](https://orpi.borland.ml/codelab/images/9e747d22-bc0e-42f6-8e33-4ddbf544117a.png "Схема подключения сенсора к arduino")](https://orpi.borland.ml/codelab/images/9e747d22-bc0e-42f6-8e33-4ddbf544117a.png)

| IR  | ARDUINO |
| :-: | :-----: |
| OUT | D2      |
| GND | GND     |
| VCC | 5V      |

## Использование
Для работы с датчиком нам понадобится [библиотека](http://z3t0.github.io/Arduino-IRremote/).

Ниже приведены варианты использования + готовые проекты
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

### Простой шаблон <Badge text="alpha" type="warn" />
> Без применения прерываний (Я тупо не помню для чего оно)
```c
#include <IRremote.h>

IRrecv irrecv(2);
decode_results results;

unsigned long key_value = 0xF;

void setup(){
  Serial.begin(9600);
  irrecv.enableIRIn();
  irrecv.blink13(true);
}

void loop(){
  if(irrecv.decode(&results)){
    if(results.value == 0xFFFFFFFF) results.value = key_value;

    switch(results.value){
      case 0xFD00FF:
        // > Действие после нажатия
        break;
      default: break;
    }

    Serial.print("0x");
    Serial.println(results.value, HEX);

    irrecv.resume();
    key_value = results.value;
  }
}
```
## Проекты
[Управление адресной лентой](/projects/1.md) <Badge text="beta" type="warn" /><Badge text="v1" type="tip" />

