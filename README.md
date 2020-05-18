# OpenWRT 19.07.2 Factory Tar file, generation and installation for Ubiquiti EdgeRouter X
  
  
There was an issue with installing OpenWRT 19.07.2 on the Ubiquiti EdgeRouter X : The new distribution build did not come automaticaly with the usual "openwrt-19.07.2-ramips-mt7621-ubnt-erx-initramfs-factory.tar" file that allow installing OpenWRT on top of EdgeOS to replace it with the router.

Using older versions like "openwrt-18.06-ramips-mt7621-ubnt-erx-initramfs-factory.tar" to reflash the router, and then apply a sysupgrade command with the "openwrt-19.07.2-ramips-mt7621-ubnt-erx-squashfs-sysupgrade.bin" file was not working either, no matter what version of EdgeOS you had factory installed on the router.

The reason why the new built doesn't come with the usual "factory tar file", but with an unusable "bin file" instead is that the kernel image of this new distribution is too big of a few 200 Kb to fit into the kernel image partition. The make file to make the built has an embedded simple security mecanism to prevent generating incorrect tar file if the kernel is too big, it generates an unusable bin file instead.

I have changed a few compilation option so that the make file can finaly generate a custom 19.07 built that fits the correct kernel partition size and therefore generate a usable tar file.

Once the router under the original EdgeOS is updated with this temporary custom OpenWRT built, you can sysupgrade it with the "Official OpenWRT stock" "openwrt-19.07.2-ramips-mt7621-ubnt-erx-squashfs-sysupgrade.bin" so that your router goes with OpenWRT "stock official images" ensuring all the repositories and further sysupgrade of this distribution will work as desired.

The following instructions should allow you to apply these custom compilation settings if you intend to compile your own tar image.  

I also provide a copy of my own operational custom 19.07 factory image tar file for those who are lazy at recompiling it themselves. You will also find my menuconfig ".config" configuration file (named "dot.config" on my repository, you will have to rename it manualy ".config"), so that you can copy/past it into your "/openwrt" directory so that "make menuconfig" becomes unnecessary, and directly compile the target with "make -jXXX" (XXX being the number of core/thread of your processor, to speed up compilation) exactly what options were disabled to reproduce this "short receipt".

---

I will update this post tomorrow with the detailed compilation instructions on Debian/Ubuntu for those hwo are not familiar with compiling their own OpenWRT target images.
