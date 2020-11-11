---
layout: post
title:  "Run C/C++ program from Windows Subsystem for Linux in Visual Studio Code"
date:   2019-09-22
categories: life-saver
tags: wsl C/C++ vs-code
---

* content
{:toc}

I want to use VS Code to edit C/C++ code on Windows but compile and run executable on WSL through the VS Code UI. Note that this is not necessary. You can simply navigate to `/mnt/c/Users/<path_to_file>`, the path of the source `.c` file on Windows under the WSL file system, do `cc xxx.c` and the `a.out` file would be produced.



## Preparation

I read [this article](https://code.visualstudio.com/docs/cpp/config-wsl).

## Method

### Installation

1. Get windows subsystem for Linux following [this guide](https://docs.microsoft.com/en-us/windows/wsl/install-win10).
2. Install [Visual Studio Code](https://code.visualstudio.com/download) and the [`C/C++` extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools).
3. In WSL, type 
```
sudo apt-get update
sudo apt install gcc
sudo apt-get install build-essential gdb
```
to install `gcc` (compiler for C), `g++` (compiler for C++), and `gdb` (debugger). Type
```
whereis gcc
whereis g++
whereis gdb
```
to verify that they are installed.

### Create project folders
1. In WSL, make a directory `projects` and subdirectory `helloworld` for the sample project `helloworld` using
```
mkdir projects
cd projects
mkdir helloworld
```
This is where the executable will be placed.
2. In Windows, create a folder `helloworld`.

### Configure VS Code
1. In VS Code, press `Ctrl+Shift+P`, type `C/C++` and select `Edit Configurations (UI)`. Set `Compiler path` to `/usr/bin/gcc` and `IntelliSense mode` to `gcc-x64`.
2. Open `helloworld/.vscode/c_cpp_properties.json`, the file should look like this
```
{
    "configurations": [
        {
            "name": "Win32",
            "includePath": [
                "${workspaceFolder}/**"
            ],
            "defines": [
                "_DEBUG",
                "UNICODE",
                "_UNICODE"
            ],
            "compilerPath": "/usr/bin/gcc",
            "cStandard": "c11",
            "cppStandard": "c++17",
            "intelliSenseMode": "gcc-x64"
        }
    ],
    "version": 4
}
```
1. In VS Code, go to `View > Command Palette > Configure Default Build Task > Create tasks.json file from template > Others`. In the `tasks.json` file that just popped up, paste the following content, replacing `<linux user name>` with your Linux username.
```
{
    "version": "2.0.0",
    "windows": {
      "options": {
        "shell": {
          "executable": "bash.exe",
          "args": ["-c"]
        }
      }
    },
    "tasks": [
      {
        "label": "build on WSL",
        "type": "shell",
        "command": "g++",
        "args": [
          "-g",
          "-o",
          "/home/<linux user name>/projects/helloworld/helloworld.out",
          "'${relativeFileDirname}/${fileBasename}'"
        ],
        "group": {
          "kind": "build",
          "isDefault": true
        },
        "problemMatcher": [
            "$gcc"
        ]
      },
      {
        "label": "run on WSL",
        "type": "shell",
        "command": "/home/<linux user name>/projects/helloworld/helloworld.out",
        "group": {
            "kind": "build",
            "isDefault": true
        },
        "problemMatcher": [
            "$gcc"
        ]
        }
    ]
  }
```
The task `build on WSL` builds the program and creates an executable `helloworld.out`. The task `run on WSL` runs the executable and prints the output in VS Code.
1. For debugging: In `json`, create a `launch.json` file and paste the following content, replacing `<linux username>` and `<windows username>` with your Linux and Windows usernames, respectively.
```
{
  "version": "0.2.0",
  "configurations": [
    
    {
      "name": "(gdb) Launch",
      "preLaunchTask": "build on WSL",
      "type": "cppdbg",
      "request": "launch",
      "program": "/home/<linux username>/projects/helloworld/helloworld.out",
      "args": [""],
      "stopAtEntry": true,
      "cwd": "/home/<linux username>/projects/helloworld/",
      "environment": [],
      "externalConsole": true,
      "windows": {
        "MIMode": "gdb",
        "miDebuggerPath": "/usr/bin/gdb",
        "setupCommands": [
          {
            "description": "Enable pretty-printing for gdb",
            "text": "-enable-pretty-printing",
            "ignoreFailures": true
          }
        ]
      },
      "pipeTransport": {
        "pipeCwd": "",
        "pipeProgram": "c:\\windows\\sysnative\\bash.exe",
        "pipeArgs": ["-c"],
        "debuggerPath": "/usr/bin/gdb"
      },
      "sourceFileMap": {
        "/mnt/c": "${env:systemdrive}/",
        "/usr": "C:\\Users\\<windows username>\\AppData\\Local\\Packages\\CanonicalGroupLimited.UbuntuonWindows_79rhkp1fndgsc\\LocalState\\rootfs\\usr\\"
      }
    }
  ]
}
```

### Add source code file
1. In VS Code, in the folder `helloworld`, create a `helloworld.cpp` or `helloworld.c` file and paste the following content:
```c
#include<stdio.h>
int main()
{
    printf("Hello world");
    return 0;
}
```
2. Save the file.

### Build the program
`Ctrl-Shift-B > build on WSL` to build the program `helloworld.cpp` or `helloworld.c` on Windows using `gcc` installed in WSL and create an executable `/home/<linux username>/projects/helloworld/helloworld.out`. You should see a message
```
> Executing task: g++ -g -o /home/<linux username>/projects/helloworld/helloworld.out 'helloworld.c' <


Terminal will be reused by tasks, press any key to close it.
```

### Run the program executable
`Ctrl-Shift-B > run on WSL` to run the executable `/home/<linux username>/projects/helloworld/helloworld.out`. You should see the output 
```
> Executing task: /home/<linux username>/projects/helloworld/helloworld.out <

Hello world
Terminal will be reused by tasks, press any key to close it.
```