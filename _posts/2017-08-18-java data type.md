---
layout: post
title: "JAVA基本数据类型"
date: 2017-07-19
categories: Java
tags: Java
---



 * 基本数据类型
    * 布尔值 (true / false)
    * 数值类型
      * 定点类型
          * 字符 char
          * 字节 byte
          * 短整数 short
          * 整数 int
          * 长整数 long
      * 浮点类型
          * 单精度浮点数
          * 双精度浮点数
    * 引用数据类型
        * 类或枚举或接口
        * 数组


|类型|占用位数|数值范围|初始值|标准|
|:----    |:---|:----- |:----- |:----- |
| boolean	| 8 (1字节)	| 只有true和false| false |  |
| char | 16 (2字节) | 从'u0000'到'uFFFF'，即0到65535 | 'u0000' | ISO Unicode字符集 |
| byte | 8 (1字节) | 从-128到+127，即-2^7 - 2^7-1 | (byte)0 | |
| short | 16(2字节) | -2^16 - 2^16-1 | (short)0 | |
| int | 32(4字节) | -2^31 - 2^31-1 | 0 | |
| long | 64(8字节) | -2^63 - 2^63-1 | 0L | |
| float | 32(4字节) | 范围不知道怎么算 | 0.0f | IEEE 754标准 |
| double | 64(8字节) | 范围不知道怎么算 | 0.0d | IEEE 754标准 |