---
layout: post
title:  "DICOM 协议学习笔记之 How Does DICOM Work ?"
author: seven
categories: [ DICOM ]
image: http://c2.haibao.cn/img/600_0_100_0/liGWf5HEcOORE/piccommon/1202/12023/liGWf5HEcOORE.jpg
tags: [DICOM]
---


DICOM有一套自己自己的模型体系，在进行具体介绍之前，首先讲解下DICOM模型：

在DICOM世界当中，所有的包括患者、检查、医学设备等都可以被视为具有不同特性（Property）或属性（Attribute）的对象。所有这些对象的属性或者特性的定义都已经在IOD（Infomation Object Definitions）中标准化了。我们可以将这些IOD视为attribute和description的一个集合，通常一个患者的IOD包含姓名、病历号（Medical Record Number）、性别、年龄、体重、吸烟史等，从另一方面来说，在DICOM当中，一个患者就是由2000多个标准的DICOM属性来描述或者组成的。
![image](https://upload-images.jianshu.io/upload_images/8924350-3cf3892c1ca39d1f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在影像设备采集到符合DICOM标准的图像以后，马上就会在DICOM设备和软件（Application Entities,AE）之间进行传输和处理。DICOM中可以将上述过程表示为一个模型：DICOM应用提供服务给每个AE。因为每个服务通常会包含一些数据交换，DICOM就将特定的服务类型与他们所处理的数据进行关联，并将其称之为associations Service-Object Pairs, SOPs。以下图为例，一个影像采集设备向归档服务器请求一个归档服务，归档服务器接收到请求后会响应请求并提供一个归档服务，其中，前者被称之为SCU(Service Class User，例子中为CT设备)，后者被称之为SCP(Service Class Provider，例子中为Archive设备)，每个SCU和SCP之间的 DICOM数据交换被称为Asscociation。因此，当两个应用要通过连接进行交换数据时，首先要进行DICOM握手建立Association。而在进行握手时双方所交换的信息则被称为Presentation Context（表示上下文），如果双方校验上下文没有问题，则可以进行对应的下一步处理。
![image.png](https://upload-images.jianshu.io/upload_images/8924350-49f4f633c2e93804.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

数以百计的DICOM设备生产厂商提供了各种各样的DICOM设备，每一个DICOM设备都会实现自己的一套DICOM一致性声明（Conformance Statement），这份声明会说明提供了哪些SOP，可以有哪些扩展。这些一致性生命是作为了解一个DICOM相关工程最好的路线图，它可以帮助你知道可以做什么，以及不能做什么。例如，当你买到一套数字归档设备后，它标明只支持CT Storage SCU，而不支持CT Storage SCP， 那么你就不能归档CT图像到它。所以如果有志要从事医学影像领域工作的，一定要了解DICOM协议。
