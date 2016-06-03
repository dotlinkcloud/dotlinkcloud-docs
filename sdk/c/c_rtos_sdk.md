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
typedef struct 
{
    we_unit8	*product_id; 		
    we_unit8	*product_secret; 
    we_unit8	*device_id; 
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
    we_unit8   version;
    we_unit32  cmd_id;          	
    we_unit8	 *cmd_value;         
    we_unit32  cmd_value_len;      
    we_unit32  seq;                  
    we_int32   ret_code;     	     
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
`typedef void (*on_receive_datapoint)(dlinkmq_datapoint *datapoint);`

参数含义如下：

名称|作用
---|---
datapoint | 数据点结构

### 4. 发送数据点回调

`typedef void (*on_send_msg)(we_int32 err_code, we_int32 msg_id);`

参数含义如下：

名称|作用
---|---
err_code | 0:成功，1:失败
msg_id | 调用发送函数时传入，用于标记消息

## 接口定义
### 1. 设备初始化
定义

```
we_int32 dlinkmq_init_device_info(dlinkmq_device_info *info)
```
说明
 
 设备初始化，创建TCP服务与云端建立连接。

参数

字段|说明
---|---
info | 设备信息

返回

返回值|含义
---|---
WE_SUCCESS | 成功
WE_ERROR | 失败

### 2. 数据点初始化

定义

```
void dlinkmq_init_datapoint(on_receive_cc_datapoint fun_cb)
```
说明
 
 设置接收数据点的回调函数

参数

字段|说明
---|---
fun_cb | 回调函数


### 3. 发送消息

定义

```
we_int32 dlinkmq_send_datapoint(dlinkmq_datapoint datapoints[], we_unit32 datapoints_count, on_send_msg fun_cb, we_uint32 msg_id)
```
说明
 
 设备上报数据点

参数

字段|说明
---|---
datapoints[] | 数据点数组，多个datapoint消息打包到一条消息里面发送
datapoints_count | datapoint的个数
fun_cb | 发送是否成功的回调函数
msg_id | 用于标记该消息，会在回调函数（fun_cb）中返回

返回

返回值|含义
---|---
WE_SUCCESS | 成功
WE_ERROR | 失败

