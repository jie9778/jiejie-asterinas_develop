# asterinas基于大赛测试样例的改造

Asterinas 操作系统已经支持 LoongArch64 架构。

Asterinas 项目在 2025年发布的 v0.16.0 版本中，明确将新增对 LoongArch 架构的支持列为其核心功能之一。---- ai得出，最后应该进行测试

 

https://github.com/oscomp/asterinas/blob/main/Makefile  ---- 这是asterinas的源makefiles


https://github.com/oscomp/testsuits-for-oskernel/tree/pre-2025 ----这是大赛官方给予的测试项目
	共12个包括basic、busybox、lua、libctest、iozone、unixbench、iperf、libcbench、lmbench、netperf、cyclictest和LTP共12组测试题目
	

https://github.com/oscomp/oskernel-testsuits-cooperation/tree/master ----对上述每个测试题目，需给出题目描述、编译方法、样例输出、评分依据四个部分的测例题目说明信息



// Asterinas 是一个微内核或简化内核，它只包含：
- 进程管理
- 内存管理  
- 文件系统
- 设备驱动
- 系统调用
// 但不包含：
- 编译器 (gcc, rustc)
- 构建工具 (make, cmake)
- 包管理器 (apt, yum)
- 完整的 Shell 工具集
<img width="500" height="700" alt="deepseek_mermaid_20251127_52e095" src="https://github.com/user-attachments/assets/938d2f55-b878-4dae-996f-c860b75f6602" />



当在 Asterinas 项目中执行 make run 时，自动进入了 QEMU 虚拟环境，不需要手动下载或启动 QEMU。

在 Ubuntu/Docker 中使用 make 编译内核和测试程序
在 Ubuntu/Docker 中启动 QEMU 来运行测试
Asterinas 内核应该自动运行测试，无需用户交互



根据源makefile和大赛提供的测试题的makefile进行修改：使用linux的make

## 步骤一：克隆测试套件

在asterinas同级目录
	
/path/to/your/workspace/

├── asterinas/                    # 你的内核项目

└── testsuits-for-oskernel/       # 测试套件仓库

## 步骤2：改造 Asterinas 的 Makefile

	等待改造----


## 步骤3：进行测试

bash

在 asterinas 目录中
	
make all      # 编译内核+测试程序

make run      # 启动测试（内核自动执行测试）

