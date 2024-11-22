- **通过结合 Wine 运行某些 Windows x86_64 程序和游戏**

在 deepin (ARM64 & RISC-V64)、Debian (ARM64 & RISC-V64)、Ubuntu (ARM64) 等系统中，Box64 可通过结合 Wine64 运行 Windows x86_64 程序。以 deepin RISC-V64 系统运行 notepad++ 为例，使用 Box64 安装配置 Wine64 并运行 Windows x86_64 程序的具体步骤如下：

1. **安装依赖项**：
   
   输入以下指令安装所需的依赖项（不同架构的系统将会安装对应架构的软件包）：
   
   ```
   sudo apt update
   sudo apt-get install -y libasound2 libc6 libglib2.0-0 libgphoto2-6 libgphoto2-port12 \
           libgstreamer-plugins-base1.0-0 libgstreamer1.0-0 libldap-common libopenal1 libpcap0.8 \
           libpulse0 libsane1 libudev1 libunwind8 libusb-1.0-0 libvkd3d1 libx11-6 libxext6 \
           ocl-icd-libopencl1 libasound2-plugins libncurses6 libncurses5 libcups2 \
           libdbus-1-3 libfontconfig1 libfreetype6 libglu1-mesa libgnutls30 \
           libgssapi-krb5-2 libjpeg62-turbo libkrb5-3 libodbc1 libosmesa6 libsdl2-2.0-0 libv4l-0 \
           libxcomposite1 libxcursor1 libxfixes3 libxi6 libxinerama1 libxrandr2 \
           libxrender1 libxxf86vm1 libc6 libcap2-bin 
   ```

2. **手动下载安装 amd64 架构的 Wine64：**
   
   如果使用 apt 直接从仓库安装 wine64，我们得到的将会是 ARM64 或 RISC-V64 架构的软件，而 Box64 的主要功能是，在 ARM64 或 RISC-V64 架构的系统上，翻译运行 amd64 程序，故从仓库下载的 wine64 将无法配合 Box64 运行。
   
   所以，我们需要手动下载安装 amd64 架构的 wine64.
   
   从官方镜像源下载 Wine64 及其组件的 deb 包（以 9.0.0.0 stable 版本为例）：
   
   ```
   wget https://dl.winehq.org/wine-builds/debian/dists/bullseye/main/binary-amd64/wine-stable-amd64_9.0.0.0~bullseye-1_amd64.deb
   wget https://dl.winehq.org/wine-builds/debian/dists/bullseye/main/binary-amd64/wine-stable_9.0.0.0~bullseye-1_amd64.deb
   ```
   
   提取文件：
   
   ```
   mkdir wine-installer
   dpkg-deb -x wine-stable-amd64_9.0.0.0~bullseye-1_amd64.deb wine-installer
   dpkg-deb -x wine-stable_9.0.0.0~bullseye-1_amd64.deb wine-installer
   ```
   
   手动安装：
   
   ```
   mkdir ~/wine
   mv wine-installer/opt/wine-stable/* ~/wine  #将可执行文件和库移动到wine目录
   # 创建软链接
   sudo ln -s ~/wine/bin/wine64 /usr/local/bin/wine64
   sudo ln -s ~/wine/bin/wineboot /usr/local/bin/wineboot
   sudo ln -s ~/wine/bin/winecfg /usr/local/bin/winecfg
   sudo ln -s ~/wine/bin/wineserver /usr/local/bin/wineserver
   sudo chmod +x /usr/local/bin/wine64 /usr/local/bin/wineboot /usr/local/bin/winecfg /usr/local/bin/wineserver
   ```

3. **使用 box64 运行 wine64：**
   
   首次运行 wine64 时，需要创建一个新的 Windows 用户环境，使用 box64 翻译运行：
   
   ```
   box64 wine64 wineboot
   ```
   
   此命令会默认在`~/.wine`目录创建一个64位的 wineprefix，进入此目录可以看见一个名为`drive_c`的文件夹，里面存放着`Program Files`、`windows`等目录，相当于Windows 系统的 C 盘。

4. **运行 Windows 应用：**
   
   把 Windows 系统的应用直接拷贝到 ARM64 或 RISC-V64 Linux 系统上运行，以 notepad++ 为例：
   
   ![files](https://github.com/Woomeeme/Box64-Wine64/blob/main/images/files-notepad%2B%2B.png)
   
   运行 notepad++.exe：
   
   ```
   box64 wine64 notepad++.exe
   ```
   
   运行效果：
   
   ![running](https://github.com/Woomeeme/Box64-Wine64/blob/main/images/running-notepad%2B%2B.png)
   
   由于经过了系统环境和架构的双重转换，加上设备性能的限制，运行起来会有一些卡顿；此外，若要运行更加复杂的应用，比如基于 directX 的游戏，则还需要在 wine64 创建的 wineprefix 中安装对应的依赖环境，目前有待进一步测试。
