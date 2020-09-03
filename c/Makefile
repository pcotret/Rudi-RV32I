ROOT_DIR=/opt/riscv_nz
CC=$(ROOT_DIR)/bin/riscv32-unknown-linux-gnu-gcc
AS=$(ROOT_DIR)/bin/riscv32-unknown-linux-gnu-as
COPTS= -Wall -pedantic 
LIB_VERSION:=$(shell ls $(ROOT_DIR)/lib/gcc/riscv32-unknown-linux-gnu/)

all : test
	./gen_mem.sh test
	rm setup.o test.o

test : test.o setup.o
	@echo "---------------------------------------------------------"
	@echo "-- Do not call anything main() - it will end up linked at"
	@echo "-- the start of the program, rather than the setup code"
	@echo "---------------------------------------------------------"
	$(ROOT_DIR)/bin/riscv32-unknown-linux-gnu-ld -Ttext 0xF0000000 -Tdata 0x10000000 -T linker_script setup.o test.o -o test $(ROOT_DIR)/lib/gcc/riscv32-unknown-linux-gnu/$(LIB_VERSION)/libgcc.a


setup.o : setup.s
	$(AS) -o setup.o --march=rv32i setup.s 

test.o : test.c
	$(CC) -c test.c $(COPTS)

clean:
	rm *.o test