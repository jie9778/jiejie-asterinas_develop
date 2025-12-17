# Asterinas Kernel - Linux Native AIO Implementation

![Asterinas](https://img.shields.io/badge/OS-Asterinas-orange)
![Language](https://img.shields.io/badge/Language-Rust-red)
![OSCOMP](https://img.shields.io/badge/OSCOMP-2025-blue)
![Status](https://img.shields.io/badge/Status-In_Progress-green)

## 📖 项目简介

本项目是 **OSCOMP (全国大学生计算机系统能力大赛 - 操作系统设计赛)** 的参赛作品。

我们的核心目标是为 **Asterinas** 操作系统内核实现 **Linux Native AIO (Legacy AIO)** 支持。具体工作包括实现 `io_setup`、`io_submit`、`io_getevents` 等关键系统调用，填补 Asterinas 在异步 I/O 领域的空白，使其能够支持 MySQL、Oracle 等依赖原生 AIO 的高性能应用。

---

## 🚀 为什么选择 Asterinas？

在众多的操作系统内核项目中，我们选择 Asterinas 作为开发平台，主要基于其独特的架构优势和技术愿景：

### 1. 独创的 Framekernel (框架内核) 架构
Asterinas 并没有止步于“用 Rust 重写 Linux”，而是提出了一种全新的 **Framekernel** 架构：
* **极致的安全与性能平衡**：它结合了宏内核（Monolithic）的高性能（单一地址空间）和微内核（Microkernel）的高安全性。
* **TCB 最小化**：通过将内核分为 **OS Framework (OSTD)** 和 **OS Services**。内存安全风险被严格限制在极小的 Framework 层，而庞大的 OS Services (如我们实现的 AIO) 强制使用 **Safe Rust** 编写。这从架构层面从根本上消除了内存安全漏洞。

### 2. Rust 语言的赋能
Rust 的所有权机制和类型系统让我们在编写复杂的并发内核代码时，能够在这个编译阶段捕获数据竞争和内存错误。这对于操作系统这种对稳定性要求极高的底层软件至关重要。

### 3. Linux 二进制兼容性
Asterinas 致力于实现 Linux ABI 兼容，这意味着现有的 Linux 应用无需重新编译即可运行。这种实用主义的路线让我们看到了它未来真正落地的可能性。

---

## 🛠️ 我们的工作 (Our Contribution)

我们的工作主要集中在 `os-services` 层，负责实现以下 5 个核心的 AIO 系统调用，并构建底层的异步处理机制。

### 涉及的系统调用
我们实现了 Linux Native AIO 的完整生命周期管理：

| Syscall ID | 名称 | 功能描述 | 实现状态 |
| :--- | :--- | :--- | :--- |
| **206** | `io_setup` | 初始化 AIO 上下文，建立用户态与内核态共享的 Ring Buffer | ✅ 完成 / 🚧 进行中 |
| **207** | `io_destroy` | 销毁 AIO 上下文，回收资源并取消未完成的请求 | ✅ 完成 / 🚧 进行中 |
| **208** | `io_getevents`| 从 Ring Buffer 获取已完成的 I/O 事件（支持超时与阻塞等待） | ✅ 完成 / 🚧 进行中 |
| **209** | `io_submit` | 提交异步 I/O 请求（非阻塞），将任务分发至后台执行 | ✅ 完成 / 🚧 进行中 |
| **210** | `io_cancel` | 取消正在排队或执行的 I/O 请求 | ✅ 完成 / 🚧 进行中 |

### 核心技术点
1.  **AIO Context 管理**：设计并实现了 `AioContext` 结构，用于管理每个 AIO 实例的状态、任务队列和资源。
2.  **共享内存 Ring Buffer**：实现了符合 Linux ABI 标准的环形缓冲区，处理内核写、用户读的并发同步问题，确保内存序（Memory Ordering）正确。
3.  **异步执行模型**：利用 Asterinas 的任务机制，将同步的 I/O 操作转换为异步执行，确保 `io_submit` 调用能立即返回，不阻塞用户线程。
4.  **Safe Rust 封装**：严格遵循 Framekernel 原则，在 Safe Rust 中处理复杂的底层逻辑，仅在必要时通过 OSTD 提供的接口与硬件交互。

---

## 💡 项目意义与作用

### 1. 完善 Linux 兼容性拼图
Linux Native AIO 是许多高性能中间件（尤其是数据库系统，如 MySQL InnoDB 引擎）的刚需。没有这组系统调用，这些关键应用在 Asterinas 上将无法启动或被迫回退到低效的同步 I/O 模式。我们的工作填补了这一关键缺失。

### 2. 提升系统 I/O 吞吐量
通过实现真正的异步 I/O，允许用户程序在等待磁盘操作完成的同时继续执行计算任务，显著提升了 CPU 利用率和系统的整体 I/O 吞吐能力。

### 3. 验证 Framekernel 的表达能力
AIO 是内核中较为复杂的子系统，涉及内存共享、并发控制和调度。我们的实现证明了 Asterinas 的 **OS Services (Safe Rust)** 层具备足够的表达能力来构建高性能、复杂的内核机制，验证了 Framekernel 架构的可行性。

---

## 📂 目录结构说明
