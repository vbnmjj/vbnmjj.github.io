## hello.c 程序

                .file   "hello.c" ;代表源文件名
                .text
                .section        .rodata
        .LC0:
                .string "hello world"
                .text
                .globl  main
                .type   main, @function
        main:
        .LFB0:
                .cfi_startproc
                pushq   %rbp
                .cfi_def_cfa_offset 16
                .cfi_offset 6, -16
                movq    %rsp, %rbp
                .cfi_def_cfa_register 6
                subq    $16, %rsp
                movl    %edi, -4(%rbp)
                movq    %rsi, -16(%rbp)
                leaq    .LC0(%rip), %rax
                movq    %rax, %rdi
                movl    $0, %eax
                call    printf@PLT
                movl    $0, %eax
                leave
                .cfi_def_cfa 7, 8
                ret
                .cfi_endproc
        .LFE0:
                .size   main, .-main
                .ident  "GCC: (Debian 13.2.0-5) 13.2.0"
                .section        .note.GNU-stack,"",@progbits
