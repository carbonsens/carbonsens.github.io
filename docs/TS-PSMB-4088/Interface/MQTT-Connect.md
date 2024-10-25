---
title: MQTT连接
sort: 2
---

# MQTT连接概述

## 1. 通信架构
设备端通过与MQTT服务器建立连接，服务器通过MQTT订阅获取设备信息。详细协议说明请参考[MQTT接口文档](./MQTT-Interface)。

测试软件使用 [MQTTX](https://mqttx.app/zh/downloads)
推荐服务器使用 [EMQX](https://www.emqx.com/zh/downloads-and-install/broker)

## 2. 版本支持
当前最新版本为3.0.x，完整版本列表：
- [3.0.x](./MQTT-Interface/3.0.x.md) - 最新版本，支持新增配置项、自动校准等功能
- [2.3.x](./MQTT-Interface/2.3.x.md) - 支持基础功能
- [2.2.x](./MQTT-Interface/2.2.x.md) - 支持曲线发送功能
- [2.1.x](./MQTT-Interface/2.1.x.md) - Bug修复版本
- [2.0.x](./MQTT-Interface/2.0.x.md) - 首次发布版本

## 3. 主要功能
- 设备状态监测
- 实时数据上报
- 配置项管理
- 远程控制
- 固件升级
- 日志管理

## 4. 相关文档
- [配置项接口](./BlufiConfigItem-Interface)