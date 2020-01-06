---
layout: post
title: "DICOM 协议学习笔记（4） 数据编码"
author: seven
categories: [ DICOM ]
image: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1578296616120&di=d0bc841ade0feeb2f8ddb50c6b0e2b71&imgtype=0&src=http%3A%2F%2F5b0988e595225.cdn.sohucs.com%2Fimages%2F20171230%2F2367316b7b544a0daa36be56a59619d1.jpeg
tags: [DICOM]
---


在上一节当中，我们了解了DICOM中的数据字典，根据这个字典，我们就能够构建一个dicom对象了。比如，如果有一个对象的信息如下
```
“Patient Jonh Smith, Male, born on August 6, 1954”
```
那么在dicom中表示就需要把属性名替换成tag号，替换后如下：
```
(0010,0010)Smith^Jonh(0010,0030)19540806(0010,0040)M
```
这就构成了一个Dicom对象，Dicom 对象是Dicom中最为重要的一个概念。所有的Dicom数据（如医疗图像、指令、报告等）都被包裹成DICOM对象。通过这种格式，dicom数据在设备、网络和存储设备之间传输并存储。
通过前面的了解，我们知道Dicom将现实世界中的信息抽象成dicom中的属性信息，比如一个患者包括性名、性别、年龄等属性，而实际中dicom所包含和展示的信息越来越复杂，那dicom如何来解决呢？
其实很简单，Dicom通过SQ（Sequence） VR来维护一系列的属性集，每一个属性都是一个Dicom对象，而每一个属性对象又有可能包含其他的SQ VR, 这样就可以形成类似于下面图片中的树状结构，使得dicom中即使包含了很多属性，但是层次分明，管理起来也方便很多。
![image.png](https://upload-images.jianshu.io/upload_images/8924350-9d4b11fa9669d79b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# Encoding Data Elements
所谓的编码，就是按照一定的格式来组织数据。在DICOM标准的第3.5节中定义了两种最为常用的编码方式：隐式VR编码和显式(explict)VR编码。隐式VR编码是最简单的也是DICOM中默认的编码方式。例如，拿我们熟悉的患者Joe Smith来说，患者姓名Patient Name的group name为0x0010, element number为0x0010, "Smith^Joe"字符串长度为9，但由于Dicom需要保证数据长度为偶数，所以需要给性名后边补空格即"Smith^Joe ",调整后的性名占用字符串长度为L = 10 = 0x0A，然后按照下面表1对患者性名进行编码。这里要注意，系统默认数据采用小端对齐，意味着多个字节数字是从低位开始的，所以对于Group=0010,右侧低位10先出现，而高位00后出现，如你所看到的，18个字节的二进制数据表示了Patient Name这个属性。

**表1** 隐式编码
<table>
	<tr>
	    <th colspan="2">Tag</th>
	    <th>Vaule Length</th>
	    <th>Value</th>  
	</tr>
	<tr>
	    <td >Group Number(2-byte unsigned integer)</td>
	    <td>Element Number(2-byte unsigned)</td>
        <td>4-byte integer L</td>
	    <td>Even number L of bytes containing the data element value</td>
	</tr>
	<tr>
	    <td>2 bytes</td>
        <td>2 bytes</td>
        <td>4 bytes</td>
	    <td>L bytes</td>
	</tr>
</table>

**表2** implict编码示例
<table>
	<tr>
	    <th >Byte#</th>
	    <th>1</th>
	    <th>2</th>  
        <th>3</th>  
        <th>4</th>  
        <th>5</th>  
        <th>6</th>  
        <th>7</th>  
        <th>8</th>  
        <th>9</th>  
        <th>10</th>
	    <th>11</th>  
        <th>12</th>  
        <th>13</th>  
        <th>14</th>  
        <th>15</th>  
        <th>16</th>  
        <th>17</th>  
        <th>18</th>  
	</tr>
	<tr>
	    <td >Decimal</td>
	    <td >16</td>
        <td >0</td>
        <td >16</td>
        <td >0</td>
        <td >10</td>
        <td >0</td>
        <td >0</td>
        <td >0</td>
        <td >S</td>
        <td >m</td>
        <td >i</td>
        <td >t</td>
        <td >h</td>
        <td >^</td>
        <td >J</td>
        <td >o</td>
        <td >e</td>
        <td >(space)</td>
	</tr>
	<tr>
	    <td>Binary</td>
        <td>10</td>
        <td>00</td>
	    <td>10</td>
        <td>00</td>
        <td>0A</td>
        <td>00</td>
        <td>00</td>
        <td>00</td>
        <td>53</td>
        <td>6D</td>
        <td>69</td>
        <td>74</td>
        <td>68</td>
        <td>5E</td>
        <td>4A</td>
        <td>6F</td>
        <td>65</td>
        <td>20</td>
	</tr>
</table>

显示编码和隐式编码其实差不多，对于除OB, OW,OF,SQ,UT 和 UN外的所有的VR类型，可以按照下面表3进行编码，其实就是把表1中的VR类型所占的空间拆分开了，一部分用来表示VR类型，另外两个字节用来表示数据的长度，通过显式编码后的数据类型如表4所示。

**表3** 显式VR编码（OB, OW,OF,SQ,UT 和 UN）
<table>
	<tr>
	    <th colspan="2">Tag</th>
	    <th>VR</th>
	    <th>Value Length</th>  
        <th>Value </th> 
	</tr>
	<tr>
	    <td >Group Number(2-byte unsigned integer)</td>
	    <td>Element Number (2-byte unsigned integer)</td>
        <td>VR (two characters)</td>
	    <td>2-byte integer L</td>
        <td>Even number L of bytes containing the data element value</td>
	</tr>
	<tr>
	    <td>2 bytes</td>
        <td>2 bytes</td>
        <td>2 bytes</td>
        <td>2 bytes</td>
	    <td>L bytes</td>
	</tr>
</table>

**表4** 显式编码示例
<table>
	<tr>
	    <th >Byte#</th>
	    <th>1</th>
	    <th>2</th>  
        <th>3</th>  
        <th>4</th>  
        <th>5</th>  
        <th>6</th>  
        <th>7</th>  
        <th>8</th>  
        <th>9</th>  
        <th>10</th>
	    <th>11</th>  
        <th>12</th>  
        <th>13</th>  
        <th>14</th>  
        <th>15</th>  
        <th>16</th>  
        <th>17</th>  
        <th>18</th>  
	</tr>
	<tr>
	    <td >Decimal</td>
	    <td >16</td>
        <td >0</td>
        <td >16</td>
        <td >0</td>
        <td >‘P’</td>
        <td >‘N’</td>
        <td >10</td>
        <td >0</td>
        <td >S</td>
        <td >m</td>
        <td >i</td>
        <td >t</td>
        <td >h</td>
        <td >^</td>
        <td >J</td>
        <td >o</td>
        <td >e</td>
        <td >(space)</td>
	</tr>
	<tr>
	    <td>Binary</td>
        <td>10</td>
        <td>00</td>
	    <td>10</td>
        <td>00</td>
        <td>50</td>
        <td>4E</td>
        <td>0A</td>
        <td>00</td>
        <td>53</td>
        <td>6D</td>
        <td>69</td>
        <td>74</td>
        <td>68</td>
        <td>5E</td>
        <td>4A</td>
        <td>6F</td>
        <td>65</td>
        <td>20</td>
	</tr>
    <tr>
        <td></td>
        <td colspan="2">g=0010</td>
        <td colspan="2">e=0010</td>
        <td colspan="2">VR Type</td>
        <td colspan="2">VR length L=10=0x000A</td>
        <td colspan="10">VR value=Smith^Joe(with trailing space)</td>
    </tr>
</table>

对于OB,OW,OF,SQ,UT和UN这几种类型，比较不一样，7、8位作为保留位，置为0000，而紧跟其后的9~12位用来表示数据长度，以下表为例
**表5** 显式编码OB,OW,OF,SQ,UT,UN
<table>
	<tr>
	    <th colspan="2">Tag</th>
	    <th colspan="2">VR</th>
	    <th>Value Length</th>  
        <th>Value </th> 
	</tr>
	<tr>
	    <td >Group Number(2-byte unsigned integer)</td>
	    <td>Element Number (2-byte unsigned integer)</td>
        <td>VR (two characters) of OB, OW, OF, SQ, UT, or UN</td>
	    <td>Reserved(2bytes) set to a value of 0000</td>
        <td>4-byte integer L</td>
        <td> Even number L of bytes containing the data element value</td>
	</tr>
	<tr>
	    <td>2 bytes</td>
        <td>2 bytes</td>
        <td>2 bytes</td>
        <td>2 bytes</td>
	    <td>4 bytes</td>
        <td>L bytes</td>
	</tr>
</table>
**表6** 显式编码OB,OW,OF,SQ,UT,UN编码示例
<table>
	<tr>
	    <th >Byte#</th>
	    <th>1</th>
	    <th>2</th>  
        <th>3</th>  
        <th>4</th>  
        <th>5</th>  
        <th>6</th>  
        <th>7</th>  
        <th>8</th>  
        <th>9</th>  
        <th>10</th>
	    <th>11</th>  
        <th>12</th>  
        <th>13</th>  
        <th>14</th>  
        <th>15</th>  
        <th>...</th>  
        <th>65547</th>  
        <th>65548</th>  
	</tr>
	<tr>
	    <td >Decimal</td>
	    <td >224</td>
        <td >127</td>
        <td >16</td>
        <td >0</td>
        <td >‘O’</td>
        <td >‘B’</td>
        <td >0</td>
        <td >0</td>
        <td >0</td>
        <td >0</td>
        <td >1</td>
        <td >0</td>
        <td >0</td>
        <td >3</td>
        <td >0</td>
        <td >...</td>
        <td >10</td>
        <td >10</td>
	</tr>
	<tr>
	    <td>Binary</td>
        <td>E0</td>
        <td>7F</td>
	    <td>10</td>
        <td>00</td>
        <td>4F</td>
        <td>42</td>
        <td>00</td>
        <td>00</td>
        <td>00</td>
        <td>00</td>
        <td>01</td>
        <td>00</td>
        <td>00</td>
        <td>03</td>
        <td>00</td>
        <td>...</td>
        <td>0A</td>
        <td>0A</td>
	</tr>
    <tr>
        <td></td>
        <td colspan="2">g=0010</td>
        <td colspan="2">e=0010</td>
        <td colspan="2">VR Type</td>
        <td colspan="2">Reserved</td>
        <td colspan="4">VR length L=0x00010000</td>
        <td colspan="6">VR value(pixel, 1 byte per pixel, L bytes)</td>
    </tr>
</table>

# Data Groups
# 属性及Group长度
# 编码DICOM 数据对象
# 必填和选填属性
# 存储图像数据
# 唯一标识
