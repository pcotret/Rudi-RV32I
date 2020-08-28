./c - files to support programming The Rudi-RV32I using C.

If you have the RISCV GCC toolset built and installed in /opt/riscv, you should be able to just run "make" to have test.c compiled and the resulting program and data converted into HDL in ../src/program_memory/.

It compiles the program, links it and then extracts the resulting .data and .text segments, and merges it into template_program_memory.vhd and template_ram_memory.vhd - ready to be included in your design.

The binary is linked such that RAM is located at 0x10000000 and program code at 0xF0000000. You need to reflect this in your system design.

Files in this directory are:

| README.md                   | This file                                                    |
| --------------------------- | ------------------------------------------------------------ |
| gen_mem.sh                  | Script to extract the .data and .text segments from a binary and convert to HDL |
| linker_script               | Custom linker script to control the placement of .rodata and sdata segments |
| Makefile                    | Makefile to compile "test.c" and build the required HDL      |
| template_program_memory.vhd | The Template used to create the HDL to infer the program (ROM) memory |
| template_ram_memory.vhd     | The Template uses to create the HDL to infer the RAM memory  |
| test.c                      | A short sample test program                                  |
| setup.s                     | Assembler to set up the C environment (i.e. set the stack pointer to the end of RAM) |

If you have any weirdness, check for missing sections by running:

    /opt/riscv/bin/riscv32-unknown-linux-gnu-objdump -s test

Everything should be in the .data or .text segments - oh, and .comment can be ignored)

## Test program

```c
int test_program(void) {
  puts("System restart\r\n");  

  /* Check some junk in memory */
  puts("String is ");
  putchar('0'+mylen(az));
  puts(" characters long\r\n");

  puts(az);
  putchar('0'+mylen(az));
  puts(bz);

  /* Run a serial port echo */
  *gpio_direction = 0xFFFF;
  while(1) {
    putchar(getchar());
    *gpio_value = *gpio_value + 1;
  }
  return 0;
}
```

- We should get some messages at the beginning ("System restart", "String is xx characters long").
- Normally, we should have a counter on LEDs:
  - LEDs 15-4 works fine.
  - Weird behavior on LEDs 3-0 depending on the key pressed in the terminal.