---
title: Hello
layout: default

nav:
  level_1:
    - title: 'Подключение'
      url: '#h1'
      level_2:
        - title: 'subtitle'
          url: '#h2'
    - title: 'Использование'
      url: '#'
    - title: 'Проекты'
      url: '#'
---

# nRF24L01

::: warning
Это заготовка страницы
:::

## Подключение

| nRF24L01+ | ARDUINO |
| :-------: | :-----: |
| GND       | GND     |
| VCC       | 3V3     |
| CE        | D9      |
| CSN       | D10     |
| SCK       | D13     |
| MOSI      | D11     |
| MISO      | D12     |
| IRQ       | ...     |

## Использование
## Проекты
В кодах ниже передаём сигнал с джойстика на моторчик (скоростьь). Не помню через, что именно контролили всс или гнд

| MOTOR | ARDUINO |
| :---: |:-------:|
| VCC   | D3~     |
| GND   | GND     |

Хрень подключается через злоебучие мосфеты повышайки. А питается от внешнего 12v+ аккума

[![Схема подключения двигателя к arduino](https://orpi.borland.ml/codelab/images/Untitled_Sketch_bb.png "Схема подключения двигателя к arduino")](https://orpi.borland.ml/codelab/images/Untitled_Sketch_bb.png)

```c
/*
https://lesson.iarduino.ru/page/urok-26-4-soedinyaem-dve-arduino-po-radiokanalu-cherez-nrf24l01/

ПРИЁМНИК
*/

#include <nRF24L01.h>
#include <RF24.h>

// PWM Pin
#define motor_out 3
// Пины приёмника
RF24 radio(9, 10);

int data[4];// Может быть массивом
int speed;

int joy_x;
int joy_y;
int joy_btn;

void setup(){
  Serial.begin(9600);
  pinMode(motor_out, OUTPUT);
  radio.begin();
  radio.setChannel(5);// Указываем канал приёма данных (от 0 до 127), 5 - значит приём данных осуществляется на частоте 2,405 ГГц (на одном канале может быть только 1 приёмник и до 6 передатчиков)
  radio.setDataRate(RF24_250KBPS);// Указываем скорость передачи данных (RF24_250KBPS, RF24_1MBPS, RF24_2MBPS), RF24_1MBPS - 1Мбит/сек
  radio.setPALevel(RF24_PA_HIGH);// Указываем мощность передатчика (RF24_PA_MIN=-18dBm, RF24_PA_LOW=-12dBm, RF24_PA_HIGH=-6dBm, RF24_PA_MAX=0dBm)
  radio.openReadingPipe(1, 0x1234567890LL);// Открываем 1 трубу с идентификатором 0x1234567890 для приема данных (на ожном канале может быть открыто до 6 разных труб, которые должны отличаться только последним байтом идентификатора)
  radio.startListening();
}

void loop(){
  if(radio.available()){
    radio.read(&data, sizeof(data));
    speed = map(data[0], 0, 1023, 0, 255);
    //
    joy_x = data[1];
    joy_y = data[2];
    joy_btn = data[3];
    //
    analogWrite(motor_out, speed);
    Serial.print("[#] Current power is ");
    Serial.print(speed * 100 / 255);
    Serial.println("%");
    //
    Serial.print("X ");
    Serial.println(joy_x);
    Serial.print("Y ");
    Serial.println(joy_y);
    Serial.print("BTN ");
    Serial.println(joy_btn);
  }
  delay(5);
}

```

---

| JOYSTICK | ARDUINO |
| :------: | :-----: |
| BTN      | D2      |
| X        | A5      |
| Y        | A4      |
| VCC      | 5V      |
| GND      | GND     |

[![Схема подключения джойстика к arduino](https://orpi.borland.ml/codelab/images/Untitled_Sketch_1_bb.png "Схема подключения джойстика к arduino")](https://orpi.borland.ml/codelab/images/Untitled_Sketch_1_bb.png)


```c
/*
https://lesson.iarduino.ru/page/urok-26-4-soedinyaem-dve-arduino-po-radiokanalu-cherez-nrf24l01/

ПЕРЕДАТЧИК


joystick
  x - A5
  y - A4
  btn - D2
*/

#include <nRF24L01.h>
#include <RF24.h>

//Joystick
#define joy_x A5
#define joy_y A4
#define joy_btn 2
// PWM Pin
#define potentiometer_in A3
// Пины приёмника
RF24 radio(9, 10);

int data[4];// Может быть массивом
int speed;

void setup(){
  Serial.begin(9600);
  pinMode(joy_x, INPUT);
  pinMode(joy_y, INPUT);
  pinMode(joy_btn, INPUT);
  pinMode(potentiometer_in, INPUT);
  radio.begin();
  radio.setChannel(5);// Указываем канал приёма данных (от 0 до 127), 5 - значит приём данных осуществляется на частоте 2,405 ГГц (на одном канале может быть только 1 приёмник и до 6 передатчиков)
  radio.setDataRate(RF24_250KBPS);// Указываем скорость передачи данных (RF24_250KBPS, RF24_1MBPS, RF24_2MBPS), RF24_1MBPS - 1Мбит/сек
  radio.setPALevel(RF24_PA_HIGH);// Указываем мощность передатчика (RF24_PA_MIN=-18dBm, RF24_PA_LOW=-12dBm, RF24_PA_HIGH=-6dBm, RF24_PA_MAX=0dBm)
  radio.openReadingPipe(1, 0x1234567890LL);// Открываем 1 трубу с идентификатором 0x1234567890 для приема данных (на ожном канале может быть открыто до 6 разных труб, которые должны отличаться только последним байтом идентификатора)
}

void loop(){
  data[0] = analogRead(potentiometer_in);
  data[1] = analogRead(joy_x);
  data[2] = analogRead(joy_y);
  data[3] = digitalRead(joy_btn);
  radio.write(&data, sizeof(data));
  delay(5);
}

```
