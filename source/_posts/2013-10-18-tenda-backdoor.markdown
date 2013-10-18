---
layout: post
title: "Tenda backdoor"
date: 2013-10-18 09:53
comments: true
categories: 
---

So, [devttyS0][1] recently published his find of a backdoor in [certain D-Link routers][2].
Since there are a bunch of cheap chinese routers around here, and Tenda 
routers being especially cheap and popular, I wanted to take a look.

Long story short, [devttyS0 beat me to it and published another awsome backdoor][3]
find. The backdoor is a bit more involved than the D-Link one. Awesome work!

What follow are just some details on other firmware versions I got.

list  of firmwares :
```
-rw-r--r-- 1 ea ea 2854920 Oct 18 09:34 301r_v3.1.192_en.bin
-rw-r--r-- 1 ea ea 2854920 Oct 18 09:34 302r_v3.1.192_en.bin
-rw-r--r-- 1 ea ea 2556279 Oct 18 09:34 3g611r_en_0607.bin.bin
-rw-r--r-- 1 ea ea 2544688 Oct 18 09:34 3gr_H2_V3.3.0y_multi_02.bin
-rw-r--r-- 1 ea ea 2536583 Oct 18 09:34 U_3G611R_H2_V3.3.1e_MULTI_02.bin
-rw-r--r-- 1 ea ea 1733480 Oct 18 09:34 U_W311R_W268R_H3_V3.3.6h_EN_spi.bin
-rw-r--r-- 1 ea ea 2778565 Oct 18 09:34 U_W330R_V3.1.201d_tenda_en.bin
-rw-r--r-- 1 ea ea 2853531 Oct 18 09:34 V3.1.201d_W301R_2010_0709.bin
-rw-r--r-- 1 ea ea 2853531 Oct 18 09:34 V3.1.201d_W302R_2010_0709.bin
-rw-r--r-- 1 ea ea 1710398 Oct 18 09:34 W311r_W268R_H1_V3.3.6b_ost_staticR.bin
-rw-r--r-- 1 ea ea 1710398 Oct 18 09:34 W368r_H1_V3.3.6b_ost_staticR.bin
-rw-r--r-- 1 ea ea 1739357 Oct 18 09:34 w368r_H1_V3.3.6l_EN.bin
-rw-r--r-- 1 ea ea 1733480 Oct 18 09:34 W368R_H3_V3.3.6h_EN_spi.bin
```
Firmwares can easily be extracted with binwalk ( -Me switch to extract everything).

List of httpd binaries in them:
```
./_301r_v3.1.192_en.bin.extracted/_40.extracted/_ramdisk.extracted/squashfs-root/bin/httpd
./_W311r_W268R_H1_V3.3.6b_ost_staticR.bin.extracted/_40.extracted/_29E000.extracted/cpio-root/bin/httpd
./_302r_v3.1.192_en.bin.extracted/_40.extracted/_ramdisk.extracted/squashfs-root/bin/httpd
./_V3.1.201d_W301R_2010_0709.bin.extracted/_40.extracted/_ramdisk.extracted/squashfs-root/bin/httpd
./_W368R_H3_V3.3.6h_EN_spi.bin.extracted/_40.extracted/_2A1000.extracted/cpio-root/bin/httpd
./_V3.1.201d_W302R_2010_0709.bin.extracted/_40.extracted/_ramdisk.extracted/squashfs-root/bin/httpd
./_3gr_H2_V3.3.0y_multi_02.bin.extracted/_40.extracted/_401000.extracted/cpio-root/bin/httpd
./_w368r_H1_V3.3.6l_EN.bin.extracted/_40.extracted/_298000.extracted/cpio-root/bin/httpd
./_U_W330R_V3.1.201d_tenda_en.bin.extracted/_40.extracted/_ramdisk.extracted/squashfs-root/bin/httpd
./_W368r_H1_V3.3.6b_ost_staticR.bin.extracted/_40.extracted/_29E000.extracted/cpio-root/bin/httpd
./_3g611r_en_0607.bin.bin.extracted/_40.extracted/_42F000.extracted/cpio-root/bin/httpd
./_U_W311R_W268R_H3_V3.3.6h_EN_spi.bin.extracted/_40.extracted/_2A1000.extracted/cpio-root/bin/httpd
./_U_3G611R_H2_V3.3.1e_MULTI_02.bin.extracted/_40.extracted/_3F9000.extracted/cpio-root/bin/httpd
```

Their coresponding md5 sums:
```
30a4a545068d99d1427556bd6612bf98  ./_301r_v3.1.192_en.bin.extracted/_40.extracted/_ramdisk.extracted/squashfs-root/bin/httpd
d1e45b3ad126ba5ba2f7f359b8277438  ./_W311r_W268R_H1_V3.3.6b_ost_staticR.bin.extracted/_40.extracted/_29E000.extracted/cpio-root/bin/httpd
30a4a545068d99d1427556bd6612bf98  ./_302r_v3.1.192_en.bin.extracted/_40.extracted/_ramdisk.extracted/squashfs-root/bin/httpd
bf4d17ad1188fa7bdd12f99b3b294567  ./_V3.1.201d_W301R_2010_0709.bin.extracted/_40.extracted/_ramdisk.extracted/squashfs-root/bin/httpd
f0cd57047f6c329e59102ce4741481d8  ./_W368R_H3_V3.3.6h_EN_spi.bin.extracted/_40.extracted/_2A1000.extracted/cpio-root/bin/httpd
bf4d17ad1188fa7bdd12f99b3b294567  ./_V3.1.201d_W302R_2010_0709.bin.extracted/_40.extracted/_ramdisk.extracted/squashfs-root/bin/httpd
3f578d8b7cf8565baa239454c9713818  ./_3gr_H2_V3.3.0y_multi_02.bin.extracted/_40.extracted/_401000.extracted/cpio-root/bin/httpd
bce9466a9bb018bd32a9dacb3a8c0487  ./_w368r_H1_V3.3.6l_EN.bin.extracted/_40.extracted/_298000.extracted/cpio-root/bin/httpd
890a83d5aee333c40b8f0032169e3a4e  ./_U_W330R_V3.1.201d_tenda_en.bin.extracted/_40.extracted/_ramdisk.extracted/squashfs-root/bin/httpd
d1e45b3ad126ba5ba2f7f359b8277438  ./_W368r_H1_V3.3.6b_ost_staticR.bin.extracted/_40.extracted/_29E000.extracted/cpio-root/bin/httpd
a22f85d995ce9c3383632b6104c5be8e  ./_3g611r_en_0607.bin.bin.extracted/_40.extracted/_42F000.extracted/cpio-root/bin/httpd
f0cd57047f6c329e59102ce4741481d8  ./_U_W311R_W268R_H3_V3.3.6h_EN_spi.bin.extracted/_40.extracted/_2A1000.extracted/cpio-root/bin/httpd
5bd72353206168fd340b293b2ddd264a  ./_U_3G611R_H2_V3.3.1e_MULTI_02.bin.extracted/_40.extracted/_3F9000.extracted/cpio-root/bin/httpd
```
There are 8 different binaries in total.

And httpd binaries  that contain the ofending string "w302r_mfg"
```
./_301r_v3.1.192_en.bin.extracted/_40.extracted/_ramdisk.extracted/squashfs-root/bin/httpd
./_W311r_W268R_H1_V3.3.6b_ost_staticR.bin.extracted/_40.extracted/_29E000.extracted/cpio-root/bin/httpd
./_302r_v3.1.192_en.bin.extracted/_40.extracted/_ramdisk.extracted/squashfs-root/bin/httpd
./_V3.1.201d_W301R_2010_0709.bin.extracted/_40.extracted/_ramdisk.extracted/squashfs-root/bin/httpd
./_W368R_H3_V3.3.6h_EN_spi.bin.extracted/_40.extracted/_2A1000.extracted/cpio-root/bin/httpd
./_V3.1.201d_W302R_2010_0709.bin.extracted/_40.extracted/_ramdisk.extracted/squashfs-root/bin/httpd
./_3gr_H2_V3.3.0y_multi_02.bin.extracted/_40.extracted/_401000.extracted/cpio-root/bin/httpd
./_w368r_H1_V3.3.6l_EN.bin.extracted/_40.extracted/_298000.extracted/cpio-root/bin/httpd
./_U_W330R_V3.1.201d_tenda_en.bin.extracted/_40.extracted/_ramdisk.extracted/squashfs-root/bin/httpd
./_W368r_H1_V3.3.6b_ost_staticR.bin.extracted/_40.extracted/_29E000.extracted/cpio-root/bin/httpd
./_3g611r_en_0607.bin.bin.extracted/_40.extracted/_42F000.extracted/cpio-root/bin/httpd
./_U_W311R_W268R_H3_V3.3.6h_EN_spi.bin.extracted/_40.extracted/_2A1000.extracted/cpio-root/bin/httpd
./_U_3G611R_H2_V3.3.1e_MULTI_02.bin.extracted/_40.extracted/_3F9000.extracted/cpio-root/bin/httpd
```

So all of them contain the offending string, you'd need to test if the backdoor is actually reachable.
For more details, see [devttyS0's][1] [From China with love][3] post.

Update:

Grabbed some more firmwares from tenda and these also have the ofending string in their httpd:

```
2b8e52eeeebcf2ca1d8aea49e0decc3e  ./_US_W268RRA__H3_V3.3.6h_EN_SPI.bin.extracted/_40.extracted/_2A1000.extracted/cpio-root/bin/httpd
2b8e52eeeebcf2ca1d8aea49e0decc3e  ./_US_W311RRA__H3_V3.3.6h_EN_SPI.bin.extracted/_40.extracted/_2A1000.extracted/cpio-root/bin/httpd
38dbefea2e2072794fdee48d0454ea81  ./_w1500a_kfw_V1.0.1.22_en_svn6227.bin.extracted/squashfs-root/bin/httpd
3a5c7ca273dbc9abc2fa2f01872c6ae9  ./_U150M_V3.32.12_EN.bin.extracted/_40.extracted/_2AD000.extracted/cpio-root/bin/httpd
3a5c7ca273dbc9abc2fa2f01872c6ae9  ./_U300M_V3.32.12_EN.bin.extracted/_40.extracted/_2AD000.extracted/cpio-root/bin/httpd
3da70dbcb25267981799cd4e80d45a66  ./_U_W302RRA_V3.1.201d_EN.bin.extracted/_40.extracted/_ramdisk.extracted/squashfs-root/bin/httpd
4dd02694228931fa29d0ab24c7cf5244  ./_US_N60BRV1_N60_V1.0.0.15_EN.bin.extracted/squashfs-root/bin/httpd
545c52d9a6ab82e38925f7ba53655738  ./_US_N6BRV1_N6_V2.0.0.2_EN.bin.extracted/squashfs-root/bin/httpd
55429ee07ee7e12463cfb31bdde3545c  ./_U_W150M_EN_V3.33.13_SPI_EN.bin.extracted/_40.extracted/_2A2000.extracted/cpio-root/bin/httpd
55429ee07ee7e12463cfb31bdde3545c  ./_U_W300M_EN_V3.33.13_SPI_EN.bin.extracted/_40.extracted/_2A2000.extracted/cpio-root/bin/httpd
6940fb6098d7248430d307ad9e0f41e3  ./_U_W330R_V3.1.201f_en_onWISP.bin.extracted/_40.extracted/_ramdisk.extracted/squashfs-root/bin/httpd
7bfc5d1aa54322e2a6a3d30dc1b071a1  ./_W330R_V3.1.201d_EN.bin.extracted/_40.extracted/_ramdisk.extracted/squashfs-root/bin/httpd
8fdd1acfb877e75254db8bf0e4d4b9ce  ./_W311R_H1_V3.3.5o.bin.extracted/_40.extracted/_29E000.extracted/cpio-root/bin/httpd
9618f60bb71ff28670c6447a4972205c  ./_w311r_H1_V3.3.5n_en.bin.extracted/_40.extracted/_29E000.extracted/cpio-root/bin/httpd
9e2403f54a5de6de68217f0f55606563  ./_US_N60BRV1_N60_V1.0.0.16_EN.bin.extracted/squashfs-root/bin/httpd
b663cb3e1e9b09bdd33116d9fade6866  ./_US_N80_W568Rbr_V1.0.1.8(4428)_en_TD.bin.extracted/_piggy.extracted/_3D8000.extracted/cpio-root/bin/httpd
d1e45b3ad126ba5ba2f7f359b8277438  ./_W311r_H1_V3.3.6b.bin.extracted/_40.extracted/_29E000.extracted/cpio-root/bin/httpd
d9eb8d5d79a3f4e9f4e5ded2887c39c4  ./_U268R_H1_V3.3.6d_EN.bin.extracted/_40.extracted/_2AD000.extracted/cpio-root/bin/httpd
d9eb8d5d79a3f4e9f4e5ded2887c39c4  ./_U311R_H1_V3.3.6d_EN.bin.extracted/_40.extracted/_2AD000.extracted/cpio-root/bin/httpd
fe34ecc4b90e0fc9bd3d50e216f80b04  ./_U150M_RT_EN_V3.32.11.bin.extracted/_40.extracted/_2AD000.extracted/cpio-root/bin/httpd
fe34ecc4b90e0fc9bd3d50e216f80b04  ./_U300M_RT_EN_V3.32.11.bin.extracted/_40.extracted/_2AD000.extracted/cpio-root/bin/httpd
```

Keep on rocking in the free world,

ea

[1]: https://twitter.com/devttyS0/
[2]: http://www.devttys0.com/2013/10/reverse-engineering-a-d-link-backdoor/
[3]: http://www.devttys0.com/2013/10/from-china-with-love/