# Build FFmpeg on Visual Studio 2013 - 2017

## Pre-requisites

* Visual Studio 2013, 2015 or 2017
* MSYS2
* YASM

### Visual Studio
Latest FFmpeg versions compile with Visual Studio 2013, 2015 and 2017.

### MSYS2

1. Download and run the installer at https://msys2.github.io. Follow the instructions and install it. By default it's installed to `C:\msys64`.
2. Install required tools: `pacman -S make gcc diffutils`
3. Rename C:\msys64\usr\bin\link.exe to link_orig.exe, in order to use MSVC link.exe *(naming conflict)*

### YASM

1. Download Win64.exe and Win32.exe at https://yasm.tortall.net/Download.html and move it to `C:\YASM`
2. You should have yasm-<version>-win32.exe and yasm-<version>-win64.exe in `C:\YASM`
3. Add `C:\YASM` to PATH environment variable

## Create working space
Create a folder structure as your working space. It is recommended not to build FFmpeg code in the souce folder. Create the following folder structure to build FFmpeg:

```
 FFmpegBuild
 |--FFmpegSrc (FFmpeg source code)
 |--libmp3lame
 |   |--include
 |   |     |--lame
 |   |         lame.def  
 |   |         lame.h  
 |   |--Win32
 |   |   |--Debug_MDd
 |   |   |   mp3lame.lib
 |   |   |--Debug_MTd
 |   |   |   mp3lame.lib
 |   |   |--ReleaseMT
 |   |   |   mp3lame.lib
 |   |   |--ReleaseMD
 |   |   |   mp3lame.lib
 |   |--x64
 |   |   |--Debug_MDd
 |   |   |   mp3lame.lib
 |   |   |--Debug_MTd
 |   |   |   mp3lame.lib
 |   |   |--ReleaseMT
 |   |   |   mp3lame.lib
 |   |   |--ReleaseMD
 |   |   |   mp3lame.lib
 |--Build (Build folder)
 |--Stage (Stage result)
```
Note that we are building libmp3lame into FFmpeg, so libmp3lame files are included.

## Compile FFmpeg
### Launch MSYS2 shell from Visual Studio Code shell
1. Run VS Prompt 
   x64 Native Tools for x64 build, or x86 Native Tools for x86 build.
2. Launch MSYS shell
    * x64 build: `C:\msys64\msys2_shell.cmd -mingw64 -use-full-path` 
    * x86 build: `C:\msys64\msys2_shell.cmd -mingw32 -use-full-path`
3. Check tools exist and point to the right location.
    * `which cl`
    * `which link`
    * `which yasm-<version>-win32` or `yasm-<version>-win64.exe`
4. Change path to your workspace
   * ```cd FFmpegBuild/Build```
5. Config
   * x86 Release build: (link with libcmt.lib, MT)
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
                          --extra-cflags='-MT -I"../libmp3lame/include" -DWIN32_LEAN_AND_MEAN' \
                          --extra-ldflags='-LIBPATH:"../libmp3lame/lib/Win32/Release_MT"'
   ```
  * x86 Debug build: (link with msvcrtd.lib MTd)
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
                          --extra-cflags='-MTd -I"../libmp3lame/include" -DWIN32_LEAN_AND_MEAN' \
                          --extra-ldflags='-LIBPATH:"../libmp3lame/lib/Win32/Debug_MTd"'
   ```
   * x64 Release build: (MD)
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
                          --extra-cflags='-MD -I"../libmp3lame/include" -DWIN32_LEAN_AND_MEAN' \
                          --extra-ldflags='-LIBPATH:"../libmp3lame/lib/Win32/Release_MD"'
   ```
   * x64 Debug build: (MDd)
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
                          --extra-cflags='-MDd -I"../libmp3lame/include" -DWIN32_LEAN_AND_MEAN' \
                          --extra-ldflags='-LIBPATH:"../libmp3lame/lib/Win32/Debug_MDd"'
   ```
6. make
    * `make`
    * `make install`

### VS2015, VS2017
 * -DWIN32_LEAN_AND_MEAN is required to be added to extra-cflags
    
### Compile Lame
1. Link libmpeghip_static with libmp3lame_static
2. Compile Lame
3. Copy lame.h to the workspace
4. Rename libm3lame_static.lib to mp3lame.lib and copy to workspace
