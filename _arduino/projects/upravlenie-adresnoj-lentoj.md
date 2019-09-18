---
title: 'Управление адресной лентой'
description: 'Управление адресной лентой'
layout: default
nav:
  level_1:
    - title: 'Подключение'
      url: '#podklyuchenie'
      level_2:
        - title: 'subtitle'
          url: '#h2'
    - title: 'Использование'
      url: '#ispolzovanie'
    - title: 'Проекты'
      url: '#proekty'
---
# Управление адресной лентой

::: warning
Это заготовка страницы
:::


> Дебильная идея управлять "кольцами" в фарах с помощью ИК-пульта, как нам вообще пришла такая идея, жееесть.

[![Схема подключения led-кольца](https://orpi.borland.ml/codelab/images/1d6edcff-4cbe-4366-bd31-e364fe8dc310.png "Схема подключения led-кольца")](https://orpi.borland.ml/codelab/images/1d6edcff-4cbe-4366-bd31-e364fe8dc310.png)




>2) Цифровой вход ленты идёт напрямую на «сырой» вход микроконтроллера внутри диода, поэтому между ним и управляющим пином ардуино нужен токоограничиваюший резистор с номиналом 200-500 ом.
>Без него есть большой шанс выгорания пина Ардуино или первого светодиода в ленте.
>Не испытывайте удачу, поставьте резистор. Точность резистора? Любая. Мощность резистора? Любая. Да, даже 1/4.
>
>[source](https://alexgyver.ru/ws2812_guide/)

| LED-RING | ARDUINO |
| :------: | :-----: |
| DIN      | D9      |
| +5V      | 5V      |
| GND      | GND     |

```c
/**
ExFastLED.h
*/

#include <FastLED.h>
#include <IRremote.h>

#define pin_irr 2
#define pin_led 9

//Для лампоШек

#define NUM_LEDS 24
CRGB leds[NUM_LEDS];
int max_brightness = 255;
int cur_brightness = max_brightness;

//Для пульта
volatile bool irr_flag;
int tmode = 0;//Режим лампоШек

IRrecv irrecv(pin_irr);
decode_results results;
unsigned long key_value = 0;


void setup(){
  Serial.begin(9600);
  
  FastLED.setBrightness(max_brightness);
  FastLED.addLeds<WS2812B, pin_led, GRB>(leds, NUM_LEDS);

  irrecv.enableIRIn();
  attachInterrupt(digitalPinToInterrupt(pin_irr), checkIR, RISING);
}
unsigned long _timer;
int co = 0;

void loop(){
  remote();//Удалённое управление
  //chmod();//Функционал режимов


  FastLED.setBrightness(cur_brightness);
  switch(tmode){
    case 1:
      leds[0] = CRGB(255, 120, 36);
      //FastLED.show();
      break;
    case 2:     
      if(co >= NUM_LEDS) co = 0;
      leds[co] = CRGB::Blue;
      leds[co == 0 ? NUM_LEDS - 1 : co - 1] = CRGB::Black;
      co++;
      delay(50);
      break;
    case 3: fillSolidColor(255,255,255); break;
    case 4: fillSolidColor(255,0,0); break;
    case 5: fillSolidColor(0,255,0); break;
    case 6: fillSolidColor(0,0,255); break;
    case 7: fillSolidColor(255,255,0); break;
    
    default: 
    //leds[0] = CRGB(0, 255, 0);
      //FastLED.show();
    break;
  }



  ///////////////////////////////////
  if(irrecv.isIdle()) FastLED.show();
  FastLED.clear();
}

/*
  Кнопки пульта
*/
#define btn_1             0x5FACA35
#define btn_2             0x5FA0AF5
#define btn_3             0x5FA48B7
#define btn_red           0x5FA42BD
#define btn_green         0x5FA827D
#define btn_blue          0x5FA00FF
#define btn_yellow        0x5FA807F
#define btn_bright_plus   0x5FA02FD
#define btn_bright_minus  0x5FABA45

void remote(){
  if(irr_flag){
    //
    if(results.value == 0xFFFFFFFF) results.value = key_value;
    //
    switch(results.value){
      case btn_1:
        tmode = 3;
        break;
      case btn_2:
        tmode = 2;
        break;
      case btn_3:
        tmode = 1;
        break;

      case btn_red:
        tmode = 4;
        break;
      case btn_green:
        tmode = 5;
        break;
      case btn_blue:
        tmode = 6;
        break;
      case btn_yellow:
        tmode = 7;
        break;
        
      case btn_bright_plus:
        cur_brightness = cur_brightness >= max_brightness ? max_brightness : cur_brightness += 50;
        break;
      case btn_bright_minus:
        cur_brightness = cur_brightness <= 0 ? 0 : cur_brightness -= 50;
        break;
      default: tmode = 999; break;
    }
    //
    key_value = results.value;
    irr_flag = false;
  }
}

void checkIR(){
  while(irrecv.decode(&results)){
    irr_flag = true;
    Serial.println(results.value, HEX);
    irrecv.resume();
  }
}


/*
  Заполняет всю ленту одним цветом
  byte: [0...255]
*/
void fillSolidColor(byte red, byte green, byte blue){
  for(int i = 0; i < NUM_LEDS; i++)
    leds[i] = CRGB(red, green, blue);
}
```
