Environment settings

1. Install Visual Studio 2015
2. Download msys2 64 bit form this link http://msys2.github.io and install it.(in my case installed "msys2-i686-20160205.exe" in directory is "c:\msys64").
3.  Run it and run below commands
  - update-core (Update the system packages with)
  close MSYS2, run it again and run below commands
  - pacman -Sy
  - pacman -Su  (sometimes this command creates error then you have uninstall msys2 and install again. In that case you can ignore this command)
  - pacman -S pkg-config make diffutils 
4.  go to C:\msys64\usr\bin and rename "link.exe" to "link_backup.exe". Because we will not use this link. we will use visual studio link.
5.  Download yasm win64 ".exe" version from http://yasm.tortall.net/Download.html 
(in my case file name was "yasm-1.3.0-win64.exe")after downloading exe file rename it as "yasm.exe" and place it or replace if already exists in "C:\msys64\usr\bin" directory.

Checking:
Open visual studio Developer Command Prompt for VS2015 or VS2015 x64 Native Tools 
- Run C:\msys64\msys2_shell.cmd -mingw64 -use-full-path
- Run below commands and check output
which cl
which link
which lib
which yasm

Step to build x264
1.  Download source code of latest x264 from this website.
http://www.videolan.org/developers/x264.html
2.  Extract the files in a directory (in my case "c:\codecsBuild\x264")
3.  Run VS2015 X64 Native Tool and open MSYS2 by running command "msys2_shell.cmd -mingw64 -use-full-path"
4.  Go to the directory where x264 placed
5.  Run below command to create x264 shared library
CC=cl ./configure --disable-cli --disable-asm --enable-shared --enable-pic --prefix=../Codecs --extra-cflags="-DNO_PREFIX"
6.  Run make install

Step to build ffmpeg with/without x264

1.  If you want to build ffmpeg with x264 then you have to build x264 first (discussed earlier). Otherwise follow below steps..
2.  Download  source code of latest ffmpeg from this website. http://ffmpeg.org/download.html
3.  Extract the files in a directory (in my case " c:\codecsBuild\ffmpeg ").
4.  Open visual studio Developer Command Prompt for VS2015 or VS2015 x64 Native Tools Command Prompt(2nd one is preferable). 
5.  Run c:\msys64\msys2_shell.cmd -mingw64 -use-full-path 
    this will open new command line interface.
6.  Now go to the directory where you have extracted ffmpeg using command line. In my case command is
    cd /c/codecsBuild/ffmpeg
7.  Now run below command to create ffmpeg shared library with x264.

  ./configure --toolchain=msvc --target-os=win32 --arch=x86 --enable-gpl --prefix=../Codecs --disable-asm --enable-shared --disable-static --disable-stripping --disable-everything --disable-doc --disable-ffplay --disable-ffserver --enable-ffmpeg --disable-programs --disable-ffprobe --enable-libx264 --enable-encoder=libx264 --enable-decoder=h264 --enable-encoder=h263 --enable-decoder=h263 --enable-hwaccels --enable-avfilter --disable-devices --disable-avdevice --disable-swresample --disable-postproc --extra-cflags="-I../Codecs/include -fPIC -DNO_PREFIX"  --extra-ldflags="-LIBPATH:../Codecs/lib"

    Options are optional. To see the options you can run "./configure --help".
    To create static library use "--enable-static --disable-shared" instead instead of "--enable-shared --disable-static".
    "-fpic" means  build position-independent code.
    "--prefix" means the path where built codec will be created. (in my case the path is "../Codecs"  equivalent to "c:\codecsBuild\Codecs")
     -I../Codecs/include is used in --extra-cflags to show the compiler the path of the header files of x264 which is located in "../Codecs/include" directory.
     -LIBPATH:../Codecs/lib is used in --extra-ldflags to show the compiler the path of the libx264.lib file which is located in "../Codecs/lib" directory.
    N.B.: if you search online, you will find --extra-ldflags="-L<path>". Which will work for gcc compiler. But for visual studio compiler you have to use  --extra-ldflags="-LIBPATH:<path>" and use --toolchain=msvc (micro soft visual-studio compiler)
    If you want to build only ffmpeg without x264 then 
    remove --enable-libx264 --enable-encoder=libx264 --enable-decoder=h264 from command.
    replace --extra-cflags="-I../Codecs/include -fPIC -DNO_PREFIX" with 
    --extra-cflags="-fPIC -DNO_PREFIX"
     remove --extra-ldflags="-LIBPATH:../Codecs/lib"
     
8.  Now run make install
    Thats it. you will see 3 folders in prefix directory.
    "bin" folder contains all the dll and lib files.
    "include" folder contains all header files.
    "lib" folder contains "pkconfig" folder and other def files.
    

