#  官方评测系统是如何工作的

操作系统大赛的评测平台（GitLab CI）其实非常“死板”，它的工作流程如下：

拉取代码：CI 服务器把你的仓库 git clone 下来。

准备环境：启动一个 Docker 容器（里面有 QEMU 和 GCC，还有官方生成的 sdcard.img）。

编译内核：执行你在根目录写的 make all。

启动评测：执行你在根目录写的 make run。

搜集日志：CI 脚本会监控 QEMU 的串口输出（Serial Output）。

正则评分：脚本一行行读日志，如果读到了特定的字符串（例如 test_name PASS 或 [success]），就给你加分。

重点来了：
评测系统并不关心你的内核是从哪里读取的测试程序。它不管你是从 SD 卡读的（标准做法）。还是从网络下载的。还是直接写死在内存里的（Initramfs 做法）。它只关心一件事：你的 make run 启动后，屏幕上有没有打印出它想要的测试结果。

2. 标准做法 vs. 准备做法
		
| 特性 | 标准做法 (Hard Mode) | 准备做法 (Initramfs / Smart Mode) |
|-------|-------|-------|
| 测试程序位置 | 放在官方提供的 sdcard.img 里 (FAT32格式) | 打包在你生成的 initramfs.cpio 里 |
| 启动方式 | 	QEMU 挂载磁盘：-drive file=sdcard.img... | 包在你生成的 initramfs.cpio 里 |
| 内核要求 | 	高：必须实现 VirtIO-Blk 驱动 + FAT32 文件系统 | 低：只要能解压 CPIO (Asterinas 自带功能) |
| 风险 | 	驱动写不好容易死机、读不到文件 | 极其稳定，几乎不出错 |
| 评测结果| 屏幕打印 test pass -> 得分 | 屏幕打印 test pass -> 得分 |

结论：
官方虽然假设大家都会去写 FAT32 驱动并挂载 SD 卡，但只要你在 make run 里把自己的 initramfs 喂给 QEMU，评测系统完全分辨不出来区别。这就是为什么说 Initramfs 是初期拿分的“神技”。




