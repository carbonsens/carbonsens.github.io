---
title: Blufi连接
sort: 1
---

# 基于蓝牙近距离连接产品的接口文档 - Blufi连接

## 概述
本接口文档旨在介绍TS-PSMB-4088 基于蓝牙通道传输，采用 BluFi 协议格式实现近距离信息交互，支持传输包括 Wi-Fi SSID、密码及其他功能参数在内的配置信息。简而言之，通过该通信机制，TS-PSMB-4088 可实现网络和参数配置功能。

## 流程简述
1. 蓝牙连接建立：移动设备搜索并连接到TS-PSMB-4088
2. GATT服务发现：连接后，移动设备发现设备提供的GATT服务和特征
3. Blufi协议通信：通过特定的GATT特征，设备与移动应用进行数据通信，按照Blufi协议的格式传输数据
4. 命令操作：基于Blufi协议自定义数据帧传输，设备支持特定的命令操作，实现重启/更换服务器/设置报警值等功能

## 更详细的参考文档
- [Blufi连接的介绍](https://docs.espressif.com/projects/esp-idf/zh_CN/v5.3.1/esp32/api-guides/blufi.html)
- [安卓端参考代码](https://github.com/EspressifApp/EspBlufiForAndroid)
- [IOS端参考代码](https://github.com/EspressifApp/EspBlufiForiOS)
- [小程序/js参考代码](https://github.com/xuhongv/BlufiEsp32WeChat)

## GATT说明
在GATT协议中，使用以下服务和特征UUID实现Blufi通信：

### 服务UUID
`0xFFFF`（16 bit）

### 写特征UUID 
`0xFF01`
- 主要权限：可写
- 用于接收移动设备发送的数据

### 通知特征UUID
`0xFF02`
- 主要权限：可读可通知
- 用于向移动设备发送数据

通过以上特征，设备与移动应用之间可以进行双向的数据通信，按照Blufi协议的格式传输数据。


## 功能说明

### 配网
**功能描述**：通过蓝牙，将Wi-Fi的SSID和密码等配置信息从移动设备传输到TS-PSMB-4088，实现设备的联网。

**实现步骤**：
1. 移动设备通过写特征发送Wi-Fi配置信息
2. 设备接收到信息后，尝试连接指定的Wi-Fi网络
3. 连接成功后，设备通过通知特征反馈连接结果

### 传输自定义数据
**功能描述**：在蓝牙连接的基础上，传输特定格式的自定义数据，实现设备的特定功能。

**实现步骤**：
1. 移动设备通过写特征发送自定义数据命令
2. 设备解析命令，执行相应的操作
3. 设备通过通知特征反馈执行结果或返回数据

### 命令操作扩展
基于自定义数据传输，我们的产品TS-PSMB-4088扩展出特定的命令操作，例如：

- rconfig：读取设备当前配置信息
- wconfig：写入新的配置参数到设备
- reboot：重启设备
- ota：通过传输固件数据进行OTA升级
- resetr0：重置设备的R0基准值
- [详细接口](./BlufiConfigItem-Interface)
