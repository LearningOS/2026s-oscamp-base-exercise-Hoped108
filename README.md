# OS Camp - Rust 与操作系统进阶实验

这是一个仿照 [rustlings](https://github.com/rust-lang/rustlings) 风格的 Rust 进阶与操作系统入门练习仓库。
你将通过补全代码并通过测试，学习 Rust 并发编程、异步编程、`no_std` 开发，以及操作系统核心概念。

## 环境要求

- Rust 工具链（stable，>= 1.75）
- Linux 环境：大多数练习面向 x86_64；**模块 4（上下文切换）仅支持 riscv64**，需要 riscv64 环境或 QEMU 用户态模拟

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

## 练习结构

总共 **6 个模块，23 道练习**，难度由浅入深：

### 模块 1：并发（同步）— `01_concurrency_sync/`

| # | 练习 | 涉及概念 |
|---|----------|----------|
| 1 | `01_thread_spawn` | `thread::spawn`、`move` 闭包、`join` |
| 2 | `02_mutex_counter` | `Arc<Mutex<T>>`、共享状态并发 |
| 3 | `03_channel` | `mpsc::channel`、多生产者模式 |
| 4 | `04_process_pipe` | `Command`、`Stdio::piped()`、进程管道 |

### 模块 2：`no_std` 开发 — `02_no_std_dev/`

| # | 练习 | 涉及概念 |
|---|----------|----------|
| 1 | `01_mem_primitives` | `no_std` 内存原语：memcpy、memset、memmove、strlen、strcmp |
| 2 | `02_bump_allocator` | `GlobalAlloc` trait、Bump 分配器、基于 CAS 的线程安全 |
| 3 | `03_free_list_allocator` | 空闲链表分配器、侵入式链表、首次适配策略 |
| 4 | `04_syscall_wrapper` | 跨架构 syscall ABI（x86_64/aarch64/riscv64）、内联汇编 |
| 5 | `05_fd_table` | 文件描述符表、`Arc<dyn File>`、fd 复用策略 |

### 模块 3：操作系统并发进阶 — `03_os_concurrency/`

| # | 练习 | 涉及概念 |
|---|----------|----------|
| 1 | `01_atomic_counter` | `AtomicU64`、`fetch_add`、CAS 循环 |
| 2 | `02_atomic_ordering` | 内存序、Release-Acquire、`OnceCell` |
| 3 | `03_spinlock` | 自旋锁实现、`compare_exchange`、`spin_loop` |
| 4 | `04_spinlock_guard` | RAII guard、`Deref` / `DerefMut` / `Drop` |
| 5 | `05_rwlock` | 从零实现写者优先读写锁（不使用 `std::sync::RwLock`） |

### 模块 4：上下文切换 — `04_context_switch/`（仅 riscv64）

| # | 练习 | 涉及概念 |
|---|----------|----------|
| 1 | `01_stack_coroutine` | 被调用者保存寄存器、栈帧、上下文切换 |
| 2 | `02_green_threads` | 绿色线程调度器、协作式调度、yield |

模块 4 仅能在 **riscv64** 上运行。和仓库中的其他部分一样，直接运行 `./check.sh` 或使用 `oscamp` CLI 即可，不需要额外脚本。详情见 `exercises/04_context_switch/README.md`。

### 模块 5：异步编程 — `05_async_programming/`

| # | 练习 | 涉及概念 |
|---|----------|----------|
| 1 | `01_basic_future` | 手动实现 `Future` trait、`Poll`、`Waker` |
| 2 | `02_tokio_tasks` | `tokio::spawn`、`JoinHandle`、并发任务 |
| 3 | `03_async_channel` | `tokio::sync::mpsc`、异步生产者-消费者 |
| 4 | `04_select_timeout` | `tokio::select!`、超时控制、竞态执行 |

### 模块 6：页表 — `06_page_table/`

| # | 练习 | 涉及概念 |
|---|----------|----------|
| 1 | `01_pte_flags` | SV39 PTE 位布局、通过位操作构造/解析页表项 |
| 2 | `02_page_table_walk` | 单级页表、VPN/offset 拆分、地址翻译、缺页异常 |
| 3 | `03_multi_level_pt` | SV39 三级页表、页表遍历、大页（2MB）映射 |
| 4 | `04_tlb_sim` | TLB 查找/插入/FIFO 替换、刷新（全部/按页/按 ASID）、MMU 模拟 |

## 快速开始

```bash
# 1. 克隆仓库
git clone <repo-url> && cd oscamp-base-experiment

# 2. 构建交互式 CLI 工具
cargo build -p oscamp-cli

# 3. 启动交互式练习模式（推荐）
./target/debug/oscamp watch
```

## 交互式 CLI 工具（`oscamp`）

仓库内置了一个类似 rustlings 的交互式终端工具，支持实时文件监听和进度跟踪：

```bash
oscamp              # 启动交互式监听模式（默认）
oscamp watch        # 同上
oscamp list         # 查看所有练习的完成状态
oscamp check        # 批量检查所有练习
oscamp run <pkg>    # 运行指定练习的测试
oscamp hint <pkg>   # 查看练习提示
oscamp help         # 显示帮助
```

### 监听模式特性

- **自动检测文件变更**：保存文件后自动重新运行测试
- **自动跳转**：当前练习通过后，自动跳到下一个未完成练习
- **实时进度条**：显示整体完成进度
- **快捷键**：
  - `h`：查看当前练习提示
  - `l`：查看全部练习列表
  - `n` / `p`：下一个 / 上一个练习
  - `r` / `Enter`：重新运行测试
  - `q` / `Esc`：退出

### 手动执行

```bash
# 运行某个练习的测试
cargo test -p thread_spawn

# 查看详细输出
cargo test -p thread_spawn -- --nocapture

# 检查全部练习
cargo test --workspace
```

## 建议流程

1. **开始**：运行 `./target/debug/oscamp watch` 进入交互模式
2. **阅读**：打开当前练习文件 `src/lib.rs`，阅读文档理解相关概念
3. **编码**：找到 `todo!()` 标记，根据注释提示补全代码
4. **保存**：保存文件后，CLI 会自动重新运行测试
5. **通过**：测试通过后会自动跳转到下一题；你也可以随时按 `h` 查看提示

## 提交成绩

将代码推送到你仓库的 `main` 分支后，会触发评分流水线。GitHub Actions 会自动运行全部测试、计算你的得分（满分 100），并上传到 OpenCamp 排行榜。

1. 接受 GitHub Classroom 作业链接，这会创建你的个人仓库
2. 在本地或 **GitHub Codespaces** 中完成练习（点击 "Code" > "Codespaces" > "Create"）
3. 提交并推送你的修改到 `main`
4. 在 "Actions" 页面查看分数

## 说明

- 部分练习（例如模块 2 的 syscall wrapper、模块 4 的汇编相关内容）需要 **Linux** 环境；模块 4 仅支持 **riscv64**
- 建议按照模块顺序完成练习；每个模块内部的题目也按照由浅入深排列

## 许可证

MIT
