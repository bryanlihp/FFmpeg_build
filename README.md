# Build FFmpeg on Visual Studio 2022

## Pre-requisites

Visual Studio 2022
MSYS2
NASM (installed via msys2 package manager)

### Visual Studio
ffmpeg v8.0 compile with Visual Studio 2022

### MSYS2

1. Download and run the installer at http://msys2.github.io. Follow the instructions and install it. In may case, it is installed to C:\msys64
2. Launch the MSYS2 terminal and update the package database and base packages by running: `pacman -Syu`
3. Install required tools: pacman -S nasm diffutils pkg-config make

## Create working space
Create a folder structure as your working space. It is recommend not to build FFmpeg code in the souce folder. We can create the following folder structure to build FFmpeg. 

```
 FFmpegBuild
 |--FFmpegSrc (FFmpeg source code)
 |--libmp3lame
 |   |--include
 |   |     |--lame
 |   |         lame.def  
 |   |         lame.h  
 |   |--lib
 |   |   |
 |   |   |--Win32
 |   |   |   |--Debug_MDd
 |   |   |   |   mp3lame.lib
 |   |   |   |--Debug_MTd
 |   |   |   |   mp3lame.lib
 |   |   |   |--ReleaseMT
 |   |   |   |   mp3lame.lib
 |   |   |   |--ReleaseMD
 |   |   |   |   mp3lame.lib
 |   |   |--x64
 |   |   |   |--Debug_MDd
 |   |   |   |   mp3lame.lib
 |   |   |   |--Debug_MTd
 |   |   |   |   mp3lame.lib
 |   |   |   |--ReleaseMT
 |   |   |   |   mp3lame.lib
 |   |   |   |--ReleaseMD
 |   |   |   |   mp3lame.lib
 |--Build (Build folder)
 |--Stage (Stage result)
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
   * x86 release build: (link with libcmt.lib, MT)
   ```
   ../FFmpegSrc/configure --prefix=../stage/win32/release \
                          --incdir=../stage/include \
                          --toolchain=msvc \
                          --arch=x86 \
                          --enable-x86asm \
                          --enable-asm \
                          --disable-debug \
                          --enable-static \
                          --enable-libmp3lame \
                          --extra-cflags='-MT \
                          -I"../libmp3lame/include" -DWIN32_LEAN_AND_MEAN' \
                          --extra-ldflags='-LIBPATH:"../libmp3lame/lib/Win32/Release_MT"'
    ```
  * x86 debug build: (link with msvcrtd.lib MTd)
   ```
   ../FFmpegSrc/configure --prefix=../stage/win32/DebugMTd \
                          --incdir=../stage/include \
                          --toolchain=msvc \
                          --arch=x86 \
                          --enable-x86asm \
                          --enable-asm \
                          --enable-static \
                          --enable-libmp3lame \
                          --extra-cflags='-MTd -I"../libmp3lame/include"' \
                          --extra-ldflags='-LIBPATH:"../libmp3lame/lib/Win32/Debug_MTd"'
   ```
   * x64 release build: (MD)
   ```
   ../FFmpegSrc/configure --prefix=../stage/win32/ReleaseMD \
                          --incdir=../stage/include \
                          --toolchain=msvc \
                          --arch=x86 \
                          --enable-x86asm \
                          --enable-asm \
                          --enable-static \
                          --enable-libmp3lame \
                          --extra-cflags='-MD -I"../libmp3lame/include"' \
                          --extra-ldflags='-LIBPATH:"../libmp3lame/lib/Win32/Release_MD"'
   ```
   * x64 debug build: (MDd)   
   ```
   ../FFmpegSrc/configure --prefix=../stage/win32/DebugMDd \
                          --incdir=../stage/include \
                          --toolchain=msvc --arch=x86 \
                          --enable-x86asm \
                          --enable-asm \
                          --enable-static \
                          --enable-libmp3lame \
                          --extra-cflags='-MDd -I"../libmp3lame/include"' \
                          --extra-ldflags='-LIBPATH:"../libmp3lame/lib/Win32/Debug_MDd"'
    ```
6. make
    * make 
    * make install

### VS2015 vs2017 
 * -DWIN32_LEAN_AND_MEAN is required to be added to extra-cflags
    
### Compile Lame
1. link libmpeghip_static with libmp3lame_static
2. Compile Lame
 
3. Copy lame.h to the workspace
4. rename libm3lame_static.lib to mp3lame.lib and copy to workspace
