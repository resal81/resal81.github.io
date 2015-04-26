---
layout: post
title:  "How to compile GroGUI 0.5.x in (K)ubuntu 8.04 and OpenSuse 10.3"
date:   2008-09-11 09:51:11
categories: diary 
---


### (K)ubuntu 8.04
- Use Adept and install these packages: `g++, make, libqt4-gui, libqt4-dev, libqwt5-qt4, libqwt5qt4-dev`.

- Download GromacsGUI.

- Extract files.

- Go to `gui_withplotting` directory.

- Edit the bold lines of grogui.pro file to be like this:

```

DEPENDPATH += . \
include \
src \
ui \
**/usr/include/qwt-qt4**

INCLUDEPATH += . \
include \
ui \
**/usr/include/qwt-qt4**

CONFIG += release
QT += qt3support
LIBS += **-lqwt-qt4**
```


- Now open a terminal in this directory and run these commands:

```
qmake
make
```

- Now enter:

```
./grogui
```

### Opensuse 10.3


- Use Yast or Smart to install these packages: `g++, make, libqt4-gui, libqt4-dev, libqwt5, libqwt5-dev`.

- Download GromacsGUI .

- Extract files.

- Go to gui_withplotting directory.

- Edit the bold lines of `grogui.pro` file to be like this:

```
DEPENDPATH += . \
include \
src \
ui \
**/usr/include/qwt5**

INCLUDEPATH += . \
include \
ui \
**/usr/include/qwt5**

CONFIG += release
QT += qt3support
LIBS += **-lqwt**
```

- Now open a terminal in this directory and run these commands:

```
qmake
make
```

- Now enter:

```
./grogui
```

[ Originally published [here](https://resal.wordpress.com/2008/09/11/how-to-compile-grogui-05x-in-kubuntu-804-and-opensuse-103/). ]
