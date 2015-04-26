---
layout: post
title:  "How to compile GromacsGUI 0.5.x"
date:   2008-09-11 19:51:11
categories: diary 
---


This is a mini-howto for compiling GromacsGUI mainly for those who may have not
worked with compiling stuff before, or want to test the software in linux
distributions other than (k)ubuntu 8.04 and OpenSuse 10.3 for which I explained
exact steps.

#### Basics

- For compiling GromacsGUI, you need both Qt4 (>4.3.x) and Qwt (>5.x) and their devel packages.

- Use your distribution package manager to install these packages. They are usually called like these: `libqt4, libqt4-dev, libqwt5-qt4 and libqwt5-qt4-dev`.

You should also have make and g++ installed. If you don’t have them, install them too.

- Now download GromacsGUI and extract the downloaded file. You should have two directories now: `grogui_noplotting` and `gui_withplotting`.

- We will try to compile GroGUI with plotting support (`gui_withplotting`) so go to this directory. Then you have to tell the compiler where it can find Qt and Qwt files. We do this using grogui.pro file which looks like this:

```
DEPENDPATH += . \
include \
src \
ui \
/usr/include/qwt5
INCLUDEPATH += . \
include \
ui \
/usr/include/qwt5
LIBS += -lqwt
```

These three entries are most important ones which specifies the locations of Qt and Qwt files. We will edit the bold items accordingly.

- When you install Qt and Qwt using a package manager, they are usually installed in your PATH. PATH is a series of directories that linux searches to find a specified program. You can see your PATH by typing this in a terminal and hitting Return key:

```
echo $PATH
```

and you will see something like this

```
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games
```

You can use your package manager to check where Qt4 and Qwt5 files are installed. For example, in adept click on package name, then push “Details” button then go to “Installed Files” tab.

- There is usually no problem with Qt4 and it is installed correctly. However for Qwt5, installation names are different among different distributions so we have to edit grogui.pro for “Qwt lib name and include files path”.

lib files are usually installed in `/usr/lib` or `/usr/local/lib` . Go to these directories and look for something that has `libqwt` in its name like `libqwt.so.1` or `libqwt-qt4.so.1`.

Now, you must use this name in `“LIBS +=”` section of grogui.pro file in this way:

  - remove `lib` and `.so.*` from library filename: `libqwt-qt5.so.1 —> qwt-qt4` or `libqwt5.so.1.0.1 —> qwt5`
  - add a `-l` to at the beginning of library name: `qwt-qt4`
  - now type this name in front of `“LIBS +=”` field: `LIBS += -lqwt-qt4`.

- Now you must locate the pah of Qwt5 include files. You can use your package manager to find out where they are installed. Usually they are installed in `/usr/include` or `/usr/local/include`. Look for a directory name that contains “qwt”. Like `/usr/include/qwt-qt4` or ``/usr/include/qwt5`. It contains files like `qwt_abstract_scale_draw.h` and `qwt_plot_item.h`.

Now replace the lines shown in bold above in `“DEPENDPATH +=”` and `“INCLUDEPATH += entries”`. For example if our include path is `/usr/include/qwt-qt4` our `grogui.pro` file must looks like this:

```
DEPENDPATH += . \
include \
src \
ui \
/usr/include/qwt-qt4
INCLUDEPATH += . \
include \
ui \
/usr/include/qwt-qt4
LIBS += -lqwt-qt4
```

- Now save your `grogui.pro` file. Open a terminal `grogui.pro` directory and enter:

```
qmake
```

and when it finishes, enter:

```
make
```

- Now test the first run. Enter:

```
./grogui
```

Hope you see the program window!


[ Originally published [here](https://resal.wordpress.com/2008/09/11/how-to-compile-gromacsgui-05x/). ]
