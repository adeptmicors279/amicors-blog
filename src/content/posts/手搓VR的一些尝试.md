---
title: 手搓VR的一些尝试
description: 持续更新
published: 2026-03-18
tags:
  - VR
---
本文章参考以下项目
::github{repo="TrueOpenVR/TrueOpenVR-DIY"}

::github{repo="opentrack/opentrack"}

:::warning
由于本人完全没学过cpp
所以本文会引用部分AI编写的程序
:::


准备材料
- Arduino nano
- MPU6050 GY-521 (待定)
- OpenTrace (软件)
- Freepie
- ESP32
- 摄像头
- HDMI屏幕


# HMD
首先，使用arduino开发板连接GY521

并且写入程序
```c++
collapse={1-5}
#include "I2Cdev.h"
#include "MPU6050_6Axis_MotionApps20.h"
#if I2CDEV_IMPLEMENTATION == I2CDEV_ARDUINO_WIRE
#include "Wire.h"
#endif
MPU6050 mpu;
#define INTERRUPT_PIN 2

  

bool dmpReady = false;

uint8_t mpuIntStatus;

uint8_t devStatus;

uint16_t packetSize;

uint16_t fifoCount;

uint8_t fifoBuffer[64];

Quaternion q;

VectorFloat gravity;

float ypr[3];


volatile bool mpuInterrupt = false;

void dmpDataReady() {

    mpuInterrupt = true;

}

  

typedef struct

{

  int16_t Begin;

  uint16_t Cpt;

  float YPR[3];

  float XYZ[3];

  int16_t End;

} type_hat;

  

type_hat hat;

  

float yaw0=0, pitch0=0, roll0=0;

bool calibrated=false;

  

void setup() {

  

    Wire.begin();

    Wire.setClock(400000);

  

    Serial.begin(115200);

  

    mpu.initialize();

    pinMode(INTERRUPT_PIN, INPUT);

  

    devStatus = mpu.dmpInitialize();

  

    mpu.setXGyroOffset(164);

    mpu.setYGyroOffset(-151);

    mpu.setZGyroOffset(-16);

    mpu.setXAccelOffset(-2174);

    mpu.setYAccelOffset(-162);

    mpu.setZAccelOffset(785);

  

    if (devStatus == 0) {

  

        mpu.setDMPEnabled(true);

  

        attachInterrupt(digitalPinToInterrupt(INTERRUPT_PIN), dmpDataReady, RISING);

  

        mpuIntStatus = mpu.getIntStatus();

  

        dmpReady = true;

  

        packetSize = mpu.dmpGetFIFOPacketSize();

    }

  

    hat.Begin = 0xAAAA;

    hat.End = 0x5555;

    hat.Cpt = 0;

  

    delay(2000); // 让传感器稳定

}

  

void calibrateZero()

{

    float sumYaw=0,sumPitch=0,sumRoll=0;

  

    for(int i=0;i<200;i++)

    {

        while (!mpuInterrupt);

        mpuInterrupt=false;

  

        fifoCount = mpu.getFIFOCount();

        if(fifoCount<packetSize) continue;

  

        mpu.getFIFOBytes(fifoBuffer, packetSize);

  

        mpu.dmpGetQuaternion(&q, fifoBuffer);

        mpu.dmpGetGravity(&gravity, &q);

        mpu.dmpGetYawPitchRoll(ypr, &q, &gravity);

  

        sumYaw+=ypr[0];

        sumPitch+=ypr[1];

        sumRoll+=ypr[2];

  

        delay(5);

    }

  

    yaw0=sumYaw/200;

    pitch0=sumPitch/200;

    roll0=sumRoll/200;

  

    calibrated=true;

}

  

void loop() {

  

    if (!dmpReady) return;

  

    if (!mpuInterrupt && fifoCount < packetSize) return;

  

    mpuInterrupt=false;

    mpuIntStatus = mpu.getIntStatus();

  

    fifoCount = mpu.getFIFOCount();

  

    if ((mpuIntStatus & _BV(MPU6050_INTERRUPT_FIFO_OFLOW_BIT)) || fifoCount >= 1024) {

  

        mpu.resetFIFO();

        return;

    }

  

    if (mpuIntStatus & _BV(MPU6050_INTERRUPT_DMP_INT_BIT)) {

  

        while (fifoCount < packetSize) fifoCount = mpu.getFIFOCount();

  

        mpu.getFIFOBytes(fifoBuffer, packetSize);

        fifoCount -= packetSize;

  

        mpu.dmpGetQuaternion(&q, fifoBuffer);

        mpu.dmpGetGravity(&gravity, &q);

        mpu.dmpGetYawPitchRoll(ypr, &q, &gravity);

  

        float yaw = ypr[0] * 180 / M_PI;

        float pitch = ypr[1] * 180 / M_PI;

        float roll = ypr[2] * 180 / M_PI;

  

        if(!calibrated)

        {

            calibrateZero();

        }

  

        hat.YPR[0] = yaw - yaw0;

        hat.YPR[1] = pitch - pitch0;

        hat.YPR[2] = roll - roll0;

  

        hat.XYZ[0] = 0;

        hat.XYZ[1] = 0;

        hat.XYZ[2] = 0;

  

        hat.Cpt++;

        if(hat.Cpt>999) hat.Cpt=0;

  

        Serial.write((byte*)&hat,30);

    }

}
```
True open VR里那个程序我嫌太麻烦了还要校准，所以用这个直接上手了

然后安装opentrace选hatire arduino

根据情况调整反转

一个VR HMD的旋转装置就做好了

注： 由于GY521没有磁力加速计所以会导致轻微便宜，也因此我在GY521后加了待定

# Controller

由于线不能太多于是选择ESP32进行开发

刚好ESP32支持microPython，我可以更方便的写程序（不用依赖AI了）

（以下还未完成，后续会继续写）