
# Lab 2: Thread Management

## Objectives

- Develop debugging skills using the Keil debugger and TExaS logic analyzer  
- Understand the two existing round robin preemptive schedulers presented in Chapter 2  
- Appreciate the distinction between real-time and non-real time tasks  
- Develop a scheduler that runs two periodic event threads and four main threads  
- Implement spin-lock semaphores and a mailbox  

## Overview

We want you to understand how an RTOS works and demonstrate your understanding by completing a set of activities. The Lab 2 starter project using the LaunchPad and the Educational BoosterPack MKII (BOOSTXL-EDUMKII) is again a fitness device. However, the starter project will not execute until you implement a very simple RTOS. The user code inputs from the microphone, accelerometer, temperature sensor and switches. It performs some simple measurements and calculations of steps, sound intensity, and temperature. It outputs data to the LCD and it generates simple beeping sounds. Figure Lab2.1 shows the data flow graph of Lab 2. Your assignment is to first understand the concepts of the chapter in general and the projects RTOS_xxx and RoundRobin_xxx in specific. Your RTOS will run two periodic threads and four main threads. Sections 2.3.1 – 2.3.6 develops an RTOS that runs three main threads and your system must run four main threads. Section 2.3.7 explains how to extend the Scheduler() function so that it also runs periodic tasks. Section 2.4.2 explains how to implement spinlock semaphores.

![Data flow graph](https://github.com/jeff-daniels/UTAustinX-Real-Time-Bluetooth-Networks-Shape-The-World/blob/main/Lab%202%20Thread%20Management/Lab2.1dataFlow.jpg)  
*Figure 2.1. Data flow graph of Lab 2.*

This simple fitness device has six tasks: two periodic and four main threads. Since we have two periodic threads to schedule, we could have used interrupts on two hardware timers to run the real-time periodic threads. However, Lab 2 will run with just SysTick interrupts to run two the periodic threads and to switch between the four main threads. These are the six tasks:

- Task0: event thread samples microphone input at 1000 Hz  
- Task1: event thread samples acceleration input at 10 Hz  
- Task2: main thread detecting steps and plotting at on LCD, runs about 10 Hz  
- Task3: main thread inputs from switches, outputs to buzzer  
- Task4: main thread measures temperature, runs about 1 Hz  
- Task5: main thread output numerical data to LCD, runs about 1 Hz  

Your RTOS manages these six tasks. We will use the same metrics as described as used in Lab 1, except jitter and error are only relevant for the two real-time event tasks:

- Minj = minimum ΔTj for Task j, j=0 to 5
- Maxj = maximum ΔTj for Task j, j=0 to 5
- Jitterj = Maxj - Minj for Task j, j=0 to 1
- Avej = Average ΔTj for Task j, j=0 to 5
- Errj = 100*( Avej - Δtj)/ Δtj for Task j, j=0 to 1

In addition to the above quantitative measures, you will be able to visualize the execution profile of the system using a logic analyzer. Each task in Lab 2 toggles both the virtual logic analyzer and a real logic analyzer when it starts. For example, Task0 calls TExaS_Task0(). The first parameter to the function TExaS_Init() will be GRADER or LOGICANALYZER. Just like Lab 1, calling TExaS_Task0() in grader mode performs the lab grading. However in logic analyzer mode, these calls implement the virtual logic analyzer and can be viewed with TExaSdisplay. The TExaS logic analyzer should be used during debugging.
