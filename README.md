# Can-Bus Firmware Update STM32



### Why?

We need in our car an easy way to update the nodes. The actual system (SWD) needs to take out each node and code it with the st-link.
**This, in race condition, needs too much time to be applied.**
The idea is to leave the nodes attached to the car, and with the help of the USBtin (CAN to USB interface), LAWICEL (serial protocol) and Microboot (software updater) we can easily update them.

### How

This is achievable with the help of a bootloader inside the memory of each node. 



# Bootloader

The bootloader is itself a program inside the memory of the microprocessor. Bootloaders usually provide a method of flashing new code to the device  and initialize the hardware before running the main program.

This is possible due to the fact that the bootloader and the user program are isolated from themselves.

![Bootloader memory](https://github.com/dongiac/Can-bus-Bootloader/blob/master/Images/Simple%20bootloader%20image.jpg)

At every reset (hardware or software) the bootloader runs and check if a new code is available.

![Bootloader General cycle](https://github.com/dongiac/Can-bus-Bootloader/blob/master/Images/Bootloader-System%20mcu.png)

### STM32F303K8 Bootloader

**Datasheet:** [stm32f303k8](https://www.st.com/resource/en/datasheet/stm32f303k8.pdf)

For this purpose, since we will use the Nucleo-stm32f303k8, we will see how is possible to use a bootloader for this Microprocessor. 

The first thing to know is that all the nucleo by the ST have their own bootloader by factory (Not only the nucleo, but also the single mcu made by ST). The chip it's the ARM Cortex-M4, nevertheless buying it from ST company, it will have some default setting. 

For us is impossible to modify this bootloader, the idea is to put a layer program in the flash memory before the user program.

![Nucleo memory map](https://github.com/dongiac/Can-bus-Bootloader/blob/master/Images/M4%20memory%20map.png)

From the image above we can see that the factory bootloader is for sure in the early memory pages from 0x0000 0000 to 0x0001 0000, and we can not access this location.

We will use the 64KBytes of the higher Flash Memory (from 0x0800 0000 to 0x0801 0000). 

In the Boot program, opening the linker script (Demo\Boot\STM32F303K8TX.ld) we will set the start from 0x0800 0000

Meanwhile in the Prog program, opening the linker script (Demo\Prog\STM32F303K8TX.ld) we will set the start 8KBytes later, so it starts from 0x0800 2000.



# Can-Bus



# What will we need?

**Hardware**

- Nucleo-stm32f303k8
- [USBtin](https://www.fischl.de/usbtin/) (CAN to USB interface)
- MCP2551 Can Transciever


**Software**

- [STM32CubeIDE](https://www.st.com/en/development-tools/stm32cubeide.html) (IDE, need to install EGit)
- GitHub Desktop
- [OpenBLT](https://www.feaser.com/openblt/doku.php) (Bootloader open source)
- Microboot (software to send code by OpenBLT's creator)



