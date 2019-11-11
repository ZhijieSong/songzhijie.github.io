---
layout: post
title:  "DICOM 协议学习笔记(一) What is DICOM"
author: seven
categories: [ DICOM ]
image: https://gss2.bdstatic.com/-fo3dSag_xI4khGkpoWK1HF6hhy/baike/crop%3D0%2C126%2C745%2C492%3Bc0%3Dbaike92%2C5%2C5%2C92%2C30/sign=4c85803204f431ada89d19797606809e/2934349b033b5bb537d8455e3ed3d539b600bc07.jpg
tags: [DICOM]
---
#什么是DICOM?
Dicom （Digital Imaging and Communications in Medicine）即医学数字成像和通信，是医学图像和相关信息的国际标准（ISO 12052）。它定义了质量能满足临床需要的可用于数据交换的医学图像格式。它完全包含了医学影像领域中的数据传输、存储和显示，并包含了数字医学影象中的所有功能层面（这也是为什么很多人将DICOM作为标准的集合，而不是一个单一的标准）。毫无疑问，DICOM协议已经统治了数字医学领域。

与DICOM另一个相关的重要内容是PACS（Picture Archiving and Communication System），PACS作为一个医疗影像系统，其目的是用来提供运行数字医疗影像所需要的硬件和软件环境，其由影像采集设备、数字影响存档和工作站所组成，与我们日常生活中的摄影进行类比，通过手机拍照就是影像采集端(Modality),将照片转存到电脑上，相当于PACS中的归档(Archive)， 将图像转发给朋友，类似将图像发到工作站给医生浏览（Reviewers）. PACS 与DICOM之间关联紧密，为了保证互换性，PACS所有的功能设计都是DICOM驱动的。
![image](https://upload-images.jianshu.io/upload_images/8924350-1c06fc2c594fe4ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
DICOM提供了哪些东西：

- 一个数字医学的通用标准： 目前所有的数字影像设备都能够拍摄DICOM影响，并通过DICOM 网络进行传输。
- 优秀的图像质量： DICOM提供16bit级（65535）灰度图显示(JPEG,BMP图像均为256级灰度)，从而可以使医生从图像捕获到非常细微的变化
- 完全支持各种类型的图像采集参数和数据类型：除了存储图像像素数据之外，DICOM文件还记录了非常多的其他参数，例如患者3D体位、图像中物体的物理尺寸、断层厚度等等。
- 对于医学数据的完全编码(encoding) : DICOM文件和消息包含了2000多个标准化的属性来覆盖从患者姓名到图像颜色深度、到当前患者的诊断等各种各样的的医学数据，从而保证了医生可以从中获取准确的信息以进行正确无误的诊断。
- 澄清了影像设备的定义和设计目的。
