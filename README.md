# OpenWRT 19.07.2 Factory Tar file, generation and installation for Ubiquiti EdgeRouter X
  
  
There was an issue with installing OpenWRT 19.07.2 on the Ubiquiti EdgeRouter X : The new distribution build did not come automatically with the usual **openwrt-19.07.2-ramips-mt7621-ubnt-erx-initramfs-factory.tar** file that allows installing OpenWRT on top of EdgeOS to replace it by OpenWRT within the router.

Using older versions like **openwrt-18.06-ramips-mt7621-ubnt-erx-initramfs-factory.tar** to reflash the router, and then apply a sysupgrade command with the **openwrt-19.07.2-ramips-mt7621-ubnt-erx-squashfs-sysupgrade.bin** file was not working either, no matter what version of EdgeOS you had factory installed on the router.

The reason why the new built doesn't come with the usual **factory tar file**, but with an unusable **bin file** instead is that the kernel image of this new distribution is too big by a few 200 Kb to fit into the kernel image partition. The make file to make the built has an embedded simple security mechanism to prevent generating incorrect tar file if the kernel is too big, it generates an unusable bin file instead.

I have changed a few compilation options so that the make file can finaly generate a custom 19.07 built that fits the correct kernel partition size and therefore generate a usable tar file.

Once the router under the original EdgeOS is updated with this temporary custom OpenWRT built, you can sysupgrade it with the "Official OpenWRT stock" **openwrt-19.07.2-ramips-mt7621-ubnt-erx-squashfs-sysupgrade.bin** so that your router works with OpenWRT "stock official images" ensuring all the repositories and further sysupgrade of this distribution will work as desired.

The following instructions should allow you to apply these custom compilation settings if you intend to compile your own tar image.  

I also provide a copy of my own operational custom 19.07 factory image tar file for those who are lazy at recompiling it themselves. You will also find my menuconfig **.config** configuration file (Named **dot.config** on my repository, you will have to rename it manualy **.config**), so that you can copy/past it into your **/openwrt** directory so that **make menuconfig** becomes unnecessary, and directly compile the target with **make -jXXX** (XXX being the number of core/thread of your processor, to speed up compilation) exactly what options were disabled to reproduce this "short receipt".

---

Here is the detailed procedure to compile an equivalent operational "short receipt" Tar factory image as I have been doing in my previous post.

These things were done under Xubuntu 18.04.2 : 

Open a terminal.

Then type the following commands (One of them related to feeds install is doubled because some errors happen on the first launch):

```
sudo apt-get update
sudo apt-get install subversion g++ zlib1g-dev build-essential git python python3 python3-distutils libncurses5-dev gawk gettext unzip file libssl-dev wget libelf-dev ecj fastjar java-propose-classpath libldap2-dev libcap-dev libidn2-dev liblzma-dev libjansson-dev libpam-dev rsync
git clone https://www.github.com/openwrt/openwrt -b openwrt-19.07
cd openwrt
./scripts/feeds update -a
./scripts/feeds install -a
./scripts/feeds install -a
make menuconfig
```

Now, from the menuconfig main screen,

- Set **Target System** to : **MediaTek Ralink MIPS**
- Set **Subtarget** to : **MT7621 based board**
- Set **Target Profile** to : **Ubiquiti EdgeRouter X**

Then select **Save** and accept to save the configuration under the proposed default filename **.config** and then select **Exit** until you quit the menuconfig utility and are back to the terminal bash prompt.

From here, enter the following bash commands : 

```
make defconfig
make menuconfig
```

Go into **Global Build Options** submenu, and then : 

- Set **Strip unnecessary export from the kernel image** to *
- Set **Strip unnecessary functions from libraries** to *
 
From this screen, enter the submenu **Kernel build options** and then : 

- Unset **Compile the kernel with debug filesystem enabled**
- Unset **Compile the kernel with symbol table information**
- Unset **Compile the kernel with debug information**

Then select **Exit** until you back reach the menuconfig main screen, then select **Save** , it will propose you to save the configuration under the default filename **.config** , accept it, and then select **Exit** until you are back to the terminal bash prompt.

Back to the terminal bash prompt, enter the following commands : 

```
make download
make -jXXX
```

For the last command above please replace XXX by the number of processor cores your computer has. In my case, I used the command **make -j8** because I have an 8 cores computer. If you wish tp ignore how many cores your microprocessor has, please just type **make** alone.

The compilation should take about 10 minutes with 8 cores.
Once it is finished, enter the following bash commands : 

```
cd ./bin/targets/ramips/mt7621
ls -al
```

And you should see the following files : 

```
user@PC:~/openwrt/bin/targets/ramips/mt7621$ ls -al
total 8684
drwxr-xr-x 3 user user    4096 mai   18 14:01 .
drwxr-xr-x 3 user user    4096 mai   18 13:55 ..
-rw-r--r-- 1 user user     335 mai   18 13:55 config.buildinfo
-rw-r--r-- 1 user user     263 mai   18 13:55 feeds.buildinfo
-rw-r--r-- 1 user user    1880 mai   18 14:01 openwrt-ramips-mt7621-device-ubnt-erx.manifest
-rw-r--r-- 1 user user 2938880 mai   18 14:01 openwrt-ramips-mt7621-ubnt-erx-initramfs-factory.tar
-rw-r--r-- 1 user user 2923299 mai   18 14:01 openwrt-ramips-mt7621-ubnt-erx-initramfs-kernel.bin
-rw-r--r-- 1 user user 2990868 mai   18 14:01 openwrt-ramips-mt7621-ubnt-erx-squashfs-sysupgrade.bin
drwxr-xr-x 2 user user    4096 mai   18 14:01 packages
-rw-r--r-- 1 user user     720 mai   18 14:01 sha256sums
-rw-r--r-- 1 user user      18 mai   18 13:55 version.buildinfo
```

As you can see, there is an **openwrt-ramips-mt7621-ubnt-erx-initramfs-factory.tar** that was generated. Use this file to update your router from EdgeOS with usual procedures.

Then, update again the router under this temporary OpenWRT 19.07 built with the official stock sysupgrade 19.07.2 bin file from the OpenWRT website, and you're done. 

# OpenWRT 21.02 and 22.03 Factory Tar file, generation and installation for Ubiquiti EdgeRouter X

The following procedure is almost the same as the previous one, except that it is more automated, because we provided the **menuconfig.config** file to be loaded into the "make menuconfig" to set all the compilation options correctly before launching the compilation. 

This work is based on the work of **@cyounkins** who improved my settings to compile the **OpenWRT 19.07 version** so that it can also work on **21.02 and 22.03 versions**, a big thank you to him for his help https://github.com/cyounkins/edgerouter-factory . On his git, you can find his **menuconfig.txt** file that is a text file with all the human readable instructions to manually set and unset all the right options to compile correctly this short receipt, while the **menuconfig.config** file I provide, based on his settings, can be directly loaded from **make menuconfig** load option, saving you the 5 minutes necessary to do it manually.

These things where done under a fresh new install of Xubuntu 20.04.2 : 

Open a terminal.

Then type the following commands (Change "-b openwrt-21.02" to "-b openwrt-22.03" to compile the 22.03 version):

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

