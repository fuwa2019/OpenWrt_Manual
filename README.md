# OpenWrt_Manual

## 首次编译：
1. 首先装好 Linux 系统，推荐 Debian 11 或 Ubuntu LTS

2. 安装编译依赖

   ```bash
   sudo apt update -y
   sudo apt full-upgrade -y
   sudo apt install -y ack antlr3 aria2 asciidoc autoconf automake autopoint binutils bison build-essential \
   bzip2 ccache cmake cpio curl device-tree-compiler fastjar flex gawk gettext gcc-multilib g++-multilib \
   git gperf haveged help2man intltool libc6-dev-i386 libelf-dev libglib2.0-dev libgmp3-dev libltdl-dev \
   libmpc-dev libmpfr-dev libncurses5-dev libncursesw5-dev libreadline-dev libssl-dev libtool lrzsz \
   mkisofs msmtp nano ninja-build p7zip p7zip-full patch pkgconf python2.7 python3 python3-pip libpython3-dev qemu-utils \
   rsync scons squashfs-tools subversion swig texinfo uglifyjs upx-ucl unzip vim wget xmlto xxd zlib1g-dev
   ```

3. 下载源代码，更新 feeds 并选择配置

   ```bash
   git clone https://github.com/coolsnowwolf/lede openwrt
   cd openwrt
   echo "src-git ssrp https://github.com/fw876/helloworld.git" >> ./feeds.conf.default
   ./scripts/feeds update -a
   ./scripts/feeds install -a
   make menuconfig
   ```

4. 下载 dl 库，编译固件
（-j 后面是线程数，第一次编译推荐用单线程）

   ```bash
   make download -j8
   find dl -size -1024c -exec ls -l {} \;
   #find dl -size -1024c -exec rm -f {} \; | make download  #列出下载不完整的文件，如果存在这样的文件将它们删除，然后重新下载并反复检查
   make V=s -j1
   ```
 
## 再次编译：
1. 更新
 ```sudo sh -c "apt update && apt upgrade -y"
 git pull
 ./scripts/feeds update -a
 ./scripts/feeds install -a
 ```
2.文件清理
  ```make clean```
  清除旧的编译产物（可选）
  在源码有大规模更新或者内核更新后执行，以保证编译质量。此操作会删除/bin和/build_dir目录中的文件。
  清除旧的编译产物、交叉编译工具及工具链等目录（可选）

  ```make dirclean```
  更换架构编译前必须执行。此操作会删除/bin和/build_dir目录的中的文件(make clean)以及/staging_dir、/toolchain、/tmp和/logs中的文件。
  清除 Open­Wrt 源码以外的文件（可选）

  ```make distclean```
  除非是做开发，并打算 push 到 GitHub 这样的远程仓库，否则几乎用不到。此操作相当于make dirclean外加删除/dl、/feeds目录和.config文件。
  还原 Open­Wrt 源码到初始状态（可选）

  ```git clean -xdf```
  如果把源码改坏了，或者长时间没有进行编译时使用。
  清除临时文件

  ```rm -rf tmp```
  删除执行make menuconfig后产生的一些临时文件，包括一些软件包的检索信息，删除后会重新加载package目录下的软件包。若不删除会导致一些新加入的软件包不显示。
  删除编译配置文件

  ```rm -f .config```
  在不删除的情况下如果取消选择某些组件它的依赖组件不会自动取消，所以对于需要调整组件的情况下建议删除。

3. 编译
  ```make -j$(nproc) || make -j1 || make -j1 V=s```

##单独编译ipk
1.更新git包、编译内核
  ```git pull
  make target/linux/compile V=s```

2.软件包目录
  **openwrt/package/lean/软件包名***

3.编译ipk
  ```make package/lean/软件包名/compile V=s```
  
  完成路径**openwrt/bin/packages/x86_64/base/软件包名.ipk**
  
4.安装&更新
  软件包-过滤器-查找-移除旧版-文件传输-上传ipk-TTYD终端
  
  ```cd /temp/upload
  ls
  opkg install 软件包名.ipk```
  
  **可以用tab补全**
  
  
