---
title: "Mi6: fastboot flash 无响应时刷入 recovery"
excerpt: "当 fastboot flash recovery 无响应时，修改官方线刷包替换 recovery，通过 EDL 模式刷入所需 recovery。"
date: 2020-07-15
tag: [android]
---

五月的时候入手了 iPhone SE2 支持了一波单镜头小屏手机，嗯，辣鸡浴霸是单反不是手机。入手 SE2 之后，手头的 Mi6 在迁移完数据后终于可以考虑下系统升级。原本的 LineageOS 15 升 16 需要进 `recovery`，然而懒得备份数据的我一直没进行升级。

等到准备更新 `recovery` 时，发现 `fastboot flash recovery` 无响应。试了下其他 `fastboot` 指令，`fastboot devices` 可用，而其他大部分指令同样不响应。问了下 @ntzyz，他的 mix2 也出现过类似问题但是无解。经过搜索，发现网上有类似问题的讨论但却并没什么新进展。既然 `fastboot` 下无法刷入，那就只能换一套刷机途径。高通芯片手机有一个 emergency download (EDL) 模式可以用于刷机，于是我有了一个想法：替换官方线刷包的 recovery，然后通过 EDL 模式刷入。具体步骤如下：

1. 下载并解压官方线刷包。
1. 使用所需 `recovery` 文件替换 `./images/recovery.img` 文件。
1. 计算新 `recovery` 文件的 md5 值并修改 `./md5sum.xml` 文件，diff 如下：

	```
	diff --git a/md5sum.xml b/md5sum.xml
	index aea389d..0871a53 100644
	--- a/md5sum.xml
	+++ b/md5sum.xml
	@@ -13,7 +13,7 @@
	    <digest hash="md5" name="gpt_both3.bin">03b39b71267428b46c11f5052385d482</digest>
	    <digest hash="md5" name="cmnlib64.mbn">fb7f517fb46757282f409dc2a8a0719b</digest>
	    <digest hash="md5" name="rawprogram4.xml">f686c986be3b3ac2dec62b204c1109c3</digest>
	-    <digest hash="md5" name="recovery.img">2e169a483b7c35d9c2056ae9c7e61469</digest>
	+    <digest hash="md5" name="recovery.img">1b92e393eabc1a4d15ae228d2260f7d8</digest>
	    <digest hash="md5" name="gpt_empty3.bin">7b062e4b9562bb74ea8fe445676b3a92</digest>
	    <digest hash="md5" name="gpt_main1.bin">93f728416333b007246455224b8bfbf0</digest>
	    <digest hash="md5" name="rpm.mbn">f2d13f89d1041adcd84cb7678ad69ba4</digest>
	```

1. 通过 `adb reboot edl` 或者 `fastboot oem edl` 进入 EDL 模式。
1. 使用官方刷机工具刷入替换后的刷机包，此时设备编码显示为以 "COM" 开头的代码。
1. 完成刷入后直接重启进入新的 `recovery` 并清除数据。