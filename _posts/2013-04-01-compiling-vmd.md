---
layout: post
title:  "Compiling VMD with Python 2.7 and CUDA 5.0"
date:   2013-04-01 19:30:11
categories: diary 
---

[ Originally published [here](https://loadmol.wordpress.com/2013/04/01/compiling-vmd-with-python-2-7-and-cuda-5-0/) ]

No, it’s not an April fool’s joke. And yes, it wasn’t easy to do!

VMD is a powerful and free molecular visualization program that also includes
very useful tools. It by default comes with a well-integrated Tcl interpreter
that allows writing scripts and adding new functionalities. However for Python,
the integrated interpreter that comes with the binary version of VMD is
compiled against Python 2.5 and lacks many useful modules. There might be
tricks to get some modules working with it, but not those that depend on Python
2.7.

So I decided to compile VMD against Python 2.7 (from EPD 7.3) and this way I
would easily have access to all my Python modules including matplotlib. After
spending some time looking into the configure script and what it expects, I was
able to compile VMD 1.9.1 against Python 2.7. Below are the steps that I
followed but first a disclaimer: please note that depending on your system’s
configuration, the necessary steps might *vary*.

- About my system: Fedora 17, 64-bit, GCC 4.6.3, CUDA 5.0, two NVidia 660Ti graphic cards.

- Let’s say I’m installing vmd under /home/user/programs/vmd.

- Download and install [Tcl](https://www.tcl.tk/software/tcltk/download.html) (8.5.13):

```
cd unix
./configure --prefix=/home/user/programs/vmd/other/tcl/8.5.13
make
make test   # don't skip it!
make install
```

- Download and install [Tk]() (8.5.13):

```
cd unix
./configure --prefix=/home/user/programs/vmd/other/tk/8.5.13 --with-tcl=/home/user/vmd/other/tcl/8.5.13/lib
make
make test
make install
```

- Download [Tachyon](http://jedi.ks.uiuc.edu/~johns/raytracer/files/) 0.99 to /home/user/programs/vmd/other and install it:

```
cd unix
make linux-64-thr
```

- Download [STRIDE](http://www.ks.uiuc.edu/Research/vmd/doxygen/extprogs.html) to /home/user/programs/vmd/other and compile it:

```
make 
export STRIDE_BIN=/home/user/programs/vmd/other/stride
```

- Download [VMD](http://www.ks.uiuc.edu/Development/Download/download.cgi?PackageName=VMD) 1.9.1 source file and unzip it. It has two directories; plugins and vmd-1.9.1. 
  First compile plugins:

```
cd plugins
export TCLINC=-I/home/user/programs/vmd/other/tcl/8.5.13/include
export TCLLIB=-F/home/user/programs/vmd/other/tcl/8.5.13/lib
make LINUXAMD64

mkdir /home/user/programs/vmd/plugins
export PLUGINDIR=/home/user/programs/vmd/lib/plugins
make distrib
```

- Now we have to set some environmental variables. I wanted to compile VMD against my EPD 7.3 (let’s say it is under /home/user/programs/epd/7.3):

```
EPD=/home/user/programs/epd/7.3
export PYTHON_INCLUDE_DIR=$EPD/include/python2.7
export PYTHON_LIBRARY_DIR=$EPD/lib
export NUMPY_INCLUDE_DIR=$EPD/lib/python2.7/site-packages/numpy/core/include
export NUMPY_LIBRARY_DIR=$EPD/lib

TCLTK=/home/user/programs/vmd/other
export TCL_INCLUDE_DIR=$TCLTK/tcl/8.5.13/include
export TCL_LIBRARY_DIR=$TCLTK/tcl/8.5.13/lib
export TK_INCLUDE_DIR=$TCLTK/tk/8.5.13/include
export TK_LIBRARY_DIR=$TCLTK/tk/8.5.13/lib
```

- Now we will edit the VMD’s configure script:

```
$install_bin_dir="/home/user/programs/vmd/bin";
$install_library_dir="/home/user/programs/vmd/lib";

# for tachyon
$libtachyon_dir="/home/user/programs/vmd/other/tachyon";
$libtachyon_include="-I/home/user/programs/vmd/other/tachyon/src";
$libtachyon_library="-L/home/user/programs/vmd/other/tachyon/compile/linux-64-thr";

# for Python
$python_libs = "-lpython2.7 -lpthread";

# for netCDF
$netcdf_dir = "/usr";
$netcdf_include = "-I$netcdf_dir/include";
$netcdf_library = "-L$netcdf_dir/lib64";

# there is a compile flag called "-ll" (two "L"s) which I had to remove in order to be able to compile VMD. Not sure what library it specifies though.

# and finally if you have cuda
$cuda_dir = "/home/user/programs/cuda/5.0";
$cuda_include = "-I$cuda_dir/include";
$cuda_library = "-L$cuda_dir/lib64";   # there are three instances of this line. modify all of them
```

- Now compile VMD:

```
./configure LINUXAMD64 OPENGL TK CUDA IMD XINERAMA XINPUT NETCDF TCL PYTHON PTHREADS NUMPY SILENT GCC LIBTACHYON

cd src
make 
make install
```

### Notes:


- You might be able to use the default Tcl/Tk of your linux distribution. Then there’s no need for compiling them.

- I used the FLTK that came with Fedora. VMD didn’t compile with FLTK 1.3 and I couldn’t compile FLTK 1.1.

- In the vmd-1.9.1 directory, there is a lib directory which you can put and compile many of the pre-requisites there, such as Tachyon. Each subfolder has instructions on how to obtain the corresponding library.

- I tried to compile VMD on my Mac too. No luck yet but I’ll give it another shot if I find some time.

 -If an error occurs during compilation, carefully check it. Sometimes it’s not mysterious at all and clearly tells you what went wrong (such as a missing library). If not, look it up to find out what might have caused it.

I have yet to test the compiled VMD thoroughly but the only problem that I’ve
noticed so far is that you need to turn off your desktop compositor (like
Compiz) if you have it on. Other than that, things seem to be working fine. The
compiled version looks nicer visually (the GUI part, which uses FLTK) and
Python 2.7 seems to work well. Numpy and other modules can be loaded and you
also have access to VMD’s own modules (Molecule/molrep…). I’ll show some Python
examples later.

