# Lab-06
## QEMU & Tools
### Makefile 
```
hello: hello.c
	$(CROSS_COMPILE)gcc hello.c -o hello
```
```
$ make CROSS_COMPILE=aarch64-linux-gnu- hello
```
