# 1 协议规定

使用bootloader来实现主程序的就地升级更新，bootloader与PC上位机通过串口来收发指令。

# 2 协议格式

## 2.1 发送格式

|              | HEADER | OPCODE |   LENGTH    |  DATA  |           CRC32           |
| :----------: | :----: | :----: | :---------: | :----: | :-----------------------: |
| **长度（Byte）** |   1    |   1    |      2      | Length |             4             |
|    **内容**    |  0xAA  |  参考下表  | 表示DATA段数据长度 |        | 从HEADER到DATA结束的所有内容CRC校验和 |

## 2.2 OPCODE

|      OPCODE       | VALUE |                PARAM                 |                         NOTE                          |
| :---------------: | :---: | :----------------------------------: | :---------------------------------------------------: |
|  **BL_OP_NONE**   | 0x00  |                  /                   |                       未知类型，异常处理                       |
| **BL_OP_INQUERY** | 0x01  |          VERSION:0<br>MTU:1          | 参考BOOT参数<br>VERSION：BOOT版本号，与上位机版本匹配<br>MTU：一次传输最大数据量 |
|  **BL_OP_BOOT**   | 0x11  |                  /                   |                         进入主芯片                         |
|  **BL_OP_RESET**  | 0x1F  |                  /                   |                         重启芯片                          |
|  **BL_OP_ERASE**  | 0x20  |         ADDR:4字节<br>SIZE:4字节         |                     擦除指定区域的指定大小内容                     |
|  **BL_OP_READ**   | 0x21  |         ADDR:4字节<br>SIZE:4字节         |                     读出指定地区指定大小的内容                     |
|  **BL_OP_WRITE**  | 0x22  |  ADDR：4字节<br>SIZE：4字节<br>DATA：待写入内容  |                    讲DATA写入地址的固定区间内                    |
| **BL_OP_VERIFY**  | 0x23  | ADDR：4字节<br>SIZE：4字节<br>CRC：校验和（4字节） |            校验FLASH中的内容，若与CRC匹配，返回BL_ERR_OK            |

## 2.3 响应格式


|              | HEADER | OPCODE |         LENGTH         | ERRCODE |           CRC32           |
| :----------: | :----: | :----: | :--------------------: | :-----: | :-----------------------: |
| **长度（BYTE）** |   1    |   1    |           2            |    1    |             4             |
|    **内容**    |  0xAA  |        | 表示ERRCODE段长度，响应中长度固定位1 |  参考下表   | 从HEADER到DATA结束的所有内容CRC校验和 |

## 2.4 ERRCODE

|     **ERRCODE**     | VALUE |   NOTE   |
| :-------------: | :---: | :------: |
|    **BL_ERR_OK**    |   0   |   操作成功   |
|  **BL_ERR_OPCODE**  |   1   | OPCODE出错 |
| **BL_ERR_OVERFLOW** |   2   |   数据溢出   |
| **BL_ERR_TIMEOUT**  |   3   |   数据超时   |
|  **BL_ERR_FORMAT**  |   4   |   格式出错   |
|  **BL_ERR_VERIFY**  |   5   |   校验出错   |
|  **BL_ERR_PARAM**   |   6   |   参数错误   |
|  **BL_ERR_UNKNOW**  | 0xFF  |   未知错误   |
