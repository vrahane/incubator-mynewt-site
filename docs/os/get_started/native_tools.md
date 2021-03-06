# Installing Native Mynewt Tools

This page shows how to install tools for native Mynewt targets (simulated targets on your laptop/computer) without using a Docker container. In other words, it allows you to run Mynewt OS as a native application on your Mac or Linux machine to simulate a target and use the Newt tool running natively on your machine to manage the simulated target. It also allows you to run the test suites for all packages not requiring HW support. You may choose to do this instead of using the build toolchain and Newt tool available in a Docker container.

This page provides guidance for MAC and Linux. See the relevant sections below.

<br>

## Set up toolchain for Mac

### Install Brew

If you have not already installed Homebrew from the 
[`newt` tutorials pages](../../newt/install/newt_mac.md), install it. 

<br>

###Install gcc/libc 

OS X ships with a C compiler called Clang.  To build applications for the Mynewt simulator with, a different compiler is used as default: gcc.

```no-highlight
$ brew install gcc
...
...
==> Summary
🍺  /usr/local/Cellar/gcc/5.2.0: 1353 files, 248M
```

<br>

Check the gcc version you have installed (either using brew or previously installed). The brew-installed version can be checked using `brew list gcc`. The default compiler.yml configuration file in Mynewt expects version 5.x for Mac users, so if the installed version is 6.x and you wish to continue with this newer version, modify the `<mynewt-src-directory>/repos/apache-mynewt-core/compiler/sim/compiler.yml` file to change the default `gcc-5` defined there to `gcc-6`. In other words, replace the lines shown highlighted below:

```hl_lines="2 3"
# OS X.
compiler.path.cc.DARWIN.OVERWRITE: "/usr/local/bin/gcc-5"
compiler.path.as.DARWIN.OVERWRITE: "/usr/local/bin/gcc-5 -x assembler-with-cpp"
compiler.path.objdump.DARWIN.OVERWRITE: "gobjdump"
compiler.path.objsize.DARWIN.OVERWRITE: "objsize"
compiler.path.objcopy.DARWIN.OVERWRITE: "gobjcopy"
```
with the following:

```no-highlight
compiler.path.cc.DARWIN.OVERWRITE: "/usr/local/bin/gcc-6"
compiler.path.as.DARWIN.OVERWRITE: "/usr/local/bin/gcc-6 -x assembler-with-cpp”
```

<br>

In case you wish to use Clang, you can change your `<mynewt-src-directory>/repos/apache-mynewt-core/compiler/sim/compiler.yml` to use Clang. Delete the gcc-5 DARWIN.OVERWRITE lines highlighted below.

```hl_lines="2 3"
# OS X.
compiler.path.cc.DARWIN.OVERWRITE: "/usr/local/bin/gcc-5"
compiler.path.as.DARWIN.OVERWRITE: "/usr/local/bin/gcc-5 -x assembler-with-cpp"
compiler.path.objdump.DARWIN.OVERWRITE: "gobjdump"
compiler.path.objsize.DARWIN.OVERWRITE: "objsize"
compiler.path.objcopy.DARWIN.OVERWRITE: "gobjcopy"
```

<br>

**NOTE:** Both the newer gcc 6.x and Clang report a few warnings but they can be ignored.

<br>

**FURTHER NOTE:** Mynewt developers mostly use gcc 5.x for sim builds; so it may take a little while to fix issues reported by the newer compiler. One option is to **disable warnings**. To do that, remove the `-Werror` flag as an option for the compiler in the  `<mynewt-src-directory>/repos/apache-mynewt-core/compiler/sim/compiler.yml` file as shown below. 

```hl_lines="2"
compiler.flags.base: >
    -m32 -Wall -ggdb
```

You may alternatively choose to **specify the precise warnings to ignore** depending on the error thrown by the compiler. For example, if you see a `[-Werror=misleading-indentation]` error while building the sim image, add `-Wno-misleading-indentation]` as a compiler flag in the same line from the `<mynewt-src-directory>/repos/apache-mynewt-core/compiler/sim/compiler.yml` file.

```hl_lines="2"
compiler.flags.base: >
    -m32 -Wall -Werror -ggdb -Wno-misleading-indentation
```


A third option is to simply **downgrade to gcc 5.x**.

<br>

###Install gdb 

```no-highlight
$ brew install gdb
...
...
==> Summary
🍺  /usr/local/Cellar/gdb/7.10.1: XXX files,YYM
```

<br>

**NOTE:** When running a program with gdb, you may need to sign your gdb
executable.  [This page](https://gcc.gnu.org/onlinedocs/gnat_ugn/Codesigning-the-Debugger.html)
shows a recipe for gdb signing. Alternately you can skip this step and
continue without the ability to debug your mynewt application on your PC.*

<br>

## Set up toolchain for Linux 

The below procedure can be used to set up a Debian-based Linux system (e.g.,
Ubuntu).  If you are running a different Linux distribution, you will need to
substitute invocations of _apt-get_ in the below steps with the package manager
that your distro uses.

<br>

###Install gcc/libc that will produce 32-bit executables: 
```no-highlight
$ sudo apt-get install gcc-multilib libc6-i386
``` 

<br>
       
###Install gdb 

```no-highlight
$ sudo apt-get install gdb

Reading package lists... Done
Building dependency tree       
Reading state information... Done
Suggested packages:
  gdb-doc gdbserver
The following NEW packages will be installed:
  gdb
...
Processing triggers for man-db (2.6.7.1-1ubuntu1) ...
Setting up gdb (7.7.1-0ubuntu5~14.04.2) ...

```

<br>

At this point you have installed all the necessary software to build and test code on a simluator running on your Mac or Linux. Proceed to the [Create Your First Project](project_create.md) section.
