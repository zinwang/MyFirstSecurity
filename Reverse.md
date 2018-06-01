<br />

# PART1_程式編譯組譯與逆向(反編譯)
# c語言程式編譯與組譯
<br >

預處理階段
```
gcc -E test.c -o test.i
```
i的架構:<br />
[https://github.com/zinwang/MyFirstSecurity/blob/master/test.i](https://github.com/zinwang/MyFirstSecurity/blob/master/test.i)

<br />
編譯階段<br />
產生組語<br />
```
gcc -S test.i -o test.s
```

<br />
.s的架構<br />
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

組譯階段:<br />
```
gcc -C test.s -o test.o
```


產生AT&T語法格式的組語(gcc預設使用的格式)



gcc -S -masm=att XXXXX.c -o XXXXX_att.s


產生Intel語法格式的組語(微軟預設使用的格式)

gcc -S -masm=intel XXXXX.c -o XXXXX_intel.s

要去掉一堆註解:請加上參數-fno-asynchronous-unwind-tables

gcc -S -masm=intel XXXXX.c -o XXXXX_intel_OK.s -fno-asynchronous-unwind-tables
