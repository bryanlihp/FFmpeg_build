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
### Compile libmp3lame
1. Get the source code  
   lame-3.100.tar.gz from [SourceForge](https://sourceforge.net/projects/lame/files/lame/3.100/lame-3.100.tar.gz/download) 
3. Modify lame projects  
  Lame has Visual Studio 2008 (VC9) solution and project files in the package. This makes it easy to build lame for Windows platform. These files are located in vc_solution sub folder.  
  To compile using Visual studio 2022(VC143), upgrading is required.  
  The following 3 lame project files cannot be upgraded to vs2022:
   - vc9_libmp3lame_dll.vcproj
   - vc9_mpglib.vcproj 
   - vc9_libmp3lame.vcproj  
   
   Use text editor to modify these files, remove the "ToolFile" node and "Configuration" node with Name "ReleaseNASM|Win32".
5. Upgrade solution  
  Copy vc9_lame.sln to a new solution file vs2015_lame.sln and open it in visual studio 2019. Visual studio will prompt for One-way upgrade, click OK to upgrade the solution and all projects.
6. Config solution  
  Create 2 Debug configurations and 2 Release configurations:

    | Configuration|CopyFrom|Runtime Library|  
    | -- | -- | -- |  
    |DebugMDd|Debug|MDd|   
    |DebugMTd|Debug|MTd|  
    |ReleaseMD|Release|MD|  
    |ReleaseMT|Release|MT|
   
    Note: Create x64 platform is x64 builds are required
7. Update project reference    
   Update project "libmp3lame-static" 's project reference ("libmpghip-static"), make sure "Link Library Dependencies" is set to "True"
8. Update output target  
   The project "libmp3lame-static" is the project we must build. The result library file is the library file we need to build with ffmpeg. Rename the output and target name as:  
   - General  
    Output Directory: $(SolutionDir)stage\lib\$(Configuration)\
    TargetName: mp3lame 
   - Librarian  
    Outout File: $(OutDir)$(TargetName)$(TargetExt)
9. Build libmpghip-static project  
   The library files (mp3lame.lib) of different configurations are generated in the stage sub folder in vc_solutions folder:

    |Configuration|Link Option|File|
    |--|--|--|
    |DebugMDd|MDd|vc_solution\stage\lib\DebugMDd|
    |DebugMTd|MTd|vc_solution\stage\lib\DebugMTd|
    |ReleaseMD|MD|vc_solution\stage\lib\ReleaseMD|
    |ReleaseMT|MT|vc_solution\stage\lib\ReleaseMT|

### Build libopus
1. Get libopus source code 
   lib opus souce code (1.5.2 as of 2025 Nov. 1st) can be acquired at:
    - Git
      [Repository](https://github.com/xiph/opus) 
    - Opus Official site
      [Download page](https://opus-codec.org/downloads/)
2. Unpack to build folder  
   unpack the downloaded package to [OpusSrc] folder
3. Create Build Folder
   ```
   cd [OpusSrc]
   mkdir build
   cd build 
   ```
4. make project file
   Assume OPUSFOLDER="D:\FFmpegBuild\libopus"
   ```
   cmake .. -G "Visual Studio 17 2022" -A Win32 -DOPUS_STATIC_RUNTIME=ON -DCMAKE_INSTALL_PREFIX="[OPUSFOLDER]\WIN32\MT"
   cmake .. -G "Visual Studio 17 2022" -A Win32 -DCMAKE_INSTALL_PREFIX="[OPUSFOLDER]\WIN32\MD"
   cmake .. -G "Visual Studio 17 2022" -A x64 -DOPUS_STATIC_RUNTIME=ON -DCMAKE_INSTALL_PREFIX="[OPUSFOLDER]\x64\MT"
   cmake .. -G "Visual Studio 17 2022" -A x64 -DCMAKE_INSTALL_PREFIX="[OPUSFOLDER]\x64\MD"
   ```
   -A specifies the platform (x64 or Win32)  
   -DOPUS_STATIC_RUNTIME=ON to make MT/MTd builds  
   -DCMAKE_INSTALL_PREFIX="OPUS_INSTALL_FOLDER" 
6. Build and Install
   Open the generated sln file and build the INSTALL project. Opus will be installed in D:\OPUS\[PLATFORM]\MT or MD folder. For debug builds, rename them to MTd or MDd respectively.

     
## Create working space
Create a folder structure as your working space. It is recommend not to build FFmpeg code in the souce folder. We can create the following folder structure to build FFmpeg. 

```
 FFmpegBuild
 |--FFmpegSrc (FFmpeg source code)
 |--libopus
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
