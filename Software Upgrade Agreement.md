# 协议规定

使用bootloader来实现主程序的就地升级更新，bootloader与PC上位机通过串口来收发指令。

# 协议格式

## 发送格式

|    |HEADER|OPCODE|LENGTH|DATA|CRC32|
|:---:|:---:|:---:|:---:|:---:|:---:|
|长度（Byte）|1 |1 |2 |length |4 |
|内容  |0xAA |参考下表 |表示DATA段数据长度 | |从HEADER到DATA结束的所有内容CRC校验和|

## OPCODE

