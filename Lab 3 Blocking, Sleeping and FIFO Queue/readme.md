# Lab 3: Blocking, Sleeping and FIFO Queue

This bug only applies to the **BSPnotMKII.c** on the TM4C123 (not MSP432). In the **BSPnoMKII.c**, file, line 2,396 (the first instruction of **BSP_Time_Init()**), please add the line

   `sr = StartCritical();`

## Objectives

The objectives of Lab 3 are:

- Rework the semaphores from spinlock to blocking
- Implement period event tasks with dedicated timer interrupt(s)
- Develop a FIFO queue to implement data flow from producer to consumer
- Develop sleeping as a mechanism to recover wasted time
- Design a round-robin scheduler with blocking and sleeping

## Overview

As we progress through this class, your RTOS will become more and more complex. The Lab 3 starter project using the LaunchPad and the Educational BoosterPack MKII (BOOSTXL-EDUMKII) is again a fitness device. Just like Lab 2, the starter project will not execute until you implement the necessary RTOS functions. Consider reusing code from Lab 2. The user code inputs from the microphone, accelerometer, light sensor, temperature sensor and switches. It performs some simple measurements and calculations of steps, sound intensity, light intensity, and temperature. It outputs data to the LCD and it generates simple beeping sounds. Figure Lab3.1 shows the data flow graph of Lab 3. Your assignment is to first understand the concepts of the chapter in general and the software programs in specific. Your RTOS will run two periodic threads and six main threads. Section 3.1 develops cooperation using OS_Suspend. Section 3.2 develops blocking semaphores. Section 3.3 explains how to implement a first-in-first-out queue. Section 3.4 shows how to implement sleeping. Section 3.5 presents the means to run periodic tasks using the hardware timers.

![Data flow graph](https://github.com/jeff-daniels/UTAustinX-Real-Time-Bluetooth-Networks-Shape-The-World/blob/main/Lab%203%20Blocking%2C%20Sleeping%20and%20FIFO%20Queue/Lab3.1dataFlow.jpg)  
*Figure Lab3.1. Data flow graph of Lab 3.*

This simple fitness device has eight tasks: two periodic and six main threads. Since you have two periodic threads to schedule, you could use one hardware timer to run both tasks, or you could use two hardware timers, one for each task. You will continue to use SysTick interrupts to switch between the six main threads. These are the eight tasks:

- Task0: event thread samples microphone input at 1000 Hz
- Task1: event thread samples acceleration input at 10 Hz (calls Put)
- Task2: main thread detecting steps and plotting at on LCD, runs about 10 Hz (calls Get)
- Task3: main thread inputs from switches, outputs to buzzer (calls Sleep)
- Task4: main thread measures temperature, runs about 1 Hz (calls Sleep)
- Task5: main thread output numerical data to LCD, runs about 1 Hz
- Task6: main thread measures light, runs about 1.25 Hz (calls Sleep)
- Task7: main thread that does no work

Thread 7, which doesn’t do any useful task, will never sleep or block. Adding this thread will make your RTOS easier to implement because you do not need to handle the case where all main threads are sleeping or blocked.

Your RTOS manages these eight tasks. We will use the same metrics as described as used in Labs 1 and 2, except jitter and error are only relevant for the two real-time event tasks:

       Minj = minimum ΔTj for Task j, j=0 to 5
       Maxj = maximum ΔTj for Task j, j=0 to 5
       Jitterj = Maxj - Minj for Task j, j=0 to 1
       Avej = Average ΔTj for Task j, j=0 to 5
       Errj = 100*( Avej - Δtj)/Δtj for Task j, j=0 to 1

In addition to the above quantitative measures, you will be able to visualize the execution profile of the system using a logic analyzer. Tasks 0 to 6 toggle both the virtual logic analyzer and a real logic analyzer when they start. For example, Task0 calls **TExaS_Task0()**. The first parameter to the function **TExaS_Init()** will be **GRADER** or **LOGICANALYZER**. Just like Labs 1 and 2, calling **TExaS_Task0()** in grader mode performs the lab grading. However in logic analyzer mode, these calls implement the virtual logic analyzer and can be viewed with **TExaSdisplay**. Figure Lab3.2 shows the profile of one possible lab solution measured with the TExaS logic analyzer.

![Task Profile](https://github.com/jeff-daniels/UTAustinX-Real-Time-Bluetooth-Networks-Shape-The-World/blob/main/Lab%203%20Blocking%2C%20Sleeping%20and%20FIFO%20Queue/Lab3_2_logicAnalyzer.jpg)  
*Figure Lab3.2. Task profile measured on a solution for Lab 3 using TExaS (zoom out). Channels 0 and 3 are oscillating too fast to be observed at this time scale.*

![Task Profile](https://github.com/jeff-daniels/UTAustinX-Real-Time-Bluetooth-Networks-Shape-The-World/blob/main/Lab%203%20Blocking%2C%20Sleeping%20and%20FIFO%20Queue/Lab3_3_logicAnalyzer.jpg)  
Figure Lab3.3. Task profile measured on a solution for Lab 3 using TExaS (zoom in).

At the start of each task it also toggles an actual pin on the microcontroller. For example, Task0 calls Profile_Toggle0(). You do not need a real logic analyzer, but if you have one, it can be used.

## Specifications

A real-time system is one that guarantees the jitters are less than a desired threshold, and the averages are close to desired values. Now that we are using hardware timer interrupts we expect the jitter for the two event tasks to be quite low. For the six main threads, you will be graded only on minimum, maximum, and average time between execution of tasks. Your assignment is implement the OS functions in OS.c and write the SysTick interrupt service routine in osasm.s. We do not expect you to edit the user code in Lab3.c, the board support package in BSP.c, or the interface specifications in profile.h, Texas.h, BSP.h, or OS.h. More specifically, we are asking you to develop and debug a real-time operating system, such that

- Task0: jitter between executions should be less than or equal to 10µs
- Task1: jitter between executions should be less than or equal to 30µs
- Task2: average time between executions should be 100 ms within 5%
- Task3: average time between executions should be less than 50 ms
- Task4: average time between executions should be less than 1.2 s
- Task5: average time between executions should be 1.0 s within 5%
- Task6: average time between executions should be less than 1.0 s
- Task7: no specifications

### Typical Output
![Typical Output](https://github.com/jeff-daniels/UTAustinX-Real-Time-Bluetooth-Networks-Shape-The-World/blob/main/Lab%203%20Blocking%2C%20Sleeping%20and%20FIFO%20Queue/Lab3Output.jpg)  

If you have a real logic analyzer and are using the MSP432, open **profile.h** and change the #define for PROFILE6 from:

`#define PROFILE6 (*((volatile uint8_t *)(0x42000000+32*0x4C43+4*7))) /* Port 6.7 Output */`

to

`#define PROFILE6 (*((volatile uint8_t *)(0x42000000+32*0x4C03+4*3))) /* Port 2.3 Output */`
