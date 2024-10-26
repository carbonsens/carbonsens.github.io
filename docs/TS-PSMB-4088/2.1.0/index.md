---
title: 2.1.0
---
# 监护垫2.0版本 - MQTT接口文档

## 1. 概述和目标
欢迎查阅监护垫2.0版本的MQTT协议接口文档。我们设计了这个文档，目的是为了帮助开发者更好地理解和实现监护垫2.0版本MQTT接口，实现从MQTT服务器获取和解析监护垫2.0版本设备信息。

## 2. 协议版本

当前使用的协议版本是2.0.x，遵循x.x.x版本格式。其中，x.x.x 版本格式表示主版本号、次版本号和修订号，分别代表协议的重大变更、功能更新和修复bug。

| 版本   | 更新说明                                                                                                        | 负责人    |
|--------|----------------------------------------------------------------------------------------------------------------|-----------|
| 2.1.0  | 修复一些bug | LanceChan  |
| 2.0.5  | 修复一些bug | LanceChan  |
| 2.0.4  | 修改一些参数说明<br>breathewarn-se和bodymove-se的取值范围<br>添加设备设置的示例 | LanceChan  |
| 2.0.3  | 修改服务器访问地址<br>修改一些参数说明 | LanceChan  |
| 2.0.2  | 优化数据接口<br>每个接口增加了设备ID<br>补充参数说明<br>增加了体动强度 | LanceChan  |
| 2.0.1  | 首次发布MQTT版本，它包含了连接、加密、升级、设备在离床监测、心率呼吸率、呼吸异常、RR值计算、体动等功能 | LanceChan  |
| 1.x.x  | 基于UDP和HTTP通信                                                                                            | LanceChan       |
| 0.x.x  | 内部研发版本                                                                                                    | --       |


## 3. 通信架构
在我们的架构中，设备端通过SSL与MQTT服务器建立连接，服务器进一步通过MQTT订阅获取设备信息。

## 4. 连接参数


## 5. 消息格式
我们定义了多种类型的消息格式，以适应不同的场景需求。

### 5.1 设备连接信息
设备连接时，会发布如下格式的信息：
类型：设备发布
主题: /dev/{devId}/online
通信质量：设备与MQTT服务器QoS1
```json
{
	"devName": "TS-PSMB-2306B",
	"devId": "TSKJA1B9A954",
	"devVersion": "2.0.1",
	"online": true,
	"updateTime": 1665285245,
}
```

| 字段名 | 类型 | 取值 | 解释 |
| --- | --- | --- | --- |
| devName | 字符串 | "TS-PSMB-2306B"等 | 设备型号 |
| devId | 字符串 | "TSKJxxxx"等 | 设备ID |
| devVersion | 字符串 | "2.0.1"等 | 设备软件版本 |
| online | bool | true | 设备在线状态 |
| updateTime | 整数 | Unix 时间戳 | 数据更新时间 |

使用场景：发现设备上线

---

### 5.2 设备下线信息
设备下线时，会发布如下格式的信息：
类型：遗嘱信息
主题: /dev/{devId}/online
通信质量：设备与MQTT服务器QoS1
```json
{
    "devId": "TSKJA1B9A954",
    "online": false,
    "updateTime": 1665285245
}
```
| 字段名 | 类型 | 取值 | 解释 |
| --- | --- | --- | --- |
| devId | 字符串 | "TSKJxxxx"等 | 设备ID |
| online | bool | false | 设备离线状态 |
| updateTime | 整数 | Unix 时间戳 | 数据更新时间 |
使用场景：发现设备离线
### 5.3 设备设置
当需要对设备进行设置，具体协议如下。配置key-value会修改设备内部value数据，配置cmd会执行特殊操作，cmd是非必要填写。
类型: 设备订阅
主题: /dev/{devId}/config
数据大小：限制不超过384字节
```json
{
    "{key}": {value},
    "cmd": ""
}
```

key表:

| key名 | 取值 | 解释 | 必要性 |
| --- | --- | --- | --- |
| maxupatetime | 1-65535(默认30) | 最大上报时间，单位秒 | 非必要 |
| maxleavetime | 1-65535(默认60) | 离床异常时间，单位秒 | 非必要 |
| maxbreathrate | 1-35(默认30) | 呼吸率高阈值，单位次数 | 非必要 |
| minbreathrate | 1-35(默认5) | 呼吸率低阈值，单位次数 | 非必要 |
| maxheartrate | 1-135(默认110) | 心率高阈值，单位次数 | 非必要 |
| minheartrate | 1-135(默认50) | 心率低阈值，单位次数 | 非必要 |
| rr-count | 5-25(默认10) | RR统计数量值（会影响心率计算速率和准确性） | 非必要 |
| breathewarn-se | 1-10(默认5)<br>1:最不灵敏<br>10:最灵敏 | se=Sensitivity,呼吸异常的灵敏度 | 非必要 |
| bodymove-se | 1-10(默认5)<br>1:最不灵敏<br>10:最灵敏 | se=Sensitivity,体动识别的灵敏度 | 非必要 |
| ota-url | - | 用于ota升级的镜像文件地址 | 非必要 |
| mac_temperature | 默认无 | 过滤温度贴产品的mac地址 | 非必要 |

---

cmd表:

| cmd | 解释 |
| --- | --- |
| reboot | 重启 |
| ota | ota升级 |
| rconfig | 读取配置 |
| wconfig | 写入配置 |

---

#### 示例一：写配置

**发送**
/dev/TSKJ12345678/config
```json
{
  "maxupatetime": 30,
  "maxleavetime": 60,
  "maxbreathrate": 30,
  "minbreathrate": 5,
  "maxheartrate": 110,
  "minheartrate": 50,
  "rr-count": 10,
  "breathewarn-se": 5,
  "bodymove-se": 5,
  "cmd":"wconfig"
}
```
**回复**
/dev/TSKJ12345678/config/response
```json
{
  "devId": "TSKJ12345678",
  "maxupatetime": 30,
  "maxleavetime": 60,
  "maxbreathrate": 30,
  "minbreathrate": 5,
  "maxheartrate": 110,
  "minheartrate": 50,
  "rr-count": 10,
  "breathewarn-se": 5,
  "bodymove-se": 5,
  "cmd": "wconfig",
  "updateTime": "1689748716"
}
```

#### 示例二：读配置
**发送**
/dev/TSKJ12345678/config
```json
{
  "cmd":"rconfig"
}
```
**回复**
/dev/TSKJ12345678/config/response
```json
{
  "devId": "TSKJ12345678",
  "maxupatetime": 30,
  "maxleavetime": 60,
  "maxbreathrate": 30,
  "minbreathrate": 5,
  "maxheartrate": 110,
  "minheartrate": 50,
  "rr-count": 10,
  "breathewarn-se": 5,
  "bodymove-se": 5,
  "cmd": "rconfig",
  "updateTime": "1689748842"
}
```
#### 示例三：重启
**发送**
/dev/TSKJ12345678/config
```json
{
  "cmd":"reboot"
}
```
**回复**
/dev/TSKJ12345678/config/response
```json
{
  "devId": "TSKJ12345678",
  "updateTime": "1689748882",
  "cmd": "reboot"
}
```

### 5.4 设备设置响应
类型：设备发布
主题: /dev/{devId}/config/response
通信质量：设备与MQTT服务器QoS1
```json
{
    "devId": "TSKJA1B9A954",
    "{key}": {new value},
    "cmd": "Option",
    "updateTime": 1665285245,
}
```
将设备中更新好的设置数据，或者即将响应的命令，发出来回应；
### 5.5 设备上报状态
类型: 设备发布
主题: /dev/{devId}/state
通信质量：设备与MQTT服务器QoS1
```json
{
    "devId": "TSKJA1B9A954",
    "updateTime": 1665285245, //数据更新时间
    "bodymove": false, //体动标志
    "bodymoveStrength": 0.0, //体动强度
    "breathRate": 0, //呼吸率数值
    "heartRate": 0, //心率数值
    "temperature": 0, //温度数值
    "empty": true, //离床标志  true：离床  false：在床
    "leaveTime": 1685770686, //离床时间记录
    "emptyWarn": true, //离床超过设定时间标志
    "heartRateLow": false, //心率过低标志
    "heartRateHigh": false, //心率过高标志
    "breatheRateLow": false, //呼吸率过低标志
    "breatheRateHigh": false, //呼吸率高低标志
    "breatheWarn": false, //呼吸异常标志
}
```

| 字段名 | 类型 | 取值 | 解释 |
| --- | --- | --- | --- |
| devId | 字符串 | "TSKJxxxx"等 | 设备ID |
| updateTime | 整数 | Unix 时间戳 | 数据更新时间 |
| bodymove | bool | true：体动超设定阈值<br>false：体动未设定阈值 | 体动标志 |
| bodymoveStrength | 浮点 | 0.0：无体动强度<br>0.0~1.0：体动强度值<br>1.0：检测强度上限 | 体动强度 |
| breathRate | 整数 | 0：无效值<br>5~35：有效值 | 呼吸率数值 |
| heartRate | 整数 | 0：无效值<br>45~135：有效值 | 心率数值 |
| temperature | 浮点 | 0：无效值<br>20.0~40.0：有效值 | 读取附近温度贴的数值上报 |
| empty | bool | true：离床<br>false：在床 | 离床标志 |
| leaveTime | 整数 | Unix 时间戳 | 最近一次离床时间记录 |
| emptyWarn | bool | true：离床时间超过设定值<br>false：离床时间未超过设定值 | 离床超过设定时间标志 |
| heartRateLow | bool | true：心率低于设定阈值<br>false：心率未低于设定阈值 | 心率过低标志 |
| heartRateHigh | bool | true：心率高于设定阈值<br>false：心率未高于设定阈值 | 心率过高标志 |
| breatheRateLow | bool | true：呼吸率低于设定阈值<br>false：呼吸率未低于设定阈值 | 呼吸率过低标志 |
| breatheRateHigh | bool | true：呼吸率高于设定阈值<br>false：呼吸率未高于设定阈值 | 呼吸率过高标志 |
| breatheWarn | bool | true：检测到呼吸异常<br>false：未检测到呼吸异常 | 呼吸异常标志 |

上报频率：除了updateTime，这里有任何一个数值的更新，设备就会上报；如果超过maxupatetime没数据更新，会主动报一次数据。

### 5.6 设备上报日志
类型: 设备发布
主题: /dev/{devId}/log
通信质量：设备与MQTT服务器QoS1
```json
{
    "devId": "TSKJA1B9A954",
    "message": "Equipment in normal operation",
    "updateTime" : 1665285245
}
```

## 6. 最佳实践
我们将在后续版本中提供使用示例，以便开发人员可以更好地理解和实施协议。

## 7. 错误处理
对于可能遇到的错误情况，设备将发布具体的错误代码和错误信息。我们建议在遇到错误时，重新发送请求或联系我们的技术支持。

## 8. 附录
### 8.1 设备规格书

### 8.2 MQTT协议

### 8.3 MQTT服务器

### 8.4 常见问题解答(FAQ)



