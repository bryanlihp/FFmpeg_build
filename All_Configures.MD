```
../FFmpegSrc/configure --prefix=../stage/win64/ReleaseMT --incdir=../stage/include --toolchain=msvc --arch=amd64  --enable-yasm --yasmexe=yasm-1.3.0-win64 --enable-asm --disable-debug --enable-static --enable-libmp3lame --extra-cflags='-MT -I"../libmp3lame"'  --extra-ldflags='-LIBPATH:"../libmp3lame/lib/x64/ReleaseMT"'

../FFmpegSrc/configure --prefix=../stage/win64/ReleaseMD --incdir=../stage/include --toolchain=msvc --arch=amd64  --enable-yasm --yasmexe=yasm-1.3.0-win64 --enable-asm --disable-debug --enable-static --enable-libmp3lame --extra-cflags='-MD -I"../libmp3lame"'  --extra-ldflags='-LIBPATH:"../libmp3lame/lib/x64/ReleaseMD"'

../FFmpegSrc/configure --prefix=../stage/win64/DebugMTd --incdir=../stage/include --toolchain=msvc --arch=amd64  --enable-yasm --yasmexe=yasm-1.3.0-win64 --enable-asm --enable-static --enable-libmp3lame --extra-cflags='-MTd -I"../libmp3lame"'  --extra-ldflags='-LIBPATH:"../libmp3lame/lib/x64/DebugMTd"'

../FFmpegSrc/configure --prefix=../stage/win64/DebugMDd --incdir=../stage/include --toolchain=msvc --arch=amd64  --enable-yasm --yasmexe=yasm-1.3.0-win64 --enable-asm --enable-static --enable-libmp3lame --extra-cflags='-MDd -I"../libmp3lame"'  --extra-ldflags='-LIBPATH:"../libmp3lame/lib/x64/DebugMDd"'

../FFmpegSrc/configure --prefix=../stage/win32/ReleaseMT --incdir=../stage/include --toolchain=msvc --arch=x86  --enable-yasm --yasmexe=yasm-1.3.0-win32 --enable-asm --disable-debug --enable-static --enable-libmp3lame --extra-cflags='-MT -I"../libmp3lame"'  --extra-ldflags='-LIBPATH:"../libmp3lame/lib/Win32/ReleaseMT"'

../FFmpegSrc/configure --prefix=../stage/win32/ReleaseMD --incdir=../stage/include --toolchain=msvc --arch=x86  --enable-yasm --yasmexe=yasm-1.3.0-win32 --enable-asm --disable-debug --enable-static --enable-libmp3lame --extra-cflags='-MD -I"../libmp3lame"'  --extra-ldflags='-LIBPATH:"../libmp3lame/lib/Win32/ReleaseMD"'

../FFmpegSrc/configure --prefix=../stage/win32/DebugMTd --incdir=../stage/include --toolchain=msvc --arch=x86 --enable-yasm --yasmexe=yasm-1.3.0-win32 --enable-asm --enable-static --enable-libmp3lame --extra-cflags='-MTd -I"../libmp3lame"'  --extra-ldflags='-LIBPATH:"../libmp3lame/lib/Win32/DebugMTd"'

../FFmpegSrc/configure --prefix=../stage/win32/DebugMDd --incdir=../stage/include --toolchain=msvc --arch=x86  --enable-yasm --yasmexe=yasm-1.3.0-win32 --enable-asm --enable-static --enable-libmp3lame --extra-cflags='-MDd -I"../libmp3lame"'  --extra-ldflags='-LIBPATH:"../libmp3lame/lib/Win32/DebugMDd"'
```
