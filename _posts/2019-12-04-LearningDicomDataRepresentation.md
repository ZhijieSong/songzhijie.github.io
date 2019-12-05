---
layout: post
title:  "DICOM 协议学习（3） Data Representation"
author: seven
categories: [ DICOM ]
image: https://www.timedoo.com/wp-content/uploads/2019/06/8-1.jpg
tags: [DICOM]
---

DICOM将临床中的几乎所有信息都标准化为DICOM中的属性，并将其组织成IOD(Information Object Defination).在接下来的内容中将主要讲解DICOM协议如何组织这些数据。
# 基本的IT知识
学过计算机基础知识的同学都知道，计算机中存储信息是通过二进制存储的，计算机存储的时候，又有字节（byte）和比特(bit)之分，一个比特表示二进制数字中的一位，而一个字节包含了八个比特。例如，十进制中的14 在二进制中表示为 0000 1110，在电脑中占用了一个字节（8bit）的存储空间。更为具体的数值存储只是，在这里就不细讲了，如果不会就需要去复习下计算机基础知识了。
# 文本 or 二进制
通常根据存储内容类型的不同，计算机会将数据分为二进制文件和文本文件两种类型，文本类型数据就像我们平时使用的.txt格式的文件，存储的都是一些文字信息，比较典型的场景包括姓名、日期、日志、ID等字符串信息的存储，优点是读取方便，不需要再次编解码。而二进制数据通常存储数字化的序列，比如图像像素数据、视频、音频数据等，相比文本类型，二进制数据会更加的节省存储空间，但相比文本数据，其编码、解码会比较耗时，通常会根据硬件的不同对性能有不同程度的影响。
在这里有一点值得注意，不同的操作系统对数据会有不同的存储方式，有的采用小端对齐（Windows），有的采用大端对齐(Mac Os),如果你现在在Windows下存储了一个数据为 0x 007F （127）, 此时相同的数据在Mac Os中就要表示成 0x7F00了。如果这个时候要在Windows和Mac Os之间传输数据，就会导致数据的错乱，为了避免这种情况，一般两个系统上的DICOM应用在传输数据之前就要先确定对方的数据存储方式，另外要注意的是，DICOM在默认情况下是以`小端对齐`的方式进行存储数据的。数据存储方式的不同这点不论是开发人员还是应用软件的使用人员都需要注意。
相比之下，文本类型的数据就不会有这么多的麻烦事了，文本类型数据都比较独立，不受操作系统的影响，为开发人员节省了很多脑细胞。对比二进制数据和文本类型数据，都有着其优势的地方，所以DICOM标准中将dicom文件的存储方式既包含文本数据，又包含二进制数据。如果你用记事本打开一个dicom文件，你可能看到如下图的场景：文件中既包含可读的文字信息，又包含一些乱糟糟的乱码，其实这些乱码就是存储的二进制数据，如果你想了解存储在这些乱码中的信息，只能通过专业的DICOM软件进行阅读了。
![image.png](https://upload-images.jianshu.io/upload_images/8924350-d85f0220b6a625d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# DICOM语法：Value Representations
如果你学习过编程，对数据类型这个词语你一定不陌生，而在DICOM中也有着自己的数据类型，专业一点的叫法叫做Value Representation. DICOM标准中总共定义了27种基本数据类型，几乎囊括了所有的临床数据类型，DICOM文件中所有的数据编码都要在这27种基本数据类型之内。另外，每一种数据类型都用两个大写英文字母来表示，具体的27种数据类型定义如下：
![image.png](https://upload-images.jianshu.io/upload_images/8924350-cbfe637c99e2c694.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png](https://upload-images.jianshu.io/upload_images/8924350-0216ebde916e5547.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png](https://upload-images.jianshu.io/upload_images/8924350-94bf42a26d7cb4df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png](https://upload-images.jianshu.io/upload_images/8924350-617affdc61d14cb3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

以上为DICOM中所有的数据类型，作为开发人员，需要在使用时重点关注数据长度，在上面的图中，每种数据类型后边都跟着其数据长度，DICOM通过两种方式来确定数据的长度，一种方式是单独的记录每种类型长度，类似于建立一个map表，映射类型与长度关系；另一种方式则是像前面表格中最后一列，一些基本数据类型其数据长度是固定的，只要根据该表就能确定数据长度是多少。
在前面表中的27中数据类型，大致可以分为以下几类：
 - Text VRs(文本类型): CS SH LO ST LT UT 
- 时间日期类型： DA TM DT AS 
- 文本类型数字：IS DS
- 二进制类型数字: SS US SL UL FL FD OB OW OF AT
- PN 存储患者姓名: PN
- AE 存储应用实体: AE
- UIDs 唯一标识：UID
- SQ： 序列数据集：SQ
- UN: 标识未知的值：UN
关于VR(Value Representations 类似于数据类型) 还有很多细节信息，在此就不具体的赘述了，需要的时候可以查看Digital Imaging and Communications in Medicine这本书和Dicom协议进行了解。

