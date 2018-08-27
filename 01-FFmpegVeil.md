#### 读完此篇需掌握的内容
- [x] **FFmpeg是什么**
- [x] **FFmpeg做什么用的，可移植性如何**
- [x] **FFmpeg怎么用**
#### 部分应用
- [x] 提取视频文件中的音频
- [x] 视频文件中的音频静音，只保留视频
- [x] 裁剪音频或视频
- [x] 为视频添加水印（文字水印、图片水印、包括视频中的广告gif等）
- [x] 将MP4格式的视频转化为gif图


### 故事开篇
![1534408855492.jpg](https://github.com/DuanZhang/TheGreatWallofFFmpeg/blob/master/01-Resources/6696C36DF89463619B598B065C4A9417.jpg)
#### 总结：香菇头还是很聪明的，那么要如何提取音频，这是一个问题，有待探索。

偶然机会旧书摊收了一本如来神掌，研读几日初见成效，以此记录，做回顾用。

#### 作为一个务实者，我们先来解决以上问题，如果你还没有听说过ffmpeg，不急，先来感受一波他的强大，建议下载一段视频实际操作

视频文件1[wyt.mp4](https://github.com/DuanZhang/TheGreatWallofFFmpeg/blob/master/01-Resources/8A128C530AAC5CF9EE9FB585BC416BCB.mp4)
视频文件2[xl.mp4](https://github.com/DuanZhang/TheGreatWallofFFmpeg/blob/master/01-Resources/154D67B1CC7170906F2919BF6AB77EE0.mp4)
水印文件[1999.png](https://github.com/DuanZhang/TheGreatWallofFFmpeg/blob/master/01-Resources/17B8658E4770F8A9425D18682B0D0BFA.png)
背景乐[qingqiu.mp4](https://github.com/DuanZhang/TheGreatWallofFFmpeg/blob/master/01-Resources/8AAD968A279BB27093E30F895D2B0858.mp4)

### 提取视频文件中的音频文件
```
ffmpeg -i wyt.mp4 -vn -acodec copy wytoutput.m4a
```
- -i filename：指定输入的文件名。
- -vn：取消视频的输出。
- -acodec codec：指定音频编码（'copy'代表不做音频转码，直接复制）。
**提取的音频文件如下**[wytoutput.mp4](https://github.com/DuanZhang/TheGreatWallofFFmpeg/blob/master/01-Resources/EFAB8CA36237AAB0470381D3CFFBA168.mp4)


### 视频文件中的音频静音，只保留视频
```
ffmpeg -i wyt.mp4 -an -vcodec copy wytoutput1.mp4
```
- -an：取消音频轨。
- vcodec codec：强制使用codec编解码方式（'copy'代表不进行重新编码）。
**视频文件如下**[wytoutput1.mp4](https://github.com/DuanZhang/TheGreatWallofFFmpeg/blob/master/01-Resources/EA9F859300A82FE4FCB8C700AE072700.mp4)



### 裁剪音频或视频
```
ffmpeg -i wytoutput.mp4 -ss 00:00:08.0 -codec copy -t 7 wytoutput2.mp4
```
- ss time_off：从指定的时间（以秒为单位）开始，也支持[-]hh:mm:ss[.xxx]的格式。
- -t duration：指定时常。
**从第8秒开始裁剪到结束结果如下**[wytoutput2.mp4](https://github.com/DuanZhang/TheGreatWallofFFmpeg/blob/master/01-Resources/BF9C3B817115E1EBDD770D51AB39F719.mp4)


### 为视频添加水印
#### 图片水印
``` ffmpeg -i xl.mp4 -i 1999.png -filter_complex 'overlay=10:main_h-overlay_h-10' xl1.mp4
```
- main_h 代表主视频的高度；main_w 代表主视频的宽度；overlay_
w 代表水印宽度；overlay_h 代表水印的宽度
**添加水印如下**[xl1.mp4](https://github.com/DuanZhang/TheGreatWallofFFmpeg/blob/master/01-Resources/A10E258462F57E35C722F4704110AFB1.mp4)


### 为视频添加背景乐
```
ffmpeg -y -i xl.mp4 -i qingqiu.mp4 -filter_complex "[0:a] pan=stereo|c0=1*c0|c1=1*c1 [a1], [1:a] pan=stereo|c0=1*c0|c1=1*c1 [a2],[a1][a2]amix=duration=first,pan=stereo|c0<c0+c1|c1<c2+c3,pan=mono|c0=c0+c1[a]" -map "[a]" -map 0:v -c:v libx264 -c:a aac -strict -2 -ac 2 xl2.mp4
```
- y：覆盖已有文件
- map：指定输出文件的流映射关系。例如：“-map 1:0 -map 1:1”要求将第二个输入文件的第一个流和第二个流写入输出文件。如果没有-map选项，则ffmpeg采用默认的映射关系。
- ac channels：设置声道数，1为单声道，2为立体声。
**添加背景音乐如下**[xl2.mp4](https://github.com/DuanZhang/TheGreatWallofFFmpeg/blob/master/01-Resources/C719406247BE4E107ACCC00558E3D145.mp4)


### 将MP4格式的视频转化为gif图
```
ffmpeg -i xl.mp4 -vf scale=100:-1 -t 20 -r 10 xl.gif
```
- -r rate：帧速率（fps）
- -vf：视频过滤器
上述命令指按比例不变宽度修改为100，频率为10，将前20秒的的视频生成gif
**转换gif如下**[xl.gif](https://github.com/DuanZhang/TheGreatWallofFFmpeg/blob/master/01-Resources/931817991284DB8BFFCC41E197321BF7.gif)

至此常用的一些功能都介绍完了，那么问题来了ffmpeg究竟是什么，如何安装等，作为一篇非专业且偏应用的文章，就以PC端安装为例吧。

### FFmpeg是什么？
>FFmpeg是一套可以用来记录、处理数字音频、视频，并能将其转化为流的开源框架。采用LGPL或GPL许可证。它提供了录制、转换以及流化音视频的完整解决方案。FFmpeg在Linux平台下开发，但它同样也可以在其它操作系统环境中编译运行，包括Windows、Mac OS X等、移动端设备（Android、iOS等移动设备)。项目的名称来自MPEG视频编码标准，前面的"FF"代表"Fast Forward"。

### FFmpeg安装
#### 方法1
- 安装Homebrew（如果你以前没有使用过，或者还不知道它是什么，目测你的损失很大，不过也还不晚，搞起来）
```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
- 直接使用命令行输入
```
brew install ffmpeg
```

#### 方法2
- 根据自己需求去[官网](https://www.ffmpeg.org/)下载稳定版本源码，下载以后解压到目录中
- 运行configure脚本

**注意安装过程中如果出现以下问题**
![屏幕快照 2018-08-13 下午3.43.29.png](https://github.com/DuanZhang/TheGreatWallofFFmpeg/blob/master/01-Resources/7DD686B2B535773FE3D4E73210D233BC.png)
请先升级nasm/yasm
各种方法都可以，推荐使用Homebrew，然后在终端中执行brew install yasm
![屏幕快照 2018-08-13 下午4.19.18.png](https://github.com/DuanZhang/TheGreatWallofFFmpeg/blob/master/01-Resources/F992560C1D8251F105E75D4C9F9AB5D5.png =892x833)
再次运行configure脚本
![屏幕快照 2018-08-13 下午4.21.10.png](https://github.com/DuanZhang/TheGreatWallofFFmpeg/blob/master/01-Resources/A3E04A256B6F70FD4A52E1E14C5F0FDA.png =565x344)
- 使用./configure --help查看提供的功能

安装还是很容易的，有任何问题请google或百度，就我自己（macOS High Sierra 10.13.5）安装过程很顺利的，至此，水文就先到这里，关于更深一些的框架结构，源码解读，还在修炼中，请打开你的终端，开始你的表演。

参考：
![官网](https://www.ffmpeg.org/)
![音视频开发进阶指南](http://item.jd.com/12292642.html)

