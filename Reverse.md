<br />

# PART1_程式編譯組譯與逆向(反編譯)
# c語言程式編譯與組譯
<br >

預處理階段
-------------------------

```
gcc -E test.c -o test.i
```
.i的架構:<br />
[https://github.com/zinwang/MyFirstSecurity/blob/master/test.i](https://github.com/zinwang/MyFirstSecurity/blob/master/test.i)

<br />

編譯階段:產生組語<br />
--------------------------------

```
gcc -S test.i -o test.s
```

<br />
.s的架構:<br />

```
	.file	"test.c"
	.section	.rodata
.LC0:
	.string	"%d + 100 = %d * %d\n"
.LC1:
	.string	"%d + 268 = %d * %d\n"
	.text
	.globl	main
	.type	main, @function
main:
.LFB0:
	.cfi_startproc
	pushq	%rbp
	.cfi_def_cfa_offset 16
	.cfi_offset 6, -16
	movq	%rsp, %rbp
	.cfi_def_cfa_register 6
	subq	$32, %rsp
	movl	$1, -20(%rbp)
	jmp	.L2
.L4:
	movl	$168, %eax
	cltd
	idivl	-20(%rbp)
	movl	%edx, %eax
	testl	%eax, %eax
	jne	.L3
	movl	$168, %eax
	cltd
	idivl	-20(%rbp)
	movl	%eax, -16(%rbp)
	movl	-20(%rbp), %eax
	cmpl	-16(%rbp), %eax
	jle	.L3
	movl	-20(%rbp), %edx
	movl	-16(%rbp), %eax
	addl	%edx, %eax
	andl	$1, %eax
	testl	%eax, %eax
	jne	.L3
	movl	-20(%rbp), %eax
	subl	-16(%rbp), %eax
	andl	$1, %eax
	testl	%eax, %eax
	jne	.L3
	movl	-20(%rbp), %edx
	movl	-16(%rbp), %eax
	addl	%edx, %eax
	movl	%eax, %edx
	shrl	$31, %edx
	addl	%edx, %eax
	sarl	%eax
	movl	%eax, -12(%rbp)
	movl	-20(%rbp), %eax
	subl	-16(%rbp), %eax
	movl	%eax, %edx
	shrl	$31, %edx
	addl	%edx, %eax
	sarl	%eax
	movl	%eax, -8(%rbp)
	movl	-8(%rbp), %eax
	imull	-8(%rbp), %eax
	subl	$100, %eax
	movl	%eax, -4(%rbp)
	movl	-8(%rbp), %ecx
	movl	-8(%rbp), %edx
	movl	-4(%rbp), %eax
	movl	%eax, %esi
	movl	$.LC0, %edi
	movl	$0, %eax
	call	printf
	movl	-12(%rbp), %ecx
	movl	-12(%rbp), %edx
	movl	-4(%rbp), %eax
	movl	%eax, %esi
	movl	$.LC1, %edi
	movl	$0, %eax
	call	printf
.L3:
	addl	$1, -20(%rbp)
.L2:
	cmpl	$84, -20(%rbp)
	jle	.L4
	movl	$0, %eax
	leave
	.cfi_def_cfa 7, 8
	ret
	.cfi_endproc
.LFE0:
	.size	main, .-main
	.ident	"GCC: (Ubuntu 5.4.0-6ubuntu1~16.04.9) 5.4.0 20160609"
	.section	.note.GNU-stack,"",@progbits

```

<br />

產生AT&T語法格式的組語(gcc預設使用的格式)


```
gcc -S -masm=att XXXXX.c -o XXXXX_att.s
```

產生Intel語法格式的組語(微軟預設使用的格式)

```
gcc -S -masm=intel XXXXX.c -o XXXXX_intel.s
```

要去掉一堆註解:請加上參數-fno-asynchronous-unwind-tables

```
gcc -S -masm=intel XXXXX.c -o XXXXX_intel_OK.s -fno-asynchronous-unwind-tables
```
<br />

組譯階段:<br />
----------------------------

```
gcc -C test.s -o test.o
```
<br />
.o檔的架構:<br />

[https://github.com/zinwang/MyFirstSecurity/blob/master/object](https://github.com/zinwang/MyFirstSecurity/blob/master/object)


<br />

![](https://github.com/zinwang/MyFirstSecurity/blob/master/%E6%93%B7%E5%8F%96.PNG)

<br />

連結階段:<br />
------------------------------

```
gcc test.o -o test
```
<br />
也可編成jpg或exe<br />
副檔名不是重點!!!!!<br />
他還是elf執行檔

```
gcc test.o -o test.jpg
```

<br />
=============================================================================

![](https://github.com/zinwang/MyFirstSecurity/blob/master/%E6%93%B7%E5%8F%962.jpg)









objdump 反編譯
-----------------------------------------


```
objdump  -S -M intel test3
```


```

test3:     file format elf64-x86-64


Disassembly of section .init:

00000000004003c8 <_init>:
  4003c8:	48 83 ec 08          	sub    rsp,0x8
  4003cc:	48 8b 05 25 0c 20 00 	mov    rax,QWORD PTR [rip+0x200c25]        # 600ff8 <_DYNAMIC+0x1d0>
  4003d3:	48 85 c0             	test   rax,rax
  4003d6:	74 05                	je     4003dd <_init+0x15>
  4003d8:	e8 43 00 00 00       	call   400420 <__libc_start_main@plt+0x10>
  4003dd:	48 83 c4 08          	add    rsp,0x8
  4003e1:	c3                   	ret    

Disassembly of section .plt:

00000000004003f0 <printf@plt-0x10>:
  4003f0:	ff 35 12 0c 20 00    	push   QWORD PTR [rip+0x200c12]        # 601008 <_GLOBAL_OFFSET_TABLE_+0x8>
  4003f6:	ff 25 14 0c 20 00    	jmp    QWORD PTR [rip+0x200c14]        # 601010 <_GLOBAL_OFFSET_TABLE_+0x10>
  4003fc:	0f 1f 40 00          	nop    DWORD PTR [rax+0x0]

0000000000400400 <printf@plt>:
  400400:	ff 25 12 0c 20 00    	jmp    QWORD PTR [rip+0x200c12]        # 601018 <_GLOBAL_OFFSET_TABLE_+0x18>
  400406:	68 00 00 00 00       	push   0x0
  40040b:	e9 e0 ff ff ff       	jmp    4003f0 <_init+0x28>

0000000000400410 <__libc_start_main@plt>:
  400410:	ff 25 0a 0c 20 00    	jmp    QWORD PTR [rip+0x200c0a]        # 601020 <_GLOBAL_OFFSET_TABLE_+0x20>
  400416:	68 01 00 00 00       	push   0x1
  40041b:	e9 d0 ff ff ff       	jmp    4003f0 <_init+0x28>

Disassembly of section .plt.got:

0000000000400420 <.plt.got>:
  400420:	ff 25 d2 0b 20 00    	jmp    QWORD PTR [rip+0x200bd2]        # 600ff8 <_DYNAMIC+0x1d0>
  400426:	66 90                	xchg   ax,ax

Disassembly of section .text:

0000000000400430 <_start>:
  400430:	31 ed                	xor    ebp,ebp
  400432:	49 89 d1             	mov    r9,rdx
  400435:	5e                   	pop    rsi
  400436:	48 89 e2             	mov    rdx,rsp
  400439:	48 83 e4 f0          	and    rsp,0xfffffffffffffff0
  40043d:	50                   	push   rax
  40043e:	54                   	push   rsp
  40043f:	49 c7 c0 f0 05 40 00 	mov    r8,0x4005f0
  400446:	48 c7 c1 80 05 40 00 	mov    rcx,0x400580
  40044d:	48 c7 c7 26 05 40 00 	mov    rdi,0x400526
  400454:	e8 b7 ff ff ff       	call   400410 <__libc_start_main@plt>
  400459:	f4                   	hlt    
  40045a:	66 0f 1f 44 00 00    	nop    WORD PTR [rax+rax*1+0x0]

0000000000400460 <deregister_tm_clones>:
  400460:	b8 3f 10 60 00       	mov    eax,0x60103f
  400465:	55                   	push   rbp
  400466:	48 2d 38 10 60 00    	sub    rax,0x601038
  40046c:	48 83 f8 0e          	cmp    rax,0xe
  400470:	48 89 e5             	mov    rbp,rsp
  400473:	76 1b                	jbe    400490 <deregister_tm_clones+0x30>
  400475:	b8 00 00 00 00       	mov    eax,0x0
  40047a:	48 85 c0             	test   rax,rax
  40047d:	74 11                	je     400490 <deregister_tm_clones+0x30>
  40047f:	5d                   	pop    rbp
  400480:	bf 38 10 60 00       	mov    edi,0x601038
  400485:	ff e0                	jmp    rax
  400487:	66 0f 1f 84 00 00 00 	nop    WORD PTR [rax+rax*1+0x0]
  40048e:	00 00 
  400490:	5d                   	pop    rbp
  400491:	c3                   	ret    
  400492:	0f 1f 40 00          	nop    DWORD PTR [rax+0x0]
  400496:	66 2e 0f 1f 84 00 00 	nop    WORD PTR cs:[rax+rax*1+0x0]
  40049d:	00 00 00 

00000000004004a0 <register_tm_clones>:
  4004a0:	be 38 10 60 00       	mov    esi,0x601038
  4004a5:	55                   	push   rbp
  4004a6:	48 81 ee 38 10 60 00 	sub    rsi,0x601038
  4004ad:	48 c1 fe 03          	sar    rsi,0x3
  4004b1:	48 89 e5             	mov    rbp,rsp
  4004b4:	48 89 f0             	mov    rax,rsi
  4004b7:	48 c1 e8 3f          	shr    rax,0x3f
  4004bb:	48 01 c6             	add    rsi,rax
  4004be:	48 d1 fe             	sar    rsi,1
  4004c1:	74 15                	je     4004d8 <register_tm_clones+0x38>
  4004c3:	b8 00 00 00 00       	mov    eax,0x0
  4004c8:	48 85 c0             	test   rax,rax
  4004cb:	74 0b                	je     4004d8 <register_tm_clones+0x38>
  4004cd:	5d                   	pop    rbp
  4004ce:	bf 38 10 60 00       	mov    edi,0x601038
  4004d3:	ff e0                	jmp    rax
  4004d5:	0f 1f 00             	nop    DWORD PTR [rax]
  4004d8:	5d                   	pop    rbp
  4004d9:	c3                   	ret    
  4004da:	66 0f 1f 44 00 00    	nop    WORD PTR [rax+rax*1+0x0]

00000000004004e0 <__do_global_dtors_aux>:
  4004e0:	80 3d 51 0b 20 00 00 	cmp    BYTE PTR [rip+0x200b51],0x0        # 601038 <__TMC_END__>
  4004e7:	75 11                	jne    4004fa <__do_global_dtors_aux+0x1a>
  4004e9:	55                   	push   rbp
  4004ea:	48 89 e5             	mov    rbp,rsp
  4004ed:	e8 6e ff ff ff       	call   400460 <deregister_tm_clones>
  4004f2:	5d                   	pop    rbp
  4004f3:	c6 05 3e 0b 20 00 01 	mov    BYTE PTR [rip+0x200b3e],0x1        # 601038 <__TMC_END__>
  4004fa:	f3 c3                	repz ret 
  4004fc:	0f 1f 40 00          	nop    DWORD PTR [rax+0x0]

0000000000400500 <frame_dummy>:
  400500:	bf 20 0e 60 00       	mov    edi,0x600e20
  400505:	48 83 3f 00          	cmp    QWORD PTR [rdi],0x0
  400509:	75 05                	jne    400510 <frame_dummy+0x10>
  40050b:	eb 93                	jmp    4004a0 <register_tm_clones>
  40050d:	0f 1f 00             	nop    DWORD PTR [rax]
  400510:	b8 00 00 00 00       	mov    eax,0x0
  400515:	48 85 c0             	test   rax,rax
  400518:	74 f1                	je     40050b <frame_dummy+0xb>
  40051a:	55                   	push   rbp
  40051b:	48 89 e5             	mov    rbp,rsp
  40051e:	ff d0                	call   rax
  400520:	5d                   	pop    rbp
  400521:	e9 7a ff ff ff       	jmp    4004a0 <register_tm_clones>

0000000000400526 <main>:
  400526:	55                   	push   rbp
  400527:	48 89 e5             	mov    rbp,rsp
  40052a:	48 83 ec 10          	sub    rsp,0x10
  40052e:	c7 45 f8 00 00 00 00 	mov    DWORD PTR [rbp-0x8],0x0
  400535:	c7 45 f4 09 00 00 00 	mov    DWORD PTR [rbp-0xc],0x9
  40053c:	c7 45 fc 01 00 00 00 	mov    DWORD PTR [rbp-0x4],0x1
  400543:	eb 15                	jmp    40055a <main+0x34>
  400545:	8b 45 fc             	mov    eax,DWORD PTR [rbp-0x4]
  400548:	83 c0 01             	add    eax,0x1
  40054b:	01 c0                	add    eax,eax
  40054d:	89 45 f8             	mov    DWORD PTR [rbp-0x8],eax
  400550:	8b 45 f8             	mov    eax,DWORD PTR [rbp-0x8]
  400553:	89 45 fc             	mov    DWORD PTR [rbp-0x4],eax
  400556:	83 6d f4 01          	sub    DWORD PTR [rbp-0xc],0x1
  40055a:	83 7d f4 00          	cmp    DWORD PTR [rbp-0xc],0x0
  40055e:	7f e5                	jg     400545 <main+0x1f>
  400560:	8b 45 f8             	mov    eax,DWORD PTR [rbp-0x8]
  400563:	89 c6                	mov    esi,eax
  400565:	bf 04 06 40 00       	mov    edi,0x400604
  40056a:	b8 00 00 00 00       	mov    eax,0x0
  40056f:	e8 8c fe ff ff       	call   400400 <printf@plt>
  400574:	b8 00 00 00 00       	mov    eax,0x0
  400579:	c9                   	leave  
  40057a:	c3                   	ret    
  40057b:	0f 1f 44 00 00       	nop    DWORD PTR [rax+rax*1+0x0]

0000000000400580 <__libc_csu_init>:
  400580:	41 57                	push   r15
  400582:	41 56                	push   r14
  400584:	41 89 ff             	mov    r15d,edi
  400587:	41 55                	push   r13
  400589:	41 54                	push   r12
  40058b:	4c 8d 25 7e 08 20 00 	lea    r12,[rip+0x20087e]        # 600e10 <__frame_dummy_init_array_entry>
  400592:	55                   	push   rbp
  400593:	48 8d 2d 7e 08 20 00 	lea    rbp,[rip+0x20087e]        # 600e18 <__init_array_end>
  40059a:	53                   	push   rbx
  40059b:	49 89 f6             	mov    r14,rsi
  40059e:	49 89 d5             	mov    r13,rdx
  4005a1:	4c 29 e5             	sub    rbp,r12
  4005a4:	48 83 ec 08          	sub    rsp,0x8
  4005a8:	48 c1 fd 03          	sar    rbp,0x3
  4005ac:	e8 17 fe ff ff       	call   4003c8 <_init>
  4005b1:	48 85 ed             	test   rbp,rbp
  4005b4:	74 20                	je     4005d6 <__libc_csu_init+0x56>
  4005b6:	31 db                	xor    ebx,ebx
  4005b8:	0f 1f 84 00 00 00 00 	nop    DWORD PTR [rax+rax*1+0x0]
  4005bf:	00 
  4005c0:	4c 89 ea             	mov    rdx,r13
  4005c3:	4c 89 f6             	mov    rsi,r14
  4005c6:	44 89 ff             	mov    edi,r15d
  4005c9:	41 ff 14 dc          	call   QWORD PTR [r12+rbx*8]
  4005cd:	48 83 c3 01          	add    rbx,0x1
  4005d1:	48 39 eb             	cmp    rbx,rbp
  4005d4:	75 ea                	jne    4005c0 <__libc_csu_init+0x40>
  4005d6:	48 83 c4 08          	add    rsp,0x8
  4005da:	5b                   	pop    rbx
  4005db:	5d                   	pop    rbp
  4005dc:	41 5c                	pop    r12
  4005de:	41 5d                	pop    r13
  4005e0:	41 5e                	pop    r14
  4005e2:	41 5f                	pop    r15
  4005e4:	c3                   	ret    
  4005e5:	90                   	nop
  4005e6:	66 2e 0f 1f 84 00 00 	nop    WORD PTR cs:[rax+rax*1+0x0]
  4005ed:	00 00 00 

00000000004005f0 <__libc_csu_fini>:
  4005f0:	f3 c3                	repz ret 

Disassembly of section .fini:

00000000004005f4 <_fini>:
  4005f4:	48 83 ec 08          	sub    rsp,0x8
  4005f8:	48 83 c4 08          	add    rsp,0x8
  4005fc:	c3                   	ret    

```












