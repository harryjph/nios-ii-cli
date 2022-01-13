# Nios II CLI Without WSL or Eclipse

This guide will show you how to use the Nios II CLI, and create/run Nios II C projects on Windows **without WSL or Eclipse**.

## Prerequisites

* Windows (duh)
* Quartus Prime Lite (if you have a version other than `21.1` you will need to modify one of the utility scripts, see "Gotchas" below)
* A `bash` terminal

## Setup

1. Clone this repository
2. Add the `Utilities` directory to `%PATH`
3. When you want to start a Nios II CLI session, open a Bash terminal and run `nios2-cli`

## Gotchas

* **You may have to modify the `Utilities/nios2-cli` script if your Nios II installation directory is not `C:\intelFPGA_lite\21.1\nios2eds\`. See lines 3-4 of the `nios2-cli` script.**

* **A lot of the Nios II tools can't handle whitespace in paths because whoever made them wants to watch the world burn. This means you must install the utility scripts and keep all your projects in a directory that contains no spaces in its full path.**

* **Whenever specifying a directory path in a command, it is best to always use relative paths to avoid compatibility issues between Linux and Windows path formats.**

## Using the Nios II CLI

### Creating a BSP (Board Support Package)

You will need a `.sopcinfo` file that describes your Nios II system. This is produced by Platform Designer (Qsys).

* In a Nios II CLI session, run `nios2-bsp {bspType} {bspDir} {sopcFile}` where `bspType` is `hal` or `ucosii` depending on if you are running on bare metal or if you want to use μC/OS-II (respectively), `bspDir` is the directory to create the BSP in, and `sopcFile` is the path to your `.sopcinfo` file.

* Then run `nios2-fixbsp {bspDir}`, where `bspDir` is the directory in which you created the BSP in the previous step. This fixes a bug in the default makefile, I'm not sure why the bug happens.

I recommend writing a bash script that will perform these two steps for you, as you do **not** want to check the BSP into VCS.

### Creating a new project / Adding new files to an existing project / Generating a Makefile

The key step to either of these tasks is to generate the `Makefile`. The `Makefile` is very big, however, so I recommend not checking it into VCS and instead writing a script that will generate it

Create a bash script that runs `nios2-app-generate-makefile --bsp-dir {bspDir} --src-dir .` where `bspDir` is your BSP directory. This will add all files in the current directory to the `Makefile`. To specify additional source directories, add `--src-dir {dir}` or `--src-files {file1} {file2}` command line flags.

Run it in a Nios II CLI session in the project directory.

### Creating a project from an example

You will need a BSP. See above for how to create a BSP.

* Create a project directory

* The examples are located in `{nios2dir}/examples/software/` where `nios2dir` is your Nios II installation directory. Usually this is `C:\intelFPGA_lite\{version}\nios2eds`, but yours might be different.

* Each example has a directory. Choose an example, copy its contents to your project directory and delete `template.xml`.

* Now follow the instructions for creating a new project.

### Compiling a project

In a Nios II CLI session in your project directory, run `make`. This will generate an `.elf` file in the project directory.

### Programming

In a Nios II CLI session, run `nios2-download -g {elfFile}` where `elfFile` is the ELF file generated by compiling the project.

### Connecting to the JTAG UART

In a Nios II CLI session, run `nios2-terminal`.

## Setup explanation

Normally, Eclipse IDE is required to use the Nios II tools. However, I ~~hate~~ ~~loathe~~ ~~detest~~ **DESPISE** Eclipse. Therefore I have developed the following CLI methods to avoid using it.

You're supposed to install WSL. However, I don't want to. So I won't. This guide shows you how to get the tools working without WSL.

The normal start menu shortcut to start a Nios II CLI session won't work - it will complain that WSL isn't installed.

There is a script in the `Utilities` directory that initializes a Nios II CLI session in the current Bash window. **You must add the `Utilities` directory to PATH** so that you can just run `nios2-cli` in any Bash window to start a Nios II CLI session. This also makes the other included utilities available.

You can also run any Nios II command directly using the script. For example, to start a Nios II terminal, run `nios2-cli nios2-terminal` outside of a Nios II CLI session, and it will start a session and run the command `nios2-terminal`. When the terminal session ends, the script will terminate, instead of leaving you in a Nios II CLI session.
