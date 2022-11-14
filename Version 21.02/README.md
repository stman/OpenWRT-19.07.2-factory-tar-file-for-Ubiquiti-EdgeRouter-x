# OpenWRT 21.02 Factory Tar file, generation and installation for Ubiquiti EdgeRouter X

The following procedure is almost the same as the previous one, except that it is more automated, because we provided the **menuconfig.config** file to be loaded into the "make menuconfig" to set all the compilation options correctly before launching the compilation. 

This work is based on the work of **@cyounkins** who improved my settings to compile the **OpenWRT 19.07 version** so that it can also work on **21.02 and 22.03 versions**, a big thank you to him for his help https://github.com/cyounkins/edgerouter-factory . On his git, you can find his **menuconfig.txt** file that is a text file with all the human readable instructions to manually set and unset all the right options to compile correctly this short receipt, while the **menuconfig.config** file I provide, based on his settings, can be directly loaded from **make menuconfig** load option, saving you the 5 minutes necessary to do it manually.

These things where done under a fresh new install of Xubuntu 20.04.2 : 

Open a terminal.

Then type the following commands :

```
sudo apt-get update
sudo apt install build-essential clang flex g++ gawk gcc-multilib gettext git libncurses5-dev libssl-dev python3-distutils rsync unzip zlib1g-dev
git clone https://www.github.com/openwrt/openwrt -b openwrt-21.02
cd openwrt
./scripts/feeds update -a
./scripts/feeds install -a
wget "https://raw.githubusercontent.com/stman/OpenWRT-19.07.2-factory-tar-file-for-Ubiquiti-EdgeRouter-x/master/Version 21.02/menuconfig.config"
make menuconfig
```

Then use the arrow to move selection to **Load** in order to load the EdgeRouter X configuration file for the build OpenWRT-21.02 : Enter the filename **menuconfig.config** then select OK.

Now select the **Save** option and choose **.config** as save filename, click OK, then select Exit.
Select Exit again to get out of the menuconfig utility, we are now ready to compile the whole source code by entering the following commands into the bash shell :


```
make download
make -jXXX
```

For the last command above please replace XXX by the number of processor cores your computer has. In my case, I used the command **make -j8** because I have an 8 cores computer. If you ignore how many cores your microprocessor has, please just type **make** alone.

The compilation should take about 10 minutes with 8 cores.
Once it is finished, enter the following bash commands : 

```
cd ./bin/targets/ramips/mt7621
ls -al
```

And you should see the following files : 

```
ThinkPad-T400:~/openwrt/bin/targets/ramips/mt7621$ ls -al
total 8172
drwxr-xr-x 3 fjouvin fjouvin    4096 nov.  14 11:40 .
drwxr-xr-x 3 fjouvin fjouvin    4096 nov.  14 11:19 ..
-rw-r--r-- 1 fjouvin fjouvin    1888 nov.  14 11:19 config.buildinfo
-rw-r--r-- 1 fjouvin fjouvin     288 nov.  14 11:19 feeds.buildinfo
-rw-r--r-- 1 fjouvin fjouvin 2764800 nov.  14 11:40 openwrt-ramips-mt7621-ubnt_edgerouter-x-initramfs-factory.tar
-rw-r--r-- 1 fjouvin fjouvin 2748428 nov.  14 11:40 openwrt-ramips-mt7621-ubnt_edgerouter-x-initramfs-kernel.bin
-rw-r--r-- 1 fjouvin fjouvin    1317 nov.  14 11:40 openwrt-ramips-mt7621-ubnt_edgerouter-x.manifest
-rw-r--r-- 1 fjouvin fjouvin 2817159 nov.  14 11:40 openwrt-ramips-mt7621-ubnt_edgerouter-x-squashfs-sysupgrade.bin
drwxr-xr-x 2 fjouvin fjouvin    4096 nov.  14 11:40 packages
-rw-r--r-- 1 fjouvin fjouvin     749 nov.  14 11:40 sha256sums
-rw-r--r-- 1 fjouvin fjouvin      18 nov.  14 11:19 version.buildinfo
```

As you can see, there is an **openwrt-ramips-mt7621-ubnt-erx-initramfs-factory.tar** that was generated. Use this file to update your router from EdgeOS with usual procedures.


