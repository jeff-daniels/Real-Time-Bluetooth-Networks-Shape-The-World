# Real-Time-Bluetooth-Networks-Shape-The-World  
Lab work done to complete the UTAustinX class [Real-Time Bluetooth Networks-Shape the World](https://www.edx.org/course/real-time-bluetooth-networks-shape-the-world?index=undefined) is contained within this repository.  
## [Lab 1: Introduction to I/O](https://github.com/jeff-daniels/UTAustinX-Real-Time-Bluetooth-Networks-Shape-The-World/blob/main/Lab%201%20Introduction%20to%20IO/Lab1.c)    
The Lab 1 starter project using the LaunchPad and the Educational BoosterPack MK-II (BOOSTXL-EDUMKII) is a fitness device. It inputs from the microphone, accelerometer, light sensor and buttons. It performs some simple measurements and calculations of steps, sound intensity, and light intensity. It outputs data to the LCD and it generates simple beeping sounds. Figure Lab1.1 shows the data flow graph of Lab 1. Your assignment in Lab 1 is to increase the rate of Task0 from 10 to 1000 Hz.  
## [Lab 2: Thread Management](https://github.com/jeff-daniels/UTAustinX-Real-Time-Bluetooth-Networks-Shape-The-World/tree/main/Lab%202%20Thread%20Management)  
A scheduler was developed that runs two periodic event threads and four main threads.  The SysTick Interrupt handler was written in assembly code and branched to a C function that scheduled threads.  The main threads ran on a round robin scheduler.  Semaphores and mailboxes were also used to synchronize threads.  
## [Lab 3: Blocking, Sleeping and FIFO Queue](https://github.com/jeff-daniels/UTAustinX-Real-Time-Bluetooth-Networks-Shape-The-World/blob/main/Lab%203%20Blocking%2C%20Sleeping%20and%20FIFO%20Queue/Lab3.c)  
The semaphores were reworked from spinlock to blocking.  Periodic event tasks were implemented with dedicated timer interrupts.  A FIFO queue was developed to implement data flow from producer to consumer.  A sleeping mechanism was developed to recover wasted time.  The round-robin scheduler is capable of implementing blocking and sleeping.  
## [Lab 4: Priority Scheduler](https://github.com/jeff-daniels/UTAustinX-Real-Time-Bluetooth-Networks-Shape-The-World/blob/main/Lab%204%20Priority%20Scheduler/Lab4.c)  
Priority was added to the thread control block.  In addition to the periodic interrupts, edge-triggered interrupts are used.  On the occurrence of an interrupt, the operating system signals one or more semaphores and then runs the scheduler.  Assigning high priority to event threads reduces latency and jitter.  
## [Lab 5: File System using Solid-State Disk](https://github.com/jeff-daniels/UTAustinX-Real-Time-Bluetooth-Networks-Shape-The-World/blob/main/Lab%205%20File%20System%20using%20a%20Solid-State%20Disk/Lab5.c)  
A simple file system was created with the following usage restrictions/specifications:  
- The 128k flash memory is erased only once at the time of downloading the project; 
- The act of erasing the entire flash is equivalent to "formatting" the disk; 
- The disk is partitioned into 256 sectors of 512 bytes/sector; 
- We can append data to a file but cannot delete data or files; 
- We append data to a file in chunks of 512 bytes; 
- We will read data in a sequential fashion; 
- We assign file names as single 8-bit numbers (0 to 254); 
- We limit the file system to a maximum of 255 files; 
- We will mount (initialize the driver) the file system on startup; 
- We will call flush (backup to disk) the file system before powering down.  

This was done on four layers of abstraction:  
1. Physical layer with actions performed by the FlashProgram module.
2. Disk layer which is implemented by the eDisk module.  
3. File system layer implemented with the eFile module.  
4. Uppermost layer containing the main function.  

## [Lab 6: Bluetooth BLE](https://github.com/jeff-daniels/UTAustinX-Real-Time-Bluetooth-Networks-Shape-The-World/blob/main/Lab%206%20Bluetooth%20BLE/Lab6.c)  
Bluetooth stuff  
