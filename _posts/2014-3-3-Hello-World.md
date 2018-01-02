---
layout: post
title: You're up and running!
---

查看了大量关于beaglebone black的i2s dac输出方法，总结了一下不使用转接板，可以支持runeos,volumo等hifi系统的i2s dac且通用的使用方法。





无操作系统限制，只要是复合下三个步奏都适用





第一步,首先找到io口

beaglebone black 的i2s是通过NXP TDA19988到hdmi输出的，但是在p9引脚里包含了这些针的引出.根据外部DAC选择你要使用的IO。







P9_25 - A14/0 - mcasp0_ahclkx (oscillator) - GPIO3_21
P9_31 - A13/0 - mcaps0_aclkx (bit clock) - SPI1_SCLK
P9_29 - B13/0 - mcasp0_fsx (lr clock) - SPI1_D0
P9_30 - D12/0 - mcasp0_axr0 (data0) - SPI1_D1
不使用转接板就是没有时钟，或者使用hdmi的时钟.
SPI1_CSOP9_28               Bitstream

SPI1_D0P9_29               Left/Right clock

SPI1_SCLKP9_31               Bit clock


第二步,查看有没有名称为dsp的字符设备节点。


ls -ald /dev/dsp*

crw-rw---T 1 root audio 14, 3 Jan  1  2000 /dev/dsp

获得声卡名称命令

aplay -L



default:CARD=Black

TI BeagleBone Black,
Default Audio Device

 

 

第三步，使用播放器播放,

ffmpeg -i test.mp3  -f alsa "default:CARD=Black" -re -vol 20 

这里的-vol分为256级，0为静音。

 



I2S为三线总线，3个信号分别为：

① 串行时钟SCK，也叫位时钟（BCK）。即每发送1位数字音频数据，SCK上都有1个脉冲。SCK的频率=2×采样频率×采样位数。在数据传输过程中，I2S总线的发送器和接收器都可以作为系统的主机来提供系统的时钟频率。

② 帧时钟WS，即命令（声道）选择，用于切换左右声道的数据。WS的频率等于采样频率，由系统主机提供。WS为“1”表示传输的是左声道的数据，WS为“0”表示传输的是右声道的数据。

③ 串行数据信号SD，用于传输二进制补码表示的音频数据。


注:文章有引用的地方来自这些地方。

https://www.element14.com/community/community/designcenter/single-board-computers/next-gen_beaglebone//blog/2013/05/28/bbb--audio-notes

http://blog.sina.com.cn/s/blog_5372b4a00102wk34.html

http://blog.csdn.net/greston/article/details/8076584

http://bbb.ieero.com/
