# Can-Bus Firmware Update STM32



### Why?

We need in our car an easy way to update the nodes. The actual system (SWD) needs to take out each node and code it with the st-link.
**This, in race condition, needs too much time to be applied.**
The idea is to leave the nodes attached to the car, and with the help of the USBtin (CAN to USB interface), LAWICEL (serial protocol) and Microboot (software updater) we can easily update them.

### How

This is achievable with the help of a bootloader inside the memory of each node. 



# Bootloader

The bootloader is itself a program inside the memory of the microprocessor. Bootloaders usually provide a method of flashing new code to the device and initialize the hardware before running the main program.

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

### OpenBLT

This is an open source Bootloader, compatible with some microprocessor families.

![OpenBLT structure](https://www.feaser.com/openblt/lib/exe/fetch.php?w=900&tok=b1cce5&media=manual:openblt_architecture.png)

This is the general structure of OpenBLT.

The **Application Specific** have the function to modify the bootloader based on project specific needs. Here we can find the *main()* function, *bootloader configuration file* and *hook functions.* (Here we can enable the backdoor or handling watchdogs timers).

The **Target Independent** is the core of the bootloader, for our purpose. It handles the data transfer from the firmware file to the microcontroller's memory. *We don't have the need to modify this part.* This works with XCP protocol by ASAM, [here](https://www.asam.net/standards/detail/mcd-1-xcp/) can be found more information.

The **Target Dependent** is the only part that need some changes when porting the bootloader to a new microcontroller. It contains the low-level drivers for accessing the communication, timer and memory peripherals. Then there is a sub-part of the *target dependent* which is the **Compiler Specific**; It contains code such as the c-startup routine and the interrupt vector table, which typically needs a little compiler magic to link to the correct memory location.

![Detailed bootloader structure](https://www.feaser.com/openblt/lib/exe/fetch.php?w=700&tok=7a98bf&media=manual:openblt_modules.png)

# Can-Bus

The **Controller Area Network** is the protocol of a specific bus used mainly in the automotive field.

In our car, almost every node it's connected to the can-bus to have an easy and fast way to exchange information through the whole vehicle. It's based on a twisted pair of copper wire, with two 120 Ohm Resistor at both ends. 

### Nodes

Each node have three foundamental parts.

- CPU or Microprocessor
- Can controller (often an integral part of microprocessor)
- Can Transciever

![CAN NODE](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c0/CAN_Node.png/220px-CAN_Node.png)

> N.B. Each node can transmit and receive, but NOT SIMULTANEUOSLY 

### Communication

The CAN-Bus use two differential signals, CANH and CANL, rispectively High and Low state. Creating a "Dominant" bit when CANH>CANL and "Recessive" one in the other cases.  Having a Wired-AND convenction this will give the priority to the nodes with lower ID. The can is based on a CSMA/CD+AMP. CSMA stands for Carrier Sense Multiple Access, so each node have to wait for a prescribed amount of time of inactivity, before attempting to send a message. CD stands for Collision Detection, basically a letteral meaning and Arbitration on Message Priority (Lower ID nodes have the priority). 

**Doing an complete explanation of the can-bus is not the purpose of this project, but knowing what it is will explain why we are going to use it as the bus for updating our nodes** 

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



