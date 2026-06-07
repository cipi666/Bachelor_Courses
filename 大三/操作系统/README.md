# 操作系统复习大纲

## 互斥与同步

### 基本概念

- 临界资源
- 同步
- 互斥

### 实现临界区互斥的基本方法

#### 软件实现方法

1. **单标志法**

```
// 进程P0
while (turn != 0); // 进入区
critical section; // 临界区
turn = 1; // 退出区
remainder section; // 剩余区

// 进程P1
while (turn != 1); // 进入区
critical section; // 临界区
turn = 0; // 退出区
remainder section; // 剩余区
```
```turn = 0```表示允许P0进入临界区；```turn = 1```表示允许P1进入临界区；该算法可以实现每次只允许一个进程进入临界区。会违反空闲让进的原则。

2. **双标志先检验法**

该算法设置一个布尔型数组```flag[2]```，用来表示各个进程想进入临界区的意愿。
```
// 进程P0:
while (flag[1]); // a
flag[0] = true; // c
critical section;
flag[0] = false;
remainder section;

// 进程P1：
while (flag[0]); // b
flag[1] = true; // d
critical section;
flag[1] = false;
remainder section;
```
按照a,b,c,d的顺序执行的时候会发生两个进程同时进入临界区（违背忙则等待的原则）。

3. **双标志后检验法**

先设置自己的标志，再检查对方的标志，若对方的标志为true，则等待；否则，进入临界区。
```
// 进程P0:
flag[0] = true; // a
while (flag[1]); // c
critical section;
flag[0] = false;
remainder section;

// 进程P1:
flag[1] = true; // b
while (flag[0]); // d
critical section;
flag[1] = false;
remainder section;
```
按照a,b,c,d执行时，双方都争着进入临界区，结果谁也进不了，违反空闲让进的原则。，导致饥饿现象。

4. Peterson算法

Peterson算法结合了前面1和3算法的思想，利用```flag[]```解决互斥访问问题，而利用```turn```解决饥饿问题。在每个进程进入临界区之前，先设置自己的flag标志，再设置允许进入turn标志；之后再检查对方的标志。

```
// 进程P0:
flag[0] = true; // 表达意愿
turn = 1; // 表达谦让
while (flag[1] && turn == 1);
critical section;
flag[0] = false;
remainder section;

// 进程P1：
flag[1] = ture; // 表达意愿
turn = 0; // 表达谦让
while (flag[0] && turn == 0);
critical section;
flag[1] = false;
remainder section;
```
Peterson算法不满足让权等待的原则。

#### 硬件实现方法

1. **中断屏蔽方法**

当一个进程正在执行它的临界区代码时，防止其他进程进入其临界区的最简单方法是关中断。因为CPU只在发生中断时引起进程切换，因此屏蔽中断能够保证当前运行的进程让临界区代码顺利地执行完，进而保证互斥的正确实现，然后执行开中断。其典型模式为
```
关中断；
临界区；
开中断；
```
缺点：不适用于多处理器系统，不适合用户进程。

2. **硬件指令方法——TestAndSet指令**

3. **硬件指令方法——Swap指令**

#### 互斥锁（mutex lock）

acquire()和release()

#### 信号量

信号量机制是一种功能较强的机制，它只用能两个标准的原语wait()和signal()访问，也可以简称为P操作和V操作。