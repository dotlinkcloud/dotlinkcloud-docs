# 设备开发指导-RTOS

DotlinkCloud不提供联网固件，提供了多种RTOS的设备SDK。 硬件厂商可以集成SDK到联网固件（WIFI, GPRS）模块中，实现和云端连接。

* 云端控制台提供了数据点的定义，可以定义硬件的功能。
* 联网固件和云端按照数据点协议进行加密通信，而MCU通过串口和联网固件进行通信。
* 厂商只需要在MCU上开发自己的设备控制逻辑，不用开发相关的联网控制逻辑，所有的联网控制逻辑都由设备SDK来实现。
* 厂商只要按照协议要求，进行通信控制即可。


## 产品定义

## 配置设备基本信息

## 设备初始化

## 设备绑定管理

### 1. 设备绑定

### 2. 设备解绑

## 云端通信

### 1. 设备上报

### 2. 设备接收