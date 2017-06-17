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

2. Rename yasm-<version>-winXX.exe to yasm.exe, XX is 32 or 64, depending on

### Working space
Create a folder as you working space. It is recommend not to build FFmpeg code in the souce folder. So we can create the following folder structure. We are building libmp3lame into FFmpeg, so libmp3lame files included.
 >
 |--FFmpegBuild
 |   |--FFmpegSrc (FFmpeg source code)
 |   |--libmp3lame
 |       |--lame
 |       |      lame.h  
 |       |--lib
 |           |--debug
 |           |   mp3lame.lib
 |           |--release
 |               mp3lame.lib
 |--Build (*working space*)
 |--Stage
  
## Ready to compile 

### Launch msys2 shell from Visual Studio Code shell
1. Run VS prompt 
   x64 native tooles for x64 build  or x86 native tools for x86 build
2. launch msys chell
    * x64 build:  Run C:\msys64\msys_shell.cmd -mingw64 -use-full-path  
    * x86 build:  Run C:\msys64\msys_shell.cmd -mingw32 -use-full-path  
3. check tools exist and point to the right location
    * which cl  
    * which link
    * which yasm-<version>-win32 or yasm-<version>-win64.exe
### Get FFmpeg source code
  Copy FFmpeg Assuming path to FFmpeg source code is ../FFmpeg, 

../FFmpeg/configure --prefix=stage --toolchain=msvc --arch=x86 (or amd64 for 64) --enable-yasm --enable-asm --disable-debug --enable-static --extra-cflags='-MT' !!!!--extra-cflags='-MT' is necessary as we want to use Libcmt.lib not msvcrt.lib

../FFmpeg/configure --prefix=stage --toolchain=msvc --arch=x86 --enable-yasm --enable-asm --enable-static --extra-cflags='-MDd'	!!!!!!!!--extra-cflags='-MDd' is necessary as we want to use not msvcrtd.lib, not msvcrt.lib


***With Lame
1. link libmpeghip_static with libmp3lame_static
2. Compile Lame
3. Copy lame.h to include/lame/lame.h
4. rename libm3lame_static.lic to mp3lame.lib
5. compile FFmpeg:

../FFmpeg/configure --prefix=stage --toolchain=msvc --arch=x86 --enable-yasm --enable-asm --disable-debug --enable-static --enable-libmp3lame --extra-cflags='-I"../lame-3.99.5/include"' --extra-ldflags='-LIBPATH:"../lame-3.99.5/output/Release"'
