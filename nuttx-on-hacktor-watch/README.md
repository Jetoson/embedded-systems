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


# Lab-05 [LVGL](https://lvgl.io/) 

## Initiate the build system from zero
```bash
make distclean
```

## Compile and run
```
cd ~/hectorwatch-nuttx/nuttx
```
```bash
./tools/configure.sh -l hacktorwatch:lvgl
```
```bash
make menuconfig
```
make sure the following settings are selected
→ Graphics Support → LVGL graphics library = [Y]
→ Graphics Applications → LVGL Demo Widgets = [Y]
→ Board Selection → Enable LCD or SPI display driver
→ CONFIG_INPUT_CST816S
→ CONFIG_ESP32S3_GPIO_IRQ
→ LV_USE_NUTTX_TOUCHSCREEN
```bash
make -j$(nproc)
```
## Flash in to the micro controller
- Press and hold the BOOT button
- Press RESET once
- Release the BOOT button
- Then flash the micro controller using the command below

```bash
make flash ESPTOOL_PORT=/dev/ttyACM0 ESPTOOL_BAUD=115200 ESPTOOL_BINDIR=../esp32s3-bins
```
- After you finish the flashing process Press the RESET button to change the mode from Download to Boot.

## Connect with the board
- Run the command below
```bash
 sudo picocom /dev/ttyACM0 -b 115200
```
- Press the Enter button 3 times to unblock the NSH console \\

### Exercise 0
#### LVGL simulator on your [linux](https://github.com/lvgl/lv_port_linux)
##### Configure the simulator
```bash
sudo apt install cmake libpkgconfig-perl libwayland-dev wayland-protocols
```
```bash
sudo apt-get install libxkbcommon-dev
```
```bash
git clone https://github.com/lvgl/lv_port_linux.git
```
```bash
cd lv_port_linux/
```
```bash
git submodule update --init --recursive
```
##### Compile the project
```bash
cmake -B build -DCONFIG=wayland
```
```bash
cmake --build build -j$(nproc)
```
##### Open the application
```bash
./build/bin/lvglsim
```

Copy the code below in place of the `src/main.c` and recompile the project
```C
/*******************************************************************
 *
 * main.c - LVGL simulator for GNU/Linux
 *
 * Based on the original file from the repository
 *
 * @note eventually this file won't contain a main function and will
 * become a library supporting all major operating systems
 *
 * To see how each driver is initialized check the
 * 'src/lib/display_backends' directory
 *
 * - Clean up
 * - Support for multiple backends at once
 *   2025 EDGEMTech Ltd.
 *
 * Author: EDGEMTech Ltd, Erik Tagirov (erik.tagirov@edgemtech.ch)
 *
 ******************************************************************/
#include <unistd.h>
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#include "lvgl/lvgl.h"
#include "lvgl/demos/lv_demos.h"

#include "src/lib/driver_backends.h"
#include "src/lib/simulator_util.h"
#include "src/lib/simulator_settings.h"

/* Internal functions */
static void configure_simulator(int argc, char **argv);
static void print_lvgl_version(void);
static void print_usage(void);

/* contains the name of the selected backend if user
 * has specified one on the command line */
static char *selected_backend;

/* Global simulator settings, defined in lv_linux_backend.c */
extern simulator_settings_t settings;

lv_obj_t * circle;
lv_obj_t *clock_label;
lv_obj_t * label_steps;
int total_steps = 0;

/**
 * @brief Configure simulator
 * @description process arguments recieved by the program to select
 * appropriate options
 * @param argc the count of arguments in argv
 * @param argv The arguments
 */
static void configure_simulator(int argc, char **argv)
{
    int opt = 0;

    selected_backend = NULL;
    driver_backends_register();

    const char *env_w = getenv("LV_SIM_WINDOW_WIDTH");
    const char *env_h = getenv("LV_SIM_WINDOW_HEIGHT");
    /* Default values */
    settings.window_width = atoi(env_w ? env_w : "240");
    settings.window_height = atoi(env_h ? env_h : "240");
}

static void cb_night_mode_event_handler(lv_event_t * e)
{
    lv_event_code_t code = lv_event_get_code(e);
    lv_obj_t * obj = lv_event_get_target_obj(e);
    if (code == LV_EVENT_VALUE_CHANGED) {
        LV_UNUSED(obj);

        if (lv_obj_get_state(obj) & LV_STATE_CHECKED) {
            lv_color_t color = {.red = 128, .green = 128, .blue = 128};

            lv_obj_set_style_bg_color(circle, color, 0);
        } else {
            lv_obj_set_style_bg_color(circle, lv_color_white(), 0);
        }
    }
}

static void update_clock(lv_timer_t *timer)
{
    time_t now = time(NULL);
    struct tm *tm_info = localtime(&now);

    char buf[16];
    strftime(buf, sizeof(buf), "%H:%M:%S", tm_info);

    lv_label_set_text(clock_label, buf);
}

static void hacktorwatch_init_watch_face(void)
{
    lv_obj_t * cb_night_mode; /* Checkbox to enable night mode */
    lv_obj_t * steps_row; /* Put the steps label and the checkbox in the same row */

    /* The main object which will be the parent of all the following objects */
    circle = lv_obj_create(lv_screen_active());

    lv_obj_set_style_radius(circle, LV_RADIUS_CIRCLE, 0); 
    /* The size of the watch display is 240 x 240 */
    lv_obj_set_size(circle, 240, 240);
    lv_obj_align(circle, LV_ALIGN_CENTER, 0, 0);

    /* A label to show the current time */
    clock_label = lv_label_create(circle);
    lv_obj_align(clock_label, LV_ALIGN_TOP_MID, 0, 10);
    lv_label_set_text(clock_label, "--:--:--");

    /* A checkbox that enables/disables the night mode */
    cb_night_mode = lv_checkbox_create(circle);
    lv_checkbox_set_text(cb_night_mode, "Night mode");
    lv_obj_add_state(cb_night_mode, 0);
    lv_obj_add_event_cb(cb_night_mode, cb_night_mode_event_handler, LV_EVENT_ALL, NULL);
    lv_obj_align(cb_night_mode, LV_ALIGN_LEFT_MID, 0, -30);

    /*
     * A row which contains two elements: the refresh button and the
     * text which displays the number of steps.
     */
    steps_row = lv_obj_create(circle);
    lv_obj_set_flex_flow(steps_row, LV_FLEX_FLOW_ROW);
    lv_obj_set_size(steps_row, lv_pct(90), lv_pct(30));
    lv_obj_align(steps_row, LV_ALIGN_BOTTOM_MID, 0, -30);

    lv_obj_t * btn_reset_steps = lv_button_create(steps_row);
    lv_obj_set_style_bg_image_src(btn_reset_steps, LV_SYMBOL_REFRESH, 0);
    lv_obj_align(btn_reset_steps, LV_ALIGN_BOTTOM_MID, 0, 0);

    label_steps = lv_label_create(steps_row);
    lv_label_set_text_fmt(label_steps, "Steps: %i", total_steps);
    lv_obj_align(label_steps, LV_ALIGN_CENTER, 0, 0);

    lv_obj_update_layout(circle);
}

int main()
{
    configure_simulator(NULL, NULL);

    /* Initialize LVGL. */
    lv_init();

    driver_backends_register();

    /* Initialize the configured backend */
    if (driver_backends_init_backend(selected_backend) == -1) {
        die("Failed to initialize display backend");
    }

    /* Enable for EVDEV support */
#if LV_USE_EVDEV
    if (driver_backends_init_backend("EVDEV") == -1) {
        die("Failed to initialize evdev");
    }
#endif

    hacktorwatch_init_watch_face();

    /* Update clock each second */
    lv_timer_create(update_clock, 1000, NULL);

    /* Enter the run loop of the selected backend */
    driver_backends_run_loop();

    return 0;
}
```
