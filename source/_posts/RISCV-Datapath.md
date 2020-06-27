---
title: RISC-V Datapath笔记
tags: 
    - '计算机硬件'
    - 'Hardware'
    - 'RISC-V'
categories: Computer Architecture
keywords: RISC-V
description: 本文主要作为RISC-V (Datapath部分)的一个梳理。
cover: https://picture-1302512218.cos.ap-guangzhou.myqcloud.com/6-27/RISCV.png
date: 2019-01-16 20:28:29
katex: false
---

----

从RV32I到电路结构

本文参考 UC Berkeley CS 61c Lecture 11-12，且以 RV32I 为例进行说明。

ISA部分见：[RISC-V ISA笔记](https://georgeokelly.github.io/2018/10/19/RISCV-ISA/)

![fig1 from UC Berkeley CS 61c](https://raw.githubusercontent.com/georgeokelly/hello-world/master/blog_images/2019-1-15/2/1.png)

# 主电路部分

先看加法指令，`ADD rd, rs1, rs2`

**指令寄存器表格**

Funct7	|rs2	|rs1	|Funct3	|rd	|opcode
:--:|:--:|:--:|:--:|:--:|:--:
7	|5	|5	|3	|5	|7
`31.........................................................0`

那么有如下图fig2所示的电路结构。目的寄存器在指令中是第7-11位（从0位开始计），源寄存器A是第15-19位，B是20-24位。所以PC先从指令内存中读取该加法指令，再将寄存器编号送入寄存器组，取出源寄存器中的数据并由ALU进行运算，再返回到目的寄存器中，此时需要寄存器组先写使能，即RegWEn=1。整个过程的时序应当如图fig3所示（时间不代表真实的执行过程，只是作为示意图）。

![fig2 from UC Berkeley CS 61c](https://raw.githubusercontent.com/georgeokelly/hello-world/master/blog_images/2019-1-15/2/2.png)

![fig3 from UC Berkeley CS 61c](https://raw.githubusercontent.com/georgeokelly/hello-world/master/blog_images/2019-1-15/2/3.png)

如果是减法指令，那么就需要给ALU输入一个控制信号，来决定是进行加法还是减法，如图fig4中，ALUSel控制信号取0时执行加法，取1时为减法。

![fig4 from UC Berkeley CS 61c](https://raw.githubusercontent.com/georgeokelly/hello-world/master/blog_images/2019-1-15/2/4.png)

而对于I-Format的类似指令，如 `ADDI rd, rs1, imm` ，就需要一个额外的立即数产生电路，如图fig5，且由控制信号BSel来控制二路选择器从而选择是使用立即数还是寄存器的数据。

Immediate|rs1|Funct3|rd|opcode
:--:|:--:|:--:|:--:|:--:
12	|5	|3	|5	|7

`31.........................................................0`

注意到，指令中的立即数仅占据了高12位（另一类I-Format立即数为5位，不过其高7位全为0，可以当作0x0000000_imm的形式，在电路结构上是可以等效看待的），而立即数产生电路产生的送至ALU计算的立即数为32位。实际上，ALU立即数的低12位就是指令中的高12位，而其高20位则是复制了指令的最高位，有点类似算术右移。同理，立即数产生电路也需要使能来工作。

![fig5 from UC Berkeley CS 61c](https://raw.githubusercontent.com/georgeokelly/hello-world/master/blog_images/2019-1-15/2/5.png)

对于LW指令，其指令结构与ADDI相同。不过涉及到了数据内存中的数据操作。由ALU计算出的存放数据的地址送入数据内存中，并使能读MemRW=Read，得到要载入寄存器的数据。为了和R-Format复用电路，后端增加一个二路选择器，控制信号WBSel取0时为内存相关操作，反之为寄存器相关操作。如图fig6所示。

![fig6 from UC Berkeley CS 61c](https://raw.githubusercontent.com/georgeokelly/hello-world/master/blog_images/2019-1-15/2/6.png)

SW指令相比LW指令，需要将寄存器rs2的数据载入数据内存，所以内存写使能MemRW=Write。同时rs2，如图fig7所示的DataB需要将数据送入数据内存，所以需要一条数据通路，此时应当将寄存器组写使能信号RegWEn置0，这样不论WBSel取何值都没影响。

![fig7 from UC Berkeley CS 61c](https://raw.githubusercontent.com/georgeokelly/hello-world/master/blog_images/2019-1-15/2/7.png)

而对于I-Format与S-Format立即数生成电路的复用，首先两个Format指令对比如下。

31-25|24-20|19-15|14-12|11-7|6-0
:--:|:--:|:--:|:--:|:--:|:--:
imm[11:5]|imm[4:0]|rs1|Funct3|rd|I-opcode
imm[11:5]|rs2|rs1|Funct3|imm[4:0]|S-opcode

而立即数产生电路都是取了12位立即数进行扩展的，所以真正输入电路的指令应当是第7-31位，产生的立即数如下，区别就是imm[4:0]段位于指令的位置不同。

31-11|10-5|4-0
:--:|:--:|:--:
inst[31]=imm[11]|inst[30:25]|inst[24:20]
inst[31]=imm[11]|inst[30:25]|inst[11:7]

对于分支指令，如图fig8，需要一个分支判断的比较器来决定分支是否发生。如果判断发生，就是图中的PCSel=taken，那么就将ALU计算出的指令地址返回给PC，而PCSel是由BrEq, BrLT, inst[31:0]共同控制的。对于判断分支的比较器如图fig9所示。

![fig8 from UC Berkeley CS 61c](https://raw.githubusercontent.com/georgeokelly/hello-world/master/blog_images/2019-1-15/2/8.png)

![fig9 from UC Berkeley CS 61c](https://raw.githubusercontent.com/georgeokelly/hello-world/master/blog_images/2019-1-15/2/9.png)

关于J-Format的跳转指令如图fig10/fig11，在结构使用上和B-Format类似。不过有写回寄存器的操作(比如 jal ra, label)。

![fig10 from UC Berkeley CS 61c](https://raw.githubusercontent.com/georgeokelly/hello-world/master/blog_images/2019-1-15/2/10.png)

![fig11 from UC Berkeley CS 61c](https://raw.githubusercontent.com/georgeokelly/hello-world/master/blog_images/2019-1-15/2/11.png)

**总体电路结构如图fig12所示**

![fig12 from UC Berkeley CS 61c](https://raw.githubusercontent.com/georgeokelly/hello-world/master/blog_images/2019-1-15/2/12.png)

<div class="note warning">
问题：LUI的电路结构实现过程？
</div>

![fig13 from UC Berkeley CS 61c](https://raw.githubusercontent.com/georgeokelly/hello-world/master/blog_images/2019-1-15/2/13.png)

# 控制电路部分

![fig14 from UC Berkeley CS 61c](https://raw.githubusercontent.com/georgeokelly/hello-world/master/blog_images/2019-1-15/2/14.png)

控制电路如fig14所示。其真值表摘要如下。

imm[31:0]|BrEq|BrLT|PCSel|immSel|BrUn|ASel|BSel|ALUSel|MemRW|RegWEn|WBSel
:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:
add|x|x|+4|x|x|Reg|Reg|add|Read|1|ALU
sub|x|x|+4|x|x|Reg|Reg|sub|Read|1|ALU
R_op|x|x|+4|x|x|Reg|Reg|op|Read|1|ALU
addi|x|x|+4|I|x|Reg|Imm|add|Read|1|ALU
lw|x|x|+4|I|x|Reg|Imm|add|Read|1|Mem
sw|x|x|+4|S|x|Reg|Imm|add|Write|0|x
beq|0|x|+4|B|x|PC|Imm|add|Read|0|x
beq|1|x|ALU|B|x|PC|Imm|add|Read|0|x
bne|0|x|ALU|B|x|PC|Imm|add|Read|0|x
bne|1|x|+4|B|x|PC|Imm|add|Read|0|x
blt|x|1|ALU|B|0|PC|Imm|add|Read|0|x
bltu|x|1|ALU|B|1|PC|Imm|add|Read|0|x
jalr|x|x|ALU|I|x|Reg|Imm|add|Read|1|PC+4
jal|x|x|ALU|J|x|PC|Imm|add|Read|1|PC+4
auipc|x|x|+4|U|x|PC|Imm|add|Read|1|ALU

控制电路可以用组合逻辑或者ROM来实现。

**组合逻辑实现**

待更新

**ROM实现**

待更新

<div class="note info">
**补充**

关于性能：待更新

流水线相关：该内容可以参考MIPS经典五级流水线。包括旁路Bypass (aka Forwarding Control Logic)，分支预测等等。
</div>

# References

[UC Berkeley CS61C](http://www-inst.eecs.berkeley.edu/~cs61c/sp18/)

[The RISC-V Reader: An Open Architecture Atlas](http://riscvbook.com/)



![](https://img.shields.io/badge/last--updated-2019.1.16-blue.svg)