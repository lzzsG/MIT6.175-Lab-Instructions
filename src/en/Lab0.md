# Lab 0: Getting Started

Throughout this course, you will use shared machines for working on the labs. These machines include vlsifarm-03.mit.edu through vlsifarm-08.mit.edu. You can log into these machines through ssh using your Athena username and password.

This document will instruct you how do some things required for lab such as obtaining initial code for each lab. Begin by using an ssh client to log into one of the servers named above.

## Setting up the toolchain

Execute the following commands to set up your environment and gain access to the tool-chain:

```shell
$ add 6.175
$ source /mit/6.175/setup.sh
```

The first command gives you access to the course locker /mit/6.175 and only needs to be run once per computer. The second command configures your current environment to include tools required for the lab and needs to be run every time you log in to work on classwork.

## Using Git to get and submit lab code

The reference designs are provided in Git repositories. You can clone them into your work directory using the following command (substitute labN with the lab number, such as lab1 and lab2):

```shell
$ git clone $GITROOT/labN.git
```

> **Note:** If "git clone" fails, it's probably because we don't have your Athena user name. Send me an email (to qmn mit) and I'll create a remote repository for you.

This command creates a labN directory in your current directory. The $GITROOT environment variable is unique to you, so this repository will be your personal repository. Inside that directory, the test benches can be run using the directions specified in the lab handouts.

Discussion questions should be answered in discussion.txt file supplied with the rest of the code.

If you want to add any new files in addition to what has been supplied by the TAs, you need to add the new file (in this example, newFile in git using:

```shell
$ git add newFile
```

You can locally commit your code whenever you hit a milestone using:

```shell
$ git commit -am "Hit milestone"
```

Submit your code by adding any necessary files and then using:

```shell
$ git commit -am "Finished lab"
$ git push
```

You can submit multiple times before the deadline if necessary.

## Writing Bluespec SystemVerilog (BSV) for the labs

### On vlsifarm-0x

6.175 will be a great opportunity to learn how to work in a Linux command-line environment if you are not already familiar with it. To test your BSV code, you need to use a Linux environment to run bsc, the BSV compiler. It makes sense to go ahead and write the BSV code on the same machine.

While there are many text editors you can use, there is only Bluespec-provided BSV syntax highlighting for Vim and Emacs. The Vim syntax highlighting files can be installed by running:

```shell
$ /mit/6.175/vim_copy_BSV_syntax.sh
```

The Emacs syntax highlighting files can be found on the [course resources](http://csg.csail.mit.edu/6.175/archive/2016/resources.html) page. Your TA used to use Emacs, but converted to Vim. He cannot claim to know how to install the highlighting mode files, or even if they work. If you are an Emacs user and would like to contribute documentation on this matter, please send an email to the course staff.

### On the Athena cluster

Your home directory on the vlsifarm machines is the same as your home directory on any Athena machine. Therefore you can write code on an Athena machine using gedit or another graphical text editor and log into a vlsifarm machine to run it.

### On your own machine

You can also use file transfer programs to move files between your Athena home directory and your own machine. MIT has help for securely transferring files between machines on the web at http://ist.mit.edu/software/filetransfer.

## Compiling BSV on other machines

BSV can also be compiled on non-vlsifarm machines. This may be useful when the vlsifarm machines are busy near lab deadlines.

### On the Athena cluster

The instructions used for the vlsifarm machines will also work for the Linux-based Athena machines. Just open a terminal and run the commands as you would run them on the vlsifarm machines.

### On your own Linux-based machine

To run the 6.175 labs on your own Linux-based machine, you will need the following software installed on your computer:

- OpenAFS to access the course locker
- Git to access and submit the labs
- [GMP (libgmp.so.3)](https://gmplib.org/) to run the BSV compiler
- Python to run build scripts

> **Side note:** A similar setup may work for Mac OS X / macOS. If you get such a setup working, please provide details to the TA.

#### OpenAFS

Installing OpenAFS on your local machine will give you access to the directory /afs/athena.mit.edu that contains all of the course lockers. You will have to create your own /mit folder with symlinks within to point to the necessary course lockers.

CSAIL TIG has some information about how to install OpenAFS for Ubuntu at http://tig.csail.mit.edu/wiki/TIG/OpenAFSOnUbuntuLinux. These instructions are for accessing /afs/csail.mit.edu, but you need access to /afs/athena.mit.edu for the lab, so replace csail with athena wherever you see it. When you install OpenAFS on your machine, it gives you a /afs folder with many domains within. This website also contains the instructions for logging in with your user name and password to gain access to the files that require authentication. You will need to do this every day you work on the lab, or every time you reset your computer, in order to access the 6.175 course locker.

Next you need to make a folder named mit in your root directory and populate it with a symlink to the course repository. On Ubuntu and similar distributions, the commands are:

```shell
$ cd /
$ sudo mkdir mit
$ cd mit
$ sudo ln -s /afs/athena.mit.edu/course/6/6.175 6.175
```

You can now access the course locker in the folder /mit/6.175.

#### Git

On Ubuntu and similar distributions, you can install Git with

```shell
$ sudo apt-get install git
```

#### GMP (libgmp.so.3)

The BSV Compiler uses libgmp for unbounded integers. To install it on Ubuntu and similar distributions, use the command

```shell
$ sudo apt-get install libgmp3-dev
```

If you have libgmp installed on your machine, but you do not have libgmp.so.3, you can create a symlink named libgmp.so.3 that points to a different version.

#### Python

On Ubuntu and similar distributions, you can install Python with

```shell
$ sudo apt-get install python
```

#### Setting up the toolchain on your Linux-based machine

The original setup.sh script will not work on your machine, so instead you will have to use

```shell
$ source /mit/6.175/local_setup.sh
```

to set up the toolchain. Once you have done this, you should be able to use the tools as usual on your own machine.

------

© 2016 [Massachusetts Institute of Technology](http://web.mit.edu/). All rights reserved.

