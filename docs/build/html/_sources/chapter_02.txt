===============
第二章 数据表示
===============

.. contents::

----------------------------------------

Bytes
=====
虽然byte和int8都是一个字节，但是int8是有类型的，表示-128~127，\
而byte则是无类型的，仅表示一个字节，你可以在里面存放任意一个字节存放得下的数据。

.. code-block:: asm

   static
       b: byte;

Words
=====
word和int16的区别同上。

.. code-block:: asm

   static
       w: word;

Double Words
============
dword和int32的区别同上。

.. code-block:: asm

   static
       dw: dword;

Quad Words&&Long Words
======================
qword和int64的区别、lword和int128的区别同上。

.. code-block:: asm

   static
       i64: int64;
       i128: int128;

   static
       qw: qword;
       lw: lword;

.. note::

   这些数据不能直接用于mov/add/sub等这些常见的指令的，因为它们超过32位了。

逻辑操作
========
.. code-block:: asm

   and(source, dest);
   or(source, dest);
   xor(source, dest);

上面三个操作对应的高级语言语义如下：
::

   dest = dest operator source

and/or/xor的source必须为常量、内存、寄存器，dest必须为内存或者寄存器。\
这三个的其它要求基本和add/sub这些是一样的。
   
.. code-block:: asm

   not(dest);

not对应的高级语言语义如下：
::

   dest = not(dest)

not的dest必须为内存、寄存器。

取反操作
========
.. code-block:: asm

   neg(dest);

neg对应的高级语言主义如下：
::

   dest = -dest

无符号类型
==========
无符号类型申明
--------------
.. code-block:: asm

   static
       u8: uns8;
       u16: uns16;
       u32: uns32;
       u64: uns64;
       u128: uns128;

无符号类型输出
--------------
.. code-block:: asm

   stdout.putu8
   stdout.putu16
   stdout.putu32
   stdout.putu64
   stdout.putu128

   stdout.putu8Size
   stdout.putu16Size
   stdout.putu32Size
   stdout.putu64Size
   stdout.putu128Size

无符号类型输入
--------------
.. code-block:: asm

   stdin.getu8
   stdin.getu16
   stdin.getu32
   stdin.getu64
   stdin.getu128

符号扩展、零扩展
================
如果将一个8位的负数（补码表示）扩展为16位，只需要将其高位用符号位进行扩展即可。

如果将一个8位的无符号数扩展为16位，只需要将其高位用0进行扩展即可。

.. code-block:: asm

   cbw(); // 通过符号扩展将al中的字节转换为ax中的字
   cwd(); // 将ax中的字转换为dx:ax中的双字
   cdq(); // 将eax中的双字转换为edx:eax中的四字
   cwde(); // 将ax中的字转换为eax中的双字

.. code-block:: asm

   movsx(source, dest); // 符号扩展
   movzx(source, dest); // 零扩展

movsx和movzx需要注意一点：
::

   dest的数据宽度必须大于source的数据宽度

移位
====
.. code-block:: asm

   shl(count, dest); // 左移
   shr(count, dest); // 右移

如果dest为负数，shr操作就会产生意料之外的结果，符号位被0填充，结果变成了正数。\
这种情况就需要使用如下指令：

.. code-block:: asm

   sar(count, dest);

循环移位
========
.. code-block:: asm

   rol(count, dest); // 循环左移
   ror(count, dest); // 循环右移

如果仅移1个位，则移出的那一位会进入CF标志位。如果多于1位，Intel不保证CF标志位的状态。

如果想让CF中的值也参与循环移位，可以用以下指令：

.. code-block:: asm

   rcl(count, dest); // 带CF循环左移
   rcr(count, dest); // 带CF循环右移

同样的，如果多于1位，Intel不保证CF标志位的状态。

位域和压缩数据
==============
通过上面介绍的逻辑操作就可以操作每个字节中一位或者多位了。

对于EFLAGS寄存器，还有额外的指令提供：

.. image:: /_static/instructions_that_affect_certain_flags.png

.. code-block:: asm

   lahf(); // EFLAGS低8位->ah
   sahf(); // ah->EFLAGS低8位
   
浮点计算
========

* When substracting two numbers with the same signs \
  or adding two numbers with different signs, \
  the accuracy of the result may be less than \
  the precision avaiable in the floating-point format.

* When performing a chain of calculations involving addition, \
  substraction, multiplication, and division, \
  try to perform the multiplication and division operations first.

* When multiplying and dividing sets of numbers, \
  try to arrange the multiplications \
  so that they multiply large and small numbers together; \
  likewise, try to divide numbers that have the same relative magnitudes.

* When comparing two floating-point numbers, \
  always compare one value to see \
  if it is in the range given by the second value plus \
  or minus some small error value.

IEEE浮点格式
------------
.. image:: /_static/single_percision_floating_point_format.png

.. image:: /_static/double_percision_floating_point_format.png

.. image:: /_static/extended_percision_floating_point_format.png

类型声明
--------
.. code-block:: asm

   static
       fltVar1:  real32; // 单精度
       fltVar1a: real32 := 2.7;
       pi:       real32 := 3.14159;
       DblVar:   real64; // 双精度
       DblVar2:  real64 := 1.23456789e+10;
       XPVar:    real80; // 扩展精度
       XPVar2:   real80 := -1.0e-104;

输出与输入
----------
.. code-block:: asm

   stdout.putr80(r:real80, width:uns32, decpts:uns32);
   stdout.putr64(r:real64, width:uns32, decpts:uns32);
   stdout.putr32(r:real32, width:uns32, decpts:uns32);

width为输出的宽度，decpts为输出的小数点精度

.. code-block:: asm

   // 输出科学表达法
   stout.pute80(r:real80, width:uns32);
   stout.pute64(r:real64, width:uns32);
   stout.pute32(r:real32, width:uns32);

.. code-block:: asm

   stdin.getf();
   stdin.get();

字符
====
类型声明
--------
.. code-block:: asm

   static
       UserInput:      char;
       TheCharA:       char := 'A';
       ExtendedChar:   char := #65;
       ExtendedChar1:  char := #$41;
       ExtendedChar1:  char := #%0100_0001;

输出与输入
----------
.. code-block:: asm

   stdout.putc(charvar);
   stdout.putcSize(charvar, widthInt32, fillchar);

.. code-block:: asm

   stdout.getc();
   stdout.get();
