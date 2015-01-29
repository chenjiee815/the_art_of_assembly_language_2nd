=============================
第一章 汇编语言之Hello, World
=============================

.. contents::

----------------------------------------

HLA程序的基本格式
=================

.. code-block:: asm

   program pgmID;
       << Declarations >>

   begin pgmID;
       << Statements >>

   end pgmID;

部分基本类型声明
================

.. code-block:: asm

   program baseData;
       #include("stdlib.hhf")

       static
           a: int8    := 8;
           b: int16   := 16;
           c: int32   := 32;
           d: boolean := false;
           e: boolean := true;
           f: char    := 'A';

   begin baseData;
       stdout.put("a = ", a, nl);
       stdout.put("b = ", b, nl);
       stdout.put("c = ", c, nl);
       stdout.put("d = ", d, nl);
       stdout.put("e = ", e, nl);
       stdout.put("f = ", f, nl);

   end baseData;

80x86CPU体系简介
================
80x86CPU体系是典型的冯-诺依曼结构，主要由下面三个部分组成：

1. CPU

2. 内存

3. 输入/输出设备

以上三者通过 *系统总线* 来进行通信。

通用寄存器
----------
一般的应用程序常用的是以下几种 *通用寄存器* ：
::

   EAX, EBX, ECX, EDX, ESI, EDI, EBP, ESP

   AX, BX, CX, DX, SI, DI, BP, SP

   AL, AH, BL, BH, CL, CH, DL, DH

.. image:: /_static/general_purpose_registers.png

EFLAGS寄存器
------------

.. image:: /_static/flags_register.png

部分基本机器指令
================
mov
---
.. code-block:: asm

   mov(source_operand, destination_operand);

在高级语言里的同等语义为：

.. code-block:: c

   destination_operand = source_operand;

source_operand可为：寄存器、内存、常量

destination_operand可为：寄存器、内存

机器指令mov不允许，两者都为内存，但是HLA的mov指令是支持的。

.. note::

   source_operand和destination_operand必须是一样的数据长度。

add
---
.. code-block:: asm

   add(source_operand, destination_operand);

在高级语言里的同等语义为：

.. code-block:: c

   destination_operand = destination_operand + source_operand;
   destination_operand += source_operand;

sub
---
.. code-block:: asm

   sub(source_operand, destination_operand);

在高级语言里的同等语义为：

.. code-block:: c

   destination_operand = destination_operand - source_operand;
   destination_operand -= source_operand;

部分HLA基本控制结构
===================
布尔表达式
----------
HLA使用0表示false, 其它表示true。

HLA支持的布尔表达式格式：

.. code-block:: asm

   flag_specification
   !flag_specification
   register
   !register
   Boolean_variable
   !Boolean_variable
   mem_reg relop mem_reg_const
   register in LowConst..HiConst
   register not in LowConst..HiConst

.. image:: /_static/flag_specification.png

或者：

.. image:: /_static/legal_boolean_expressions.png

举例：

.. code-block:: asm

   @c
   Bool_var
   al
   esi
   eax < ebx
   ebx > 5
   i32 < -2
   i8 > 128
   al < i8
   eax in 1..100
   ch not in 'a'..'z'

   @z && al in 5..10
   al in 'a'..'z' && ebx
   Bool_var && !eax

   @z || al = 10
   al in 'a'..'z' || ebx
   !Bool_var || eax

   !(eax < 0)

.. note::

   布尔表达式中两边的操作数不能都为内存，而且两者的长度要一样。

.. warning::

   这里有一个问题：如果布尔表达式中左值为register，\
   右值为一个正数或者另外一个register，HLA将之当作无符号类型来进行比较。

if...endif
----------
.. code-block:: asm

   if (expression) then
       << sequence of one or more statements >>
   endif;

   if (expression) then
       << sequence of one or more statements >>
   else
       << sequence of one or more statements >>
   endif;

   if (expression) then
       << sequence of one or more statements >>
   elseif (expression) then
       << sequence of one or more statements >>
   else
       << sequence of one or more statements >>
   endif;
       
while...endwhile
----------------
.. code-block:: asm

   while(expression) do
       << sequence of one or more statements >>
   endwhile;

for...endfor
------------
.. code-block:: asm

   for(Initial_Stmt; Termination_Expression; Post_Body_Statement) do
       << Loop body >>
   endfor;

repeat...until
--------------
.. code-block:: asm

   repeat
       << sequence of one or more statements >>
   until(expression);

循环体流程控制
--------------
break&&breakif: 

.. code-block:: asm

   break;
   breakif(expression);

.. note::

   不支持多层循环体退出

continue&&continueif: 

.. code-block:: asm

   continue;
   continueif(expression);

forever..endfor
---------------
无限循环。

.. code-block:: asm

   forever
       << sequence of one or more statements >>
   endfor;

try...exception...endtry
-----------------------
.. code-block:: asm

   try
       << sequence of one or more statements >>
   exception(exceptionID)
       << sequence of one or more statements >>
   exception(exceptionID)
       << sequence of one or more statements >>
   endtry;

HLA标准库简介
=============
主要讲解了stdout和stdin这两个命名空间中的常用函数。

有关try..endtry的更多细节
=========================
* try..endtry支持嵌套。

* unprotected：用来恢复异常现场。

* anyexception: 捕获任意异常。

* try..endtry中的代码不能随意使用寄存器，因为异常处理会打乱寄存器中的值。
