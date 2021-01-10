# Can-Bus Firmware Update STM32

### Why?

We need in our car an easy way to update the nodes. The actual system (SWD) needs to take out each node and code it with st-link.
**This, in race condition, needs too much time to be applied.**
The idea is to leave the nodes attached to the car, and with the help of the USBtin (CAN to USB interface), LAWICEL (serial protocol) and Microboot (software updater) we can update them.

### How

This is achievable by changing the bootloader option, we'll see later on how this is possible.

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

# Bootloader

The bootloader is itself a program inside the memory of the microprocessor. Bootloaders usually provide a method of flashing new code to the device  and initialize the hardware before running the main program.

This is possible due to the fact that the bootloader and the user program are isolated from themselves.

![Bootloader memory](link)

Usually at every reset (hardware or software) the first thing is to check if is necessary to step in the bootloader.

![Bootloader General cycle](https://github.com/dongiac/Can-bus-Bootloader/blob/master/Images/Bootloader-System%20mcu.png)



### STM32F303K8 Bootloader

**Datasheet:** [stm32f303k8](https://www.st.com/resource/en/datasheet/stm32f303k8.pdf)



# Can-Bus







