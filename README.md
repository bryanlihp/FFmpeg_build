# Build FFmpeg on Visual Studio 2013 - 2017

## Pre-requisites

Visual Studio 2013,2015 or 2017
MSYS2
YASM

### Visual Studio
Latest ffmpeg versions compile with Visual Studio 2013, 2015 and 2017. 

### MSYS2

1. Download and run the installer at http://msys2.github.io. Follow the instructions and install it. In may case, it is installed to C:\msys64
2. Install required tools: pacman -S make gcc diffutils
3. Rename C:\msys64\usr\bin\link.exe to link_orig.exe, in order to use MSVC link.exe (naming conflict)

### YASM

1. Download Win64.exe and Win32.exe at http://yasm.tortall.net/Download.html and move it to C:\YASM
2. You should have yasm-<version>-win32.exe and yasm-<version>-win64.exe in C:\YASM
3. Add C:\YASM to PATH environment variable

## Create working space
Create a folder structure as your working space. It is recommend not to build FFmpeg code in the souce folder. We can create the following folder structure to build FFmpeg. 

```
 FFmpegBuild
 |--FFmpegSrc (FFmpeg source code)
 |--libmp3lame
 |   |--lame
 |   |      lame.h  
 |   |--lib32
 |   |   |--debug
 |   |   |   mp3lame.lib
 |   |   |--release
 |   |       mp3lame.lib
 |   |--lib64
 |       |--debug
 |       |   mp3lame.lib
 |       |--release
 |           mp3lame.lib
 |--Build (Build folder)
 |--Stage (Build result)
```
Note that We are building libmp3lame into FFmpeg, so libmp3lame files are included. 

## Compile FFmpeg
### Launch msys2 shell from Visual Studio Code shell
1. Run VS prompt 
   x64 native tooles for x64 build  or x86 native tools for x86 build
2. launch msys chell
    * x64 build:  Run C:\msys64\msys2_shell.cmd -mingw64 -use-full-path  
    * x86 build:  Run C:\msys64\msys2_shell.cmd -mingw32 -use-full-path  
3. check tools exist and point to the right location
    * which cl  
    * which link
    * which yasm-<version>-win32 or yasm-<version>-win64.exe
4. Change path to your workspace
   * ```cd /FFmpegBuild/Build```
5. Config
   * x86 release build: (link with libcmt.lib)
   ```
   ../FFmpegSrc/configure --prefix=../stage/win32/release \
                             --incdir=../stage/include \
                             --toolchain=msvc \
                             --arch=x86 \
                             --enable-yasm \
                             --yasmexe=yasm-1.3.0-win32 \
                             --enable-asm \
                             --disable-debug \
                             --enable-static \
                             --enable-libmp3lame \
                             --extra-cflags='-MT -I"../libmp3lame"' \
                             --extra-ldflags='-LIBPATH:"../libmp3lame/lib32/release"'
   ```
   * x86 debug build: (link with msvcrtd.lib)
   ```
   ../FFmpegSrc/configure --prefix=../stage/win32/debug \
                             --incdir=../stage/include \
                             --toolchain=msvc \
                             --arch=x86 \
                             --enable-yasm \
                             --yasmexe=yasm-1.3.0-win32 \
                             --enable-asm \
                             --enable-static \
                             --enable-libmp3lame \
                             --extra-cflags='-MDd -I"../libmp3lame"' \
                             --extra-ldflags='-LIBPATH:"../libmp3lame/lib32/debug"'
   ```
   * x64 release build: (link with libcmt.lib)
   ```
   ../FFmpegSrc/configure --prefix=../stage/win64/release \
                             --incdir=../stage/include \
                             --toolchain=msvc \
                             --arch=amd64 \
                             --enable-yasm \
                             --yasmexe=yasm-1.3.0-win64 \
                             --enable-asm \
                             --disable-debug \
                             --enable-static \
                             --enable-libmp3lame \
                             --extra-cflags='-MT -I"../libmp3lame"' \
                             --extra-ldflags='-LIBPATH:"../libmp3lame/lib64/release"'
   ```
   * x64 debug build: (link with msvcrtd.lib)   
   ```
   ../FFmpegSrc/configure --prefix=../stage/win64/debug \
                             --incdir=../stage/include \
                             --toolchain=msvc \
                             --arch=amd64 \
                             --enable-yasm \
                             --yasmexe=yasm-1.3.0-win64 \
                             --enable-asm \
                             --enable-static \
                             --enable-libmp3lame \
                             --extra-cflags='-MDd -I"../libmp3lame"' \
                             --extra-ldflags='-LIBPATH:"../libmp3lame/lib64/debug"'
   ```
6. make
    * make 
    * make install

### VS2015
 * -DWIN32_LEAN_AND_MEAN is required to be added to extra-cflags
    
### Compile Lame
1. link libmpeghip_static with libmp3lame_static
2. Compile Lame
3. Copy lame.h to the workspace
4. rename libm3lame_static.lib to mp3lame.lib and copy to workspace
