---
title: java基础-字符串
date: 2018-09-07 14:28:11
tags:
- java基础
- 字符串
categories:
- java基础

---

### java基础之字符串

##### String、StringBuffer 和StringBuilder三者的区别

+ String是字符串常量
+ StringBuffer 字符串变量（线程安全）
+ StringBuilder 字符串变量（非线程安全）

String是用final关键字修饰的类，不可以被继承，String变量也是不可变对象，因此每次对String对象操作时，生成新的String对象，二将引用指向新的对象。

+ 在经常需要改变的字符串尽量不要使用String，而是使用StringBuffer或者StringBuilder。
+ StringBuilder是非线程安全的，他的速度要比StringBuffer快，如果不存线程安全问题，优先使用StringBuilder。