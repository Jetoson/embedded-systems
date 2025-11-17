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
### Bash
`.bash_profile` - este executat când se pornește un shell de login (ex: primul shell după logare);
`.bashrc` - este executat cand se pornește orice shell interactiv (ex: orice terminal deschis);
`.bash_logout` - este executat când shell-ul de login se închide.
`.bash_history` - memorează un istoric al comenzilor interactive rulate.

### QEMU
QEMU poate folosi un modul de nucleu, `KVM`, pentru a accelera rularea guest-ului, atunci când există suport pentru virtualizare în hardware.

QEMU poate rula în două moduri:
1. User-mode Emulation:- în care un executabil obișnuit (user-space), compilat pentru o arhitectură, este rulat pe o altă arhitectură.
Eg. ``` qemu-arm -cpu <procesor> <executabil> ```
2. System Emulation:- în care este emulat un sistem de calcul complet

#### Instalare QEMU
```
sudo apt update
```
```
sudo apt install qemu qemu-kvm qemu-system-arm qemu-utils
```













