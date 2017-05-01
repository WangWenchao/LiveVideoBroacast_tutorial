## install ffmpeg

### 环境

系统环境：CentOS release 6.7 (Final)

需求

编译安装ffmpeg

获取依赖

### 安装依赖包

```shell
yum install -y autoconf automake cmake freetype-devel gcc gcc-c++ git libtool make mercurial nasm pkgconfig zlib-devel

```
创建编译源码的目录

```shell
mkdir ~/ffmpeg_sources
```

编译&安装

Note:如果您不需要特定的编码器，可以跳过相关部分，然后删除编译ffmpeg 时相应的./configure选项。例如，如果不需要libvorbis ，可以跳过这一节，然后从安装ffmpeg的部分去掉–enable-libvorbis

#### Yasm

Ysam是X264和FFmpeg使用的汇编程序。
```shell
cd ~/ffmpeg_sources
git clone --depth 1 git://github.com/yasm/yasm.git
cd yasm
autoreconf -fiv
./configure --prefix="$HOME/ffmpeg_build" --bindir="$HOME/bin"
make
make install
make distclean
```

#### libx264

libx264 视频编码器。更多说明和用法示例可以参考：https://trac.ffmpeg.org/wiki/Encode/H.264 
需要ffmpeg编译的时候添加–enable-gpl –enable-libx264
```shell
cd ~/ffmpeg_sources
git clone --depth 1 git://git.videolan.org/x264
cd x264
PKG_CONFIG_PATH="$HOME/ffmpeg_build/lib/pkgconfig" ./configure --prefix="$HOME/ffmpeg_build" --bindir="$HOME/bin" --enable-static
make
make install
make distclean
```
#### libx265

H.265/HEVC 视频编码器。更多说明和用法示例可以参考：https://trac.ffmpeg.org/wiki/Encode/H.265

需要ffmpeg编译的时候添加–enable-gpl –enable-libx265
```shell
cd ~/ffmpeg_sources
hg clone https://bitbucket.org/multicoreware/x265
cd ~/ffmpeg_sources/x265/build/linux
cmake -G "Unix Makefiles" -DCMAKE_INSTALL_PREFIX="$HOME/ffmpeg_build" -DENABLE_SHARED:bool=off ../../source
make
make install
```
#### libfdk_aac

AAC 音频编码器。 
需要ffmpeg编译的时候添加–enable-libfdk-aac (以及 –enable-nonfree 如果你添加了 –enable-gpl的话)
```shell
cd ~/ffmpeg_sources
git clone --depth 1 git://git.code.sf.net/p/opencore-amr/fdk-aac
cd fdk-aac
autoreconf -fiv
./configure --prefix="$HOME/ffmpeg_build" --disable-shared
make
make install
make distclean
```
#### libmp3lame

MP3 音频编码器.

需要ffmpeg编译的时候添加 –enable-libmp3lame
```shell
cd ~/ffmpeg_sources
curl -L -O http://downloads.sourceforge.net/project/lame/lame/3.99/lame-3.99.5.tar.gz
tar xzvf lame-3.99.5.tar.gz
cd lame-3.99.5
./configure --prefix="$HOME/ffmpeg_build" --bindir="$HOME/bin" --disable-shared --enable-nasm
make
make install
make distclean
```
#### libopus

Opus 音频编解码器. 
需要ffmpeg编译的时候添加 –enable-libopus
```shell
cd ~/ffmpeg_sources
git clone https://git.xiph.org/opus.git
cd opus
autoreconf -fiv
./configure --prefix="$HOME/ffmpeg_build" --disable-shared
make
make install
make distclean
```
#### libogg

Ogg 比特流库.。libtheora and libvorbis需要
``` shell
cd ~/ffmpeg_sources
curl -O http://downloads.xiph.org/releases/ogg/libogg-1.3.2.tar.gz
tar xzvf libogg-1.3.2.tar.gz
cd libogg-1.3.2
./configure --prefix="$HOME/ffmpeg_build" --disable-shared
make
make install
make distclean
```
#### libvorbis

Vorbis 音频编码器. 需要 libogg

需要ffmpeg编译的时候添加 –enable-libvorbis
``` shell
cd ~/ffmpeg_sources
curl -O http://downloads.xiph.org/releases/vorbis/libvorbis-1.3.4.tar.gz
tar xzvf libvorbis-1.3.4.tar.gz
cd libvorbis-1.3.4
LDFLAGS="-L$HOME/ffmeg_build/lib" CPPFLAGS="-I$HOME/ffmpeg_build/include" ./configure --prefix="$HOME/ffmpeg_build" --with-ogg="$HOME/ffmpeg_build" --disable-shared
make
make install
make distclean
```
#### libvpx

VP8/VP9 视频编码器.

需要ffmpeg编译的时候添加 –enable-libvpx.
``` shell
cd ~/ffmpeg_sources
git clone https://github.com/webmproject/libvpx.git
cd libvpx
./configure --prefix="$HOME/ffmpeg_build" --disable-examples
make
make install
make clean
```
### FFmpeg

```shell
cd ~/ffmpeg_sources

git clone https://github.com/FFmpeg/FFmpeg.git

cd FFmpeg

export PKG_CONFIG_PATH="$HOME/ffmpeg_build/lib/pkgconfig"

./configure --prefix="$HOME/ffmpeg_build" --extra-cflags="-I$HOME/ffmpeg_build/include" --extra-ldflags="-L$HOME/ffmpeg_build/lib" --bindir="$HOME/bin" --pkg-config-flags="--static" --enable-gpl --enable-nonfree --enable-libfdk-aac --enable-libfreetype --enable-libmp3lame --enable-libopus --enable-libvorbis --enable-libvpx --enable-libx264 --enable-libx265

make

make install

make distclean

hash -r
```
至此，编译ffmpeg完成， ffmpeg (包括 ffprobe, ffserver, lame, 和 x264已经可以使用

Done ^^

参考： 

ffmpeg安装指南：https://trac.ffmpeg.org/wiki/CompilationGuide 

ffmpeg安装指南（centos）：https://trac.ffmpeg.org/wiki/CompilationGuide/Centos

ffmpeg Git地址：https://github.com/FFmpeg/FFmpeg

