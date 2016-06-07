# 设备SDK RTOS

针对RTOS平台的设备，DotlinkCloud提供了设备SDK，只要使用SDK开发，即可让设备连接到DotlinkCloud的云端。

## 支持平台
* Linux : 支持POSIX标准的平台，例如 Ubuntu、树莓派。
* 其他平台 : 后续支持，例如：Broadcom WICED等。

## 文件目录

```
.
├── adapter							// 适配层
│   └── linux   	
│   	  ├── dlinkmq_types.h    	// 变量类型定义
│   	  └── dlinkmq_linux.c    	// memory,socket等实现  
├── dist                  			// 编译输出目录
├── inc                   			// 打包产出
│   ├── dlinkmq_api.h     			// 结构体和接口头文件
│   └── dlinkmq_error.h   			// 错误码头文件
├── src                       		// 源码
└── test                      		// 测试用例
	 └── main.c                		// 测试入口
```


## 数据结构 

### 1. 设备信息

1.1 `dlinkmq_device_info` 结构定义如下：

```
#define DlinkMQ_product_id_len 40
#define DlinkMQ_product_secret_len 40
#define DlinkMQ_device_id_len 40
typedef struct
{
    we_int8	product_id[DlinkMQ_product_id_len];
    we_int8	product_secret[DlinkMQ_product_secret_len];
    we_int8	device_id[DlinkMQ_device_id_len];
} dlinkmq_device_info;

```
参数含义如下：

名称|作用
---|---
product_id | 产品ID：云端分配给厂商同种类型设备的唯一标识。
product_secret | 产品密钥：与产品ID相对应。
device_id | 设备ID：每个设备的唯一标识。

### 2. 数据点消息

2.1 `dlinkmq_datapoint_head` 消息头结构定义如下：

```
typedef struct
{
    we_int8    version[20];
    we_uint32   cmd_id;
    we_uint16   msg_id;
    we_int8    *cmd_value;
    we_uint32   cmd_value_len;
    we_uint32   seq;
    we_int32    ret_code;
} dlinkmq_datapoint;

```
参数含义如下：

名称|作用
---|---
version | 协议版本号
cmd_id | 属性ID：云端定义的设备功能点的唯一标识。
cmd_value | 属性value：ID所对应的值。类型有bool, int, string, binary。
value_len | 属性value的长度：数据长度。
seq | 操作序号
ret_code | 回复结果：如果需要回复，回复设备的处理结果给云端。

### 3. 接收数据点回调

3.1`dlinkmq_on_receive ` 结构定义如下：

```
typedef struct
{
    void (*on_receive_datapoint)(dlinkmq_datapoint *datapoint);
} dlinkmq_on_receive;
```

参数含义如下：

名称|作用
---|---
on_receive_datapoint | 接收数据点回调
datapoint | 数据点结构

### 4. 发送数据点回调

4.1`dlinkmq_on_send ` 结构定义如下：

```
typedef struct
{
    void (*on_send_msg)(we_int32 err_code, dlinkmq_datapoint *datapoint);
} dlinkmq_on_send;
```

参数含义如下：

名称|作用
---|---
on_send_msg | 发送数据点回调
err_code | 0:成功，1:失败
msg_id | 调用发送函数时传入，用于标记消息

## 接口定义
### 1. 设备初始化
定义

```
we_int32 dlinkmq_init_device_info(dlinkmq_device_info *device_info, dlinkmq_on_receive fun_cb);
```
说明
 
 设备初始化，创建TCP服务与云端建立连接获取和MQTT连接所需的信息。

参数

字段|说明
---|---
info | 设备信息

返回

返回值|含义
---|---
DlinkMQ_ERROR_CODE_SUCCESS | 成功
其他 | 失败

### 2. 主循环函数

定义

```
void dlinkmq_run();
```
说明
 
 初始化成功后，设备与MQTT服务器的连接、接收消息都在这里面完成。

### 3. 发送消息

定义

```
we_int32 dlinkmq_publish(dlinkmq_datapoint *datapoint, we_int8 *topic, we_int32 qos, dlinkmq_on_send fun_cb);
```
说明
 
 设备上报、回复数据点

参数

字段|说明
---|---
datapoint | 数据点消息
topic | 发送消息的频道
qos | 消息发布服务质量. 0：“至多一次”，1：“至少一次”，2：“只有一次”
fun_cb | 发送是否成功的回调函数

返回

返回值|含义
---|---
DlinkMQ_ERROR_CODE_SUCCESS | 成功
其他 | 失败

## 状态码

状态码|含义
---|---
DlinkMQ_ERROR_CODE_SUCCESS | 成功
DlinkMQ_ERROR_CODE_PARAM | 参数错误
DlinkMQ_ERROR_CODE_SOC_CREAT | 创建网络失败
DlinkMQ_ERROR_CODE_SOC_CONN | 网络连接失败
DlinkMQ_ERROR_CODE_DATA | 数据异常