# 前置条件：

## 克隆官方测试集仓库，按照编译方法，得到执行文件sdcard

https://github.com/oscomp/testsuits-for-oskernel/tree/pre-2025  -- oscomp测试仓库



# 第一步：删除旧的 mnt(若之前无挂载无需进行--跳转到第二部步）
由于之前这个目录非空，rmdir 删不掉。我们需要用 rm -rf 强制删除。

请在 ~/Workspace/asterinas 目录下执行：

Bash

## 1. 为了安全，先确保它没有被挂载
sudo umount mnt 2>/dev/null

## 2. 强制删除该目录及其内容
rm -rf mnt

## 验证是否删除
ls -d mnt

如果提示 "无法访问...: 没有那个文件或目录"，说明删除成功

# 第二步：重构（替换）自带的 ext2.img
我们不去调整旧文件的大小（那样很麻烦），而是直接在原位置创建一个同名的新文件覆盖它。

## 1. 进入构建目录

Bash

cd ~/Workspace/asterinas/test/build
## 2. 备份旧的镜像（好习惯）

Bash

mv ext2.img ext2.img.bak
## 3. 创建新的 4GB 空镜像 (命名为 ext2.img) 注意：这里我们直接用 ext2.img 这个名字，这样 Asterinas 就会直接识别它。

Bash

dd if=/dev/zero of=ext2.img bs=1M count=4096 status=progress
## 4. 格式化为 ext2

Bash

mkfs.ext2 ext2.img

# 第三步：装入 3.2GB 测试数据
现在我们有了 4GB 的 ext2.img，我们需要把数据拷进去。

## 1. 在当前目录创建临时挂载点

Bash

mkdir mnt
## 2. 挂载新的 ext2.img

Bash

sudo mount ext2.img mnt
## 3. 拷贝数据 (使用绝对路径)

Bash

echo "正在拷贝 3.2GB 数据，请耐心等待..."
sudo cp -r /home/jie/Workspace/testsuits-for-oskernel/sdcard/riscv/* mnt/
## 4. 检查拷贝结果

Bash

ls -lh mnt/

应该能看到 glibc 和 musl
 
## 5. 卸载并清理

Bash

sudo umount mnt

rmdir mnt

# 第四步：启动验证
现在，test/build/ 下的 ext2.img 已经是包含完整测试集的 4GB 镜像了。

## 1. 回到项目根目录

Bash

cd ~/Workspace/asterinas
## 2. 还原 Makefile (如果您之前改过) 如果您之前修改了 Makefile 里的 EXT2_IMG 路径，请把它改回来，或者直接删除您修改的那一行（通常默认就是指向 test/build/ext2.img 的）。 如果您之前没保存或者已经撤销了修改，直接进行下一步。

## 3. 运行

Bash

make run OSDK_TARGET_ARCH=rsicv64
## 4. 内核中验证 进入 Asterinas Shell 后：

Bash

ls /ext2
预期：看到 glibc 和 musl
这样就完美实现了“原生重构”，既不需要额外的镜像文件，也不需要改配置文件！
