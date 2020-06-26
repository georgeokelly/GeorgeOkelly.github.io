---
title: 'RISC-V ISA笔记'
tags: 
  - '计算机硬件'
  - 'Hardware'
  - 'RISC-V'
categories: Computer Architecture
keywords: RISC-V
description: 本文主要作为RISC-V (ISA部分)的一个梳理。
cover: https://github.com/georgeokelly/georgeokelly.github.io/blob/source/source/_images/RISCV.png
date: 2018-12-19 21:43:49
katex: true
---

Datapath部分见： {% btn 'https://georgeokelly.github.io/2019/01/16/RISCV-Datapath/', RISC-V Datapath, far fa-hand-point-right,outline larger %}

---

RISC-V Instruction 的格式通常为
<span style="font-size:16px"> **Operation Code + Destination Register + First Operand Register + Second Oprand Register** </span>

如 **`add x1, x2, x0`** 所对应的 **`opcode = add`** ; **`rd = x1`**; **`rs1 = x2`**; **`rs2 = x0`**

# 基础部分 RV32I Base Integer ISA

对于RV32I，有32个寄存器，分别是：

寄存器|调用名字|用途|存储者|调用中是否保留
:--:|:--:|:--:|:--:|:--:
x0	|zero	|常数"0"	 |N.A.|-
x1	|ra 	|返回地址 	 |Caller|No
x2	|sp 	|栈指针 	 |Callee|Yes
x3	|gp 	|全局指针 	 |/|-
x4	|tp 	|线程指针 	 |/|-
x5  |t0     |临时存储/alternate link register|Caller|No
x6-x7	|t1-t2 	|临时存储 	 |Caller|No
x8	|s0/fp 	|保存用寄存器/帧指针（配合栈指针界定一个函数的栈）	 |Callee|Yes
x9 	|s1 	|保存用寄存器 	 |Callee|Yes
x10-x11 	|a0-a1 	|函数参数/返回值 	 |Caller|No
x12-x17 	|a2-a7 	|函数参数	 |Caller|No
x18-x27 	|s2-s11 	|保存用寄存器	 |Callee|Yes
x28-x31 	|t3-t6 	|临时存储 	 |Caller|No

然后还有pc寄存器。

而对于RV32F和RV32D，也有32个寄存器，分别是：

寄存器|调用名字|用途|存储者|调用中是否保留
:--:|:--:|:--:|:--:|:--:
f0-f7 	|ft0-ft7 	|浮点临时存储 	 |Caller|No
f8-f9 	|fs0-fs1 	|浮点保存用寄存器 	 |Callee|Yes
f10-f11 	|fa0-fa1 	|浮点函数参数/返回值 	 |Caller|No
f12-f17 	|fa2-fa7 	|浮点函数参数 	 |Caller|No
f18-f27 	|fs2-fs11 	|浮点保存用寄存器 	 |Callee|Yes
f28-f31 	|ft8-ft11 	|浮点临时存储	 |Caller|No

<div class="note info">**Tips:**
<p>callee:是一个指针，指向拥有这个arguement对象的<span style="color:firebrick">函数</span>；</p>
caller:保留着<span style="color:firebrick">调用当前函数的函数</span>的引用。</div>

<div class="note info">**Tips:**

调用中是否保留 Preserved across call

保留数据不被改变的称为saved register;否则为temporary register。对于 `ra`: Just a reminder that if you choose to save the return address in a register, then that register must be one which is preserved across procedure calls. Even better, save `ra` on the stack.</div>

## RV32I标准指令集有以下几种框架：

- <span style="color:red">**R-format**</span> for register-register arithmetic/logical operations
- <span style="color:red">**I-format**</span> for register-immediate arith/logical operations and loads
- <span style="color:red">**S-format**</span> for stores
- <span style="color:red">**B-format**</span> for branches
- <span style="color:red">**U-format**</span> for 20-bit upper immediate instructions
- <span style="color:red">**J-format**</span> for jumps
- <span style="color:red">**Others**</span>:  Used for OS & Syncronization

<span style="color:red">**R**</span>即Reg相关；<span style="color:red">**I**</span>即立即数相关；<span style="color:red">**S**</span>存储相关；<span style="color:red">**B**</span>分支相关；<span style="color:red">**U**</span>高位数相关（因为一条32位指令中无法表示高达32位的数据）；<span style="color:red">**J**</span>跳转相关。

<div class="note info"> **Tips: About Arithmetic & Logical Operations.**
<p>逻辑右移(LSR)是将各位依次右移指定位数，然后在<span style="color:firebrick">左侧补0</span>，算术右移(ASR)是将各位依次右移指定位数，然后在左侧<span style="color:firebrick">用原符号位补齐</span>。</p>
<p>逻辑左移与算术左移操作相同。</p>
<p>RISC-V采用小端格式（Little-Endian），即**低位**字节排放在内存的**低地址**端，高位字节排放在内存的高地址端。</p></div>

<h2 style="color:forestgreen">R-Format</h2>

通常是XXX rd, rs1, rs2。

Funct7	|rs2	|rs1	|Funct3	|rd	|opcode
:--:|:--:|:--:|:--:|:--:|:--:
7	|5	|5	|3	|5	|7
`31.........................................................0`

R-Format的 **Opcode = 0110011**

其中，Funct7 + Funct3 + opcode 定义了要执行的操作。但其实Funct7在RV32I中应用不多，比如Add和Sub有Funct7的改变。

/|Funct7|Funct3|opcode|用途
:--:|:--:|:--:|:--:|:--:
<span style="color:CornflowerBlue">**ADD**</span>|0000000|000|0110011|加法
<span style="color:CornflowerBlue">**SUB**</span>|0100000|000|0110011|减法
SLL|0000000|001|0110011|左移
SLT|0000000|010|0110011|Set Less Than
SLT| | | |:= rs1 < rs2 ? 1:0
SLTU|0000000|011|0110011|SLT Unsigned
<span style="color:CornflowerBlue">**XOR**</span>|0000000|100|0110011|异或
SRL|0000000|101|0110011|逻辑右移
SRA|0100000|101|0110011|算术右移
<span style="color:CornflowerBlue">**OR**</span>|0000000|110|0110011|或
<span style="color:CornflowerBlue">**AND**</span>|0000000|111|0110011|与

**相关伪指令：**

<code><span style="font-size:18px"><span style="color:CornflowerBlue">mv rd, rs</span>   =   addi rd, rs, x0</span></code>

<code><span style="font-size:18px"><span style="color:CornflowerBlue">nop</span>   =   addi r0, r0, x0</span></code>

<code><span style="font-size:18px">not rd, rs   =   xori rd, rs, 111111111111</span></code>

<div class="note info">**Note:**

因为某事突然发现RISC-V好像没有循环移位的指令（未查证），要实现循环移位估计要三条指令以上。</div>

<h2 style="color:forestgreen">I-Format</h2>

Immediate|rs1|Funct3|rd|opcode
:--:|:--:|:--:|:--:|:--:
12	|5	|3	|5	|7

`31.........................................................0`

I-Format的 **Opcode = 0010011**

/|Shift-by-immediate (shamt)|rs1|Funct3|rd|opcode
:--:|:--:|:--:|:--:|:--:|:--:
0000000|5 |5 |3 |5	|7

`31.........................................................0`

第一部分包括<span style="color:CornflowerBlue">ADDI</span>, SLTI, SLTIU, <span style="color:CornflowerBlue">XORI</span>, <span style="color:CornflowerBlue">ORI</span>, <span style="color:CornflowerBlue">ANDI</span>（立即数有12位）和<span style="color:CornflowerBlue">SLLI, SRLI, SRAI</span>（立即数仅有5位），还包括<span style="color:firebrick">JALR</span>。

第二部分包括load instruction，格式同ADDI（12位的立即数）Load Opcode 0000011有LB(load byte) ，LH(load halfword=2 bytes)， <span style="color:CornflowerBlue">LW</span>，LBU(load unsigned byte)，LHU(load unsigned halfword)。

<h2 style="color:forestgreen">S-Format</h2>

Imm[11:5]|rs2|rs1|Funct3|Imm[4:0]|opcode
:--:|:--:|:--:|:--:|:--:|:--:
7|5	|5	|3	|5	|7

`31.........................................................0`

S-Format的 **Opcode = 0100011**

包括SB，<span style="color:CornflowerBlue">SW</span>，SH等指令。

<h2 style="color:forestgreen">B-Format</h2>

imm[12]|	imm[10:5]|	rs2 |	rs1 |	Funct3 |	imm[4:1]|	imm[11]|	opcode
:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:
1 |	6 |	5 |	5 |	3 |	4 |	1 |	7

`31.........................................................0`

B-Format的 **Opcode = 1100011**

可以表示-4096~4094的范围

<div class="note info">**Note:**

The <span style="color:red">12 immediate bits</span>  encode  even <span style="color:red">13-bit</span> signed byte offsets (**<span style="color:red">lowest bit</span> of offset is always <span style="color:red">zero</span>, so no need to store it**).

包括<span style="color:CornflowerBlue">BEQ</span>，<span style="color:CornflowerBlue">BNE</span>，BLT(branch less than: if [rs1]<[rs2], then branch) ，BGE(branch greater than or equal: if [rs1]>=[rs2], then branch) ，BLTU，BGEU。
</div>

**相关伪指令：**

`beqz x1, label　　=　　beq x1, x0, label`

`bnez x1, label　　=　　bne x1, x0, label`

<h2 style="color:forestgreen">U-Format</h2>

imm[31:12]|	rd|	opcode
:--:|:--:|:--:
20	|5	|7

`31.........................................................0`

LUI – Load Upper Immediate  lui rd, upper im(20-bit)

e.x.

`lui x10, 0x87654   # x10 = original value → 0x87654000`

AUIPC – Add Upper Immediate to PC

e.x.

`auipc t0, 1　　# t0 = original value → PC + 0x00001000`

`auipc t0, 0　　# t0 = original value → PC`

相关伪指令：

加载立即数

<code><span style="font-size:18px"><span style="color:CornflowerBlue">li rd, imm(32-bit)</span> = lui rd, imm(20-bit) + addi rd, rd, imm(12-bit)
</span></code>

加载地址

<code><span style="font-size:18px"><span style="color:CornflowerBlue">la rd, label</span> = auipc rd, imm(20-bit) + addi rd, rd, imm(12-bit)
</span></code>

<h2 style="color:forestgreen">J-Format</h2>

imm[20]	|imm[10:1]	|imm[11]	|imm[19:12]	|rd	|opcode
:--:|:--:|:--:|:--:|:--:|:--:
1	|10	|1|	8|	5|	7
`31.........................................................0`

<code><span style="color:CornflowerBlue">jal x0, label</span> </code>     Discard return address

(1)<code><span style="color:CornflowerBlue">jal ra, function_name</span> </code>

(2)<code><span style="color:CornflowerBlue">jr ra</span></code>  Call Function within 2^18 (指令中得到的立即数为 22 bits，而1条指令占用了4字节=32 bits).

<div class="note warning">
注意jalr是I-Format而不是J-Format，其指令结构如下。</div>

Immediate|rs1|Funct3|rd|opcode
:--:|:--:|:--:|:--:|:--:
imm	|5	|3	|5	|7
imm[11:0]|rs1|000|rd|1100111

**# Call function at any 32-bit <span style="color:red">absolute address</span>**

`lui x1, <high 20-bit>`

`jalr ra, x1, <low 12-bit>`      Call Function at 32-bit absolute address

**# Jump <span style="color:red">PC-relative</span> with 32-bit <span style="color:red">offset</span>**

`auipc x1, <high 20-bit>`

`jalr x0, x1, <low 12-bit>`      Jump within 32-bit

相关伪指令:

`j label     =     jal x0, label`

`ret          =     jr ra               =     jalr x0, 0(ra)`

<div class="note info">**Elaboration:**

如上所述，B-Format可以看作是S-Format的一个变种（the immediate field is rotated 1 bit for branch instructions, a variation of the S-Format that we relabel the B-Format.）同理，J-Format也可以看作是U-Format的变种，或者说是某种复用。所以实际上RISC-V只有4种框架，但我们为了方便仍称呼6框架。</div>

![fig1](https://raw.githubusercontent.com/georgeokelly/hello-world/master/blog_images/2019-1-15/1.jpg)

# 补充部分 RV32F and RV32D

待更新

# 补充部分 RV32V

待更新

# 补充部分 RV32M

RV32M是关于乘法和除法的指令。显然地，有 

`Quotient = (Dividend - Remainder) / Divisor`

`Product = Multiplicand * Multiplier`

-|RV32M|-
:--:|:--:|:--:
**mul**tiply |-| **mul**
**mul**tiply **h**igh|-| **mulh**
/| **u**signed| **mulhu**
/| **s**igned **u**nsigned | **mulhsu**
**div**ide|-| **div**
/| **u**signed| **divu**
**rem**ainder|-| **rem**
/| **u**nsigned| **remu**

对于许多微处理器，整数除法指令相对很慢，而右移可以实现2的次方的除法（同理左移实现乘法）。而对于其它数作为除数也可以通过乘一个倒数，再修正乘积的高半部分进行优化。以下是一个除以3的例子。

`lui  t0, 0xaaaab    #t0 = 0x000aaaab`

`addi  t0, t0, -1365    #t0 ≈ 2^32 / 1.5 = 0xaaaaaaab`

`mulhu  a1, a0, t0    #a1 ≈ a0 / 1.5`

`srli  a1, a1, 0x1    #a1 = a0 / 3`

可以看出在一些简单的情况这种方法可以推广（比如除数低于31位，且近似值容易恢复到原值的情况）。事实上，这个方法普遍适用，只是对于其他情形，修正过程会更加复杂。 关于正确性，生成倒数和修正的证明，见 *Division by invariant integers using multiplication*, Granlund and Montgomery 1994. [HERE](http://dl.acm.org/citation.cfm?doid=178243.178249)。这里简要描述下usigned division的理论基础。

<div class="note success">

假设m, d, l 是非负整数，d≠0，且满足

$$2^{N+l}\leq m*d\leq 2^{N+l}+2^l$$

则对于任意整数$n\in [0, 2^{N+l})$，有

$$floor(n/d)=floor(\frac{mn}{2^{N+l}})$$

**PROOF**

设$k=md-2^{N+l}$，那么有$0\leq k\leq 2^l$

给定$n\in [0, 2^{N+l})$，有$n=qd+r$。其中r=mod(n, d)且满足 $r\in [0, d-1]$。易知同时有 $floor(n/d)=q$。

$$
Let\quad F=\frac{mn}{2^{N+l}}-floor(n/d)\\
=\frac{mn}{2^{N+l}}-q
=\frac{k+2^{N+l}}{d}\times\frac{n}{2^{N+l}}-\frac{n-r}{d}\\
=\frac{k}{2^l}\times\frac{n}{2^Nd}+\frac{n}{d}-\frac{n-r}{d}
=\frac{k}{2^l}\times\frac{n}{2^N}\times\frac{1}{d}+\frac{r}{d}\\
\leq\frac{2^N-1}{2^N}\times\frac{1}{d}+\frac{d-1}{d}
=1-\frac{1}{2^Nd}<1
$$

所以$floor(F)=0\to floor(n/d)=floor(\frac{mn}{2^{N+l}})$。因此除以d可以用乘以m加移位操作来实现。

实际上，有$\frac{n}{d}<\frac{mn}{2^{N+l}}<\frac{n+1}{d}$，所以可以用m来代替d。
</div>

<div class="note info"> **Tips:**

MULH 和 MULHU 可以检查乘法中的溢出（这里指乘积是否超过32位）。

在执行除法指令前，请确认除数不为0. 用 `beqz`

MULHSU 对多字符号型乘法很有效。
</div>

# 补充部分 RV32A

待更新

# 补充部分 RV32C

待更新

# 补充部分 RV64

待更新

# References

[UC Berkeley CS61C](http://www-inst.eecs.berkeley.edu/~cs61c/sp18/)

[可以在线编译RISC-V的工具](http://www.kvakil.me/venus/)

[The RISC-V Reader: An Open Architecture Atlas](http://riscvbook.com/)

<!-- Last SVG -->
![](https://img.shields.io/badge/last--updated-2019.1.31-blue.svg)