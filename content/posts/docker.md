+++
title = 'Docker'
date = 2024-03-13T17:33:33+08:00
draft = false
+++

为了统一开发环境，需要生成一个安装好各种开发工具的容器，并能够借助主机的JLink调试芯片

## Windows安装Docker Desktop

- 确保Windows版本不小于19044
- 开启WSL功能

![Untitled](../../images/docker.png)

勾选适用于Linux的Windows子系统

## 运行container

### **拉取Ubuntu镜像**

```bash
docker pull ubuntu:latest
```

可以通过 `docker images` 查看本地的镜像

### 创建容器

```bash
docker run -it --name my_container ubuntu /bin/bash
```

- 在Windows的git-bash中需要将/bin/bash替换为bash

可以通过 docker stop my_container 和 docker start my_container 停止或启动容器

### 容器内安装开发工具

更换容器内的源

```bash
mv /etc/apt/sources.list /etc/apt/sources.list.bak
echo "deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy main restricted universe multiverse" >> /etc/apt/sources.list
echo "deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-updates main restricted universe multiverse" >> /etc/apt/sources.list
echo "deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ jammy-backports main restricted universe multiverse" >> /etc/apt/sources.list
echo "deb http://security.ubuntu.com/ubuntu/ jammy-security main restricted universe multiverse" >> /etc/apt/sources.list
```

注意Ubuntu的版本，jammy对应22.04

在安装 `gdb-multiarch` 遇到依赖的动态库为空的情况，只能手动安装

根据提示的动态库搜索对应的名字

```bash
apt-cache search libipt
```

在容器内 git clone 代码

## 主机VS Code连接容器

前提是容器已经在运行

1. VS Code左下角Open a Remote Window
2. Attach to Running Container
3. 会自动识别到正在运行的container，点击即可连接
4. 打开仓库路径

## 调试

一种思路是把JLink设备从主机挂载到容器内（借助WSL2再安装一个Ubuntu发行版）

也就是从Windows主机→WSL2Ubuntu→Container，但没有成功

调试的通信过程为

GDB→JLink→设备

- GDB与JLink之间为TCP/IP通信
- JLink与设备之间是SWD接口

第二种思路是主机运行`JLinkRemoteServer`，容器内的jlink客户端与之通信

实际调试过程中，会出现断连情况

launch.json配置

```json
        {
            "name": "Debug with JLink(Linux)",
            "request": "launch",
            "type": "cortex-debug",
            "cwd": "${workspaceFolder}",
            "executable": "./build/meter.elf",
            "device": "RN8217",
            "interface": "swd",
            "runToEntryPoint": "main",
            "showDevDebugOutput": "none",
            "servertype": "jlink",
            "preRestartCommands": [
                "file ./build/meter.elf",
                "load",
                //"add-symbol-file ./build/release/release.elf 0x08002030",
                "enable breakpoint",
                "monitor reset",
                "monitor halt"
              ],

            "gdbPath": "gdb-multiarch",
            "objdumpPath": "arm-none-eabi-objdump",
            "serverpath": "JLinkGDBServerCLExe",
            "ipAddress": "172.31.160.1",
        },
```

`172.31.160.1`是主机WSL网卡的地址

第三种思路是主机运行`JLinkGDBServer` ，容器内的GDB客户端与之通信

launch.json配置

```json
        {
            "name": "Debug with JLink(Linux)",
            "request": "launch",
            "type": "cortex-debug",
            "cwd": "${workspaceFolder}",
            "executable": "./build/meter.elf",
            // "device": "RN8217",
            // "interface": "swd",
            "runToEntryPoint": "main",
            "showDevDebugOutput": "none",
            "servertype": "external",
            "preRestartCommands": [
                "file ./build/meter.elf",
                "load",
                //"add-symbol-file ./build/release/release.elf 0x08002030",
                "enable breakpoint",
                "monitor reset",
                "monitor halt"
              ],

            "gdbPath": "gdb-multiarch",
            "objdumpPath": "arm-none-eabi-objdump",
            "serverpath": "JLinkGDBServerCLExe",
            // "ipAddress": "172.31.160.1",
            "gdbTarget": "host.docker.internal:2331"
        },
```
