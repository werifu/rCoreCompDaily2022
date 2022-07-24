## 简介
参加2022 rCore Comp的学习日志

## 目录
[toc]
#### 7.05

* 一边阅读[《Rust语言圣经》](https://course.rs/about-book.html) 一边做rustlings（接近40道）
* 一些新的收获：
  * match 模式匹配是一个非常有魅力的设定，能像switch一样区分出各种各样可预见的值，还能进行匹配的赋值，在语义上有极其强大的表达能力；此外，rust中区分表达式和语句使得match这个行为本身也能有一个值，像是三目运算符的加强版
  * 实际上模式匹配无处不在，解构也是一种匹配，在js里非常常见而且优雅，@绑定给match的项增加了表达能力（可以用于进一步约束）
  * [给enum添加方法，如何匹配自身？](https://stackoverflow.com/questions/33925232/how-to-match-over-self-in-an-enum) match *self
  * rust在泛型&特征方面提供了巨多语法糖，<T: SomeTrait>、where、impl SomeTrait，看上去很方便，不过怀疑在实践中会带来一定的混乱。
  * 特征对象其实就是一个实现了某些trait的对象，但是编译期无法确定具体对象类型，因此只能在运行时创建，使用& / Box\<dyn Trait\>用智能指针来创建（特征对象的动态分发）

#### 7.06

* 一边阅读圣经一边做rustlings，进度53/84

  * 在error6卡到了一下，对map_err的使用有了更深的理解，rust的数据处理方式感觉跟js真的很像

* 收获：

  * vector的引用：

    对一个元素有不可变引用后，不能拥有该vector的可变引用（即不能同时push又同时修改其中某个元素），虽然看起来风牛马不相及，安全问题在哪？

    > 原因在于：数组的大小是可变的，当旧数组的大小不够用时，Rust 会重新分配一块更大的内存空间，然后把旧数组拷贝过来。这种情况下，之前的引用显然会指向一块无效的内存，这非常 rusty —— 对用户进行严格的教育

    这个点确实没想到

* to_string, to_owned, into()的区别

* 语法糖?会让Err的情况下一直返回错误，而main返回()，所以main里不能用func()?吗
  ：错误的，main也可以返回Result

  * 比Go文明许多的设计，代替了if err != nil return err这样的句子，让链式调用更方便

  

#### 7.07

* 一边阅读圣经一边做rustlings，进度69/84，开始难起来了
* 包crate和项目package
  * package是一个项目工程，crate是一个编译单元
* 限制可见性pub (in crate::a)
  * 原本子mod可以访问父mod的private项，但父不可以，使用限制可见性可以有一定自由度地控制父访问子private的权限
* 自定义实现fmt::Display
* 生命周期
  * 生命周期标注并不会改变任何引用的实际作用域，帮助推导（隐形=>显性
  * 生命周期消除三原则
    * **每一个引用参数都会获得独自的生命周期**
    * **若只有一个输入生命周期(函数参数中只有一个引用类型)，那么该生命周期会被赋给所有的输出生命周期**，也就是所有返回值的生命周期都等于该输入生命周期
    * **若存在多个输入生命周期，且其中一个是 `&self` 或 `&mut self`，则 `&self` 的生命周期被赋给所有的输出生命周期**
  * 不符合三原则就得手标，其实本质上都有标，只是编译器进行消除来减少手写

#### 7.08

* rustlings完成！
* 迭代器的 collect方法有很丰富的功能，手动指定可以返回想要的数组类型
* 线程屏障 barrier让多个线程都执行到某个点后，才继续一起往后执行
* RefCell 与内部可变性，可以解决struct部分字段可改的需求（可改字段使用RefCell<>包裹）
* 宏的写法，功能很强大，但是不好写，需要多琢磨

#### 7.09

* 做了五道rust quiz，真难啊，，，几道看了答案。答案附[另一个文档上](RustQuiz.md)

  * #1：注意甄别||闭包跟逻辑或
  * #16：rust没有自增/自减运算，但是可以`--x`表示负负得正的x
  * #21：
    * rust有Break-with-value这种东西，可以给loop statement提供一个值
    * `(return)`这个表达式的值是`()`
  * #26：迭代器的lazy特性，不被使用不会执行map里的闭包
  * #27：trait有`trait Trait1: Trait2 {}`的写法，但表示一种约束constraint，而不是继承，二者之间实际上没啥关系，因此在两个traits中有同样的方法名并不会造成混淆

* 看了《计算机组成与设计：软硬件接口RV》的第一章，感觉没太多新接收的概念，大多以前有了解，讲了计算机硬件发展的历程，然后和评判计算机性能的指标。有意思的是八个伟大的思想：

  1. 面向摩尔定律的设计 设计芯片的时候要考虑未来工艺，需要超前点

  2. 使用抽象简化设计

  3. 加速经常性事件

  4. 通过并行提高性能

  5. 通过流水线提高性能

  6. 通过预测提高性能

  7. 存储层次 Memory Hierarchy

  8. 通过冗余提高可靠性

#### 7.10
* 看组成设计，记录都写在[博客](https://werifu.github.io/posts/riscv_memo/)里了。
* 主要是学习了RV的指令集，数量挺少的，很简洁，感觉x0恒为0的设计很有趣。
* 学习到了双指令实现atomic exchange的方法

#### 7.11

* 学习RiscV的特权级指令，看[RV手册第十章](http://riscvbook.com/chinese/RISC-V-Reader-Chinese-v2p1.pdf)
  * mret：machine-mode trap return
  * sret： supervisor-mode trap return
  * wfi： wait for interrupt
  * sfence.vma：supervisor-mode fence virtual memory address
* 用户模式、机器模式、监管者模式三种状态
* 切换模式过程中对控制状态寄存器（CSR，八个）的使用
* 把lab0-0、lab0-1交了，都不需要做代码上的修改。
  lab0-1中在codespace里直接跑`make test2`会有找不到asm宏的问题，微信群里有助教解释了，感谢
* 虽然把lab0交了，但是对指南里的教程还是一知半解，明天再深入研究下

![](https://s3.bmp.ovh/imgs/2022/07/12/247945a94e0f69a1.png)

#### 7.12

* 仔细读lab0和lab1的指南
* rust实现特权级的切换
  * 跟RV手册第十章里一样，主要就是几个CSR的保存和切换
  * 保存CSR，陷入trap，调用trap_handler，之后再通过CSR返回用户态的状态
* 多道程序的加载：把应用程序载入不同的base_addr
* 任务切换：主动的yield，类似以前写过的jyyOS课的协程，只是这个在操作系统实现
* 分时多任务（抢占式调度），利用SBI的SBI_SET_TIMER能力，做到在一定时间后触发中断，然后在trap_handler的match中增加一个时间片中断处理，跟上面的任务切换一样

#### 7.13

* 做完lab1
* lab1报告，简答题思考，都写在下面链接了
  https://werifu.github.io/posts/rcore-camp-2022-lab1/

#### 7.14

* 上班摸鱼，看第四章（地址空间），读起来难度非常高，通读了一遍没看懂
* 以前课上大概知道这些知识，但是知识点没有连贯地串起来
* 实验题目也不是很明白

#### 7.15

* 继续看第四章，还是没怎么看懂
* 看了1h lec5的后半部分，地址空间部分，理解深了一些，但是还没串起来，周末再做知识的总结

#### 7.16

给自己放假一天

#### 7.17

* 完成了 sys_get_time 的重写
  * 核心其实就是把 virt_addr 转换成 phys_addr ，然后再将数据类型强转写入 \*phys_addr
  * 实现了一个 virt2phys_addr() 函数，通过查页表得到物理地址
* sys_get_task_info也是同样的原理：
  * 找到物理地址，再写入 \*task_info
  * 但是遇到了 bug ：写入 TaskStatus::Running 在评测里会变成 Ready ，没找到原因

#### 7.18

* 完成 sys_get_task_info
  * 在群友提醒下，找到一个[关于那个 bug 的 pr](https://github.com/LearningOS/rust-based-os-comp2022/pull/77)
  * 在评测里 enum TaskStatus 多了一个 UnInit 的值在前面，导致编译期枚举值解析不一致
* 写了 mmap 的逻辑：直接使用 frame_allocate() 操作，但是遇到了两次mmap，不同vpn映射到同一ppn的问题

#### 7.19

* 排查良久没看出问题，去偷瞄了两眼仓库里其他人的代码，发现我的设计就有问题
  * 没有用到 MemorySet 的抽象，而是直接去操作物理帧，所以分配后的物理帧没有被管理到，因此 frame_allocate() 后那个物理帧会自动 drop 掉，而资源又是RAII式的管理方式，自定义的 drop 函数里dealloc了这个帧，因此第二次 mmap 的时候又用上了原来的那一帧
* 校验地址后用 MemorySet 的 insert_framed_area 来分配新页
* 实现一个 MemorySet 的 munmap ，校验地址后通过遍历的匹配在 areas 里 remove 掉页
* 博客 WIP……
* 需要加快速度了，还剩3个实验但是只有10天



#### 7.20

* 博客写了，简答题还没完成
* 看第五章-进程管理
  * 进程管理的基础是 fork 和 exec （按伟大导师 jyy 说的，前者是状态机的复制，后者是状态机的重置）
  * rCore 里进程管理机制由一个 Processor 任务管理器负责，这个 Processor 里还有一个 idle_task_cx ，表示一个空闲的任务，实际上就是两个不同任务切换中间的一个状态，一个任务让出后会来到这个 idle_task_cx，再分配给下一个任务，这样在只有一个任务的时候也能调度给同一个任务

#### 7.21

* 看完了第五章

#### 7.22

* 今天上班累了，没精力学……休息

#### 7.23

* 搬运代码，能跑通以前的测试
  * Os5 里，把之前 TASK_MANAGER 的工作分成了 manager 和 processor 的工作，前者负责管理任务调度，后者负责操控当前任务 + 当前任务的切换，因此那些 time、info 相关的工作我都迁移进了 Processor 里
* 完成了 spawn
  * 其实就是从 fork 和 exec 的实现各偷一点，从前者偷新建一个上下文，后者偷修改状态机
* stride 实现，但是仍有 bug 未解决
  * 参考了助教 xushanpu 的讲解视频，学到许多

#### 7.24

* 花了一整天捣鼓 stride test，终于在三更半夜成功了
  * 会有 `already borrowed: BorrowMutError` 的 panic 报错，是在测试结束后调用最后一个 exit 时发生的
    * 定位：在函数前面加 `#[trace_caller]` 就能显示文件+行数
    * 原因：在测试的时候，initproc 会被替换成各章的 ch_usertest ,所以是不会像普通的运行那样进入 initproc 然后运行 shell 的， usertest 退出的时候所有权会被借走，但是后面又借回来了导致错误（其实就是在 exit_current_and_run_next_task 里，将要退出的和下一个任务是同一个，而我们需要同时可变借用这两个）
    * 解决方案：在拿到 current 后判断 pid，如果是 0（表示初进程）就 shutdown() 直接关机
* stride test 失败
  * 没找到原因
  * 在 ci-user 里，给几个 stride 测试样例加 println! 大幅降低运行速度就成功了。。。