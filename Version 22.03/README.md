# OpenWRT 22.03 Factory Tar file, generation and installation for Ubiquiti EdgeRouter X

The following procedure is almost the same as the previous one, except that it is more automated, because we provided the **menuconfig.config** file to be loaded into the "make menuconfig" to set all the compilation options correctly before launching the compilation. 

This work is based on the work of **@cyounkins** who improved my settings to compile the **OpenWRT 19.07 version** so that it can also work on **21.02 and 22.03 versions**, a big thank you to him for his help https://github.com/cyounkins/edgerouter-factory . On his git, you can find his **menuconfig.txt** file that is a text file with all the human readable instructions to manually set and unset all the right options to compile correctly this short receipt, while the **menuconfig.config** file I provide, based on his settings, can be directly loaded from **make menuconfig** load option, saving you the 5 minutes necessary to do it manually.

These things where done under a fresh new install of Xubuntu 20.04.2 : 

Open a terminal.

Then type the following commands :

```
sudo apt-get update
sudo apt install build-essential clang flex g++ gawk gcc-multilib gettext git libncurses5-dev libssl-dev python3-distutils rsync unzip zlib1g-dev
git clone https://www.github.com/openwrt/openwrt -b openwrt-22.03
cd openwrt
./scripts/feeds update -a
./scripts/feeds install -a
make defconfig
wget "https://raw.githubusercontent.com/stman/OpenWRT-19.07.2-factory-tar-file-for-Ubiquiti-EdgeRouter-x/master/Version 22.03/menuconfig.config"
make menuconfig
```

Then use the arrow to move selection to **Load** in order to load the EdgeRouter X configuration file for the build OpenWRT-22.03 : Enter the filename **menuconfig.config** then select OK.

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
total 8908
drwxr-xr-x 3 fjouvin fjouvin    4096 nov.  19 18:00 .
drwxr-xr-x 3 fjouvin fjouvin    4096 nov.  19 15:47 ..
-rw-r--r-- 1 fjouvin fjouvin    1983 nov.  19 17:47 config.buildinfo
-rw-r--r-- 1 fjouvin fjouvin     288 nov.  19 17:47 feeds.buildinfo
-rw-r--r-- 1 fjouvin fjouvin 3010560 nov.  19 17:59 openwrt-ramips-mt7621-ubnt_edgerouter-x-initramfs-factory.tar
-rw-r--r-- 1 fjouvin fjouvin 3002053 nov.  19 17:59 openwrt-ramips-mt7621-ubnt_edgerouter-x-initramfs-kernel.bin
-rw-r--r-- 1 fjouvin fjouvin    1358 nov.  19 17:59 openwrt-ramips-mt7621-ubnt_edgerouter-x.manifest
-rw-r--r-- 1 fjouvin fjouvin 3073159 nov.  19 17:59 openwrt-ramips-mt7621-ubnt_edgerouter-x-squashfs-sysupgrade.bin
drwxr-xr-x 2 fjouvin fjouvin    4096 nov.  19 17:59 packages
-rw-r--r-- 1 fjouvin fjouvin     749 nov.  19 18:00 sha256sums
-rw-r--r-- 1 fjouvin fjouvin      18 nov.  19 17:47 version.buildinfo

```

As you can see, there is an **openwrt-ramips-mt7621-ubnt-erx-initramfs-factory.tar** that was generated. Use this file to update your router from EdgeOS with usual procedures.


