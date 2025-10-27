# Lab-01
## 1. Development OS
### Windows 
Workstation pro with a VM downloaded from here is recomended.
### Linux 
Continue from step 2.
### Mac OS

## 2. Installation of missing packages
```bash
apt-get update
```
```bash
apt-get upgrade -y
```
```bash 
apt-get install -y bison flex gettext texinfo libncurses5-dev libncursesw5-dev gperf automake libtool pkg-config build-essential gperf genromfs libgmp-dev libmpc-dev libmpfr-dev libisl-dev binutils-dev libelf-dev libexpat-dev gcc-multilib g++-multilib picocom u-boot-tools util-linux chrony libusb-dev libusb-1.0.0-dev kconfig-frontends python3-pip
```
```bash
pip install esptool pyserial
```
### If you face problems installing esptool use pipx as follows
```bash
pipx upgrade esptool
```
```bash
pipx install --force esptool
```
```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
```
```bash
source ~/.bashrc
```

## 3. Installing the tool-chain
```bash
wget https://github.com/espressif/crosstool-NG/releases/download/esp-12.2.0_20230208/xtensa-esp32s3-elf-12.2.0_20230208-x86_64-linux-gnu.tar.xz
```
```bash
tar -xf xtensa-esp32s3-elf-12.2.0_20230208-x86_64-linux-gnu.tar.xz
```
```bash
mkdir /opt/xtensa
```
```bash
mv xtensa-esp32s3-elf/ /opt/xtensa/
```
```bash
echo "export PATH=\$PATH:/opt/xtensa/xtensa-esp32s3-elf/bin" >> ~/.bashrc
```
```bash
source ~/.bashrc
```

## 4. Installing development repositories
```bash
git clone --recurse-submodules https://github.com/radupascale/hectorwatch-nuttx
```
```bash
cd hectorwatch-nuttx
```
## 5. Compiling and running
```
cd ~/hectorwatch-nuttx/nuttx
```
```bash
./tools/configure.sh -l hacktorwatch:usbnsh
```
```bash
make -j$(nproc)
```
## 6. To flash the microcontroller 
- Press and hold the BOOT button
- Press RESET once
- Release the BOOT button
- Then flash the micro controller using the command below

```bash
make flash ESPTOOL_PORT=/dev/ttyACM0 ESPTOOL_BAUD=115200 ESPTOOL_BINDIR=../esp32s3-bins
```
- After you finish the flashing process Press the RESET button to change the mode from Download to Boot.

## 7. Connecting with the board
- Run the command below
```bash
 sudo picocom /dev/ttyACM0 -b 115200
```
- Press the Enter button 3 times to unblock the NSH console \\

N.B To see all configuration parameters you can use the command below:
```bash
./tools/configure.sh -h
```
## 8. Clean up operations
- make clean - removes object compiled files only \\
```bash
make clean
```
- make distclean - removes any file associated with the current configuration to reinitialize the build system from zero. \\
```bash
make distclean
```






# Lab-04

 Broker MQTT public:
 ```bash
 broker.hivemq.com
```

