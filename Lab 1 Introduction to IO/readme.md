# Lab 1: Introduction to I/O

## Preparation

You will need either a TM4C123 or an MSP432 LaunchPad and an MK-II BoosterPack. There is an option to performing this lab without the MK-II BoosterPack.

## Overview

The Lab 1 starter project using the LaunchPad and the Educational BoosterPack MK-II (BOOSTXL-EDUMKII) is a fitness device. It inputs from the microphone, accelerometer, light sensor and buttons. It performs some simple measurements and calculations of steps, sound intensity, and light intensity. It outputs data to the LCD and it generates simple beeping sounds. Figure Lab1.1 shows the data flow graph of Lab 1. Your assignment in Lab 1 is to increase the rate of Task0 from 10 to 1000 Hz.

[Figure Lab1.1.]  
*Data flow graph of Lab 1.*

This simple fitness device has six tasks. Normally, one would use interrupts to create real-time periodic events. However, Lab 1 will run without interrupts to illustrate the need for an operating system to manage multiple tasks that are only loosely connected. A very poorly constructed main program runs four of the tasks at about 10 times a second and the other two tasks at about once a second. One of the best ways to see how the six tasks fit together is to understand the data being passed.

- Task0: microphone input measuring RMS sound amplitude running at 10 Hz
  1. Reads sound from microphone (ADC)  
  2. Sends SoundData to Task4  
  3. Sends SoundRMS to Task5  
- Task1: acceleration input measuring steps running at 10 Hz
  1. Reads x,y,z acceleration (ADC)
  2. Sends AlgorithmState to Task3
  3. Sends Magnitude, EWMA to Task4
  4. Sends Steps to Task5
- Task2: light input measure average light intensity running at 1 Hz
  1. Reads light from sensor (I2C)
  2. Sends LightData to both Task4 and Task5
- Task3: input from switches, output to buzzer running at 10 Hz
  1. Inputs from Buttons (GPIO)
  2. Sends PlotState to Task4
  3. Outputs to Buzzer (PWM)
  4.Outputs to LED (GPIO)
- Task4: plotting output to LCD running at 10 Hz
  1. Receives SoundData, Magnitude, EWMA, LightData, PlotState
  2. Outputs to LCD (SSI)
  3. Task5: numerical output to LCD running at 1 Hz
  4. Receives SoundRMS, Steps, LightData
  5. Outputs to LCD (SSI)

The main program manages these six tasks. We can define the real-time performance of this manager by measuring the time between execution of tasks. For example, the grader will measure when Task0 is started for the first n times it is run, T0,i, for i=0 to n-1. From these measurements we calculate the time difference between starts. ΔT0,i = T0,i - T0,i-1, for i = 1 to n-1. Each task has a desired rate. Let Δtj be the desired time between executions for task j. The grader will generate these performance measures for each for Task j, j=0 to 5:  
  Minj = minimum ΔTj  
  Maxj = maximum ΔTj  
  Jitterj = Maxj - Minj  
  Avej = Average ΔTj  
  Errj = 100*( Avej - Δtj)/ Δtj  

In addition to the above quantitative measures, you will be able to visualize the execution profile of the system using a logic analyzer. Each task in Lab 1 toggles both a virtual logic analyzer and a real logic analyzer when it starts. For example, Task0 calls TExaS_Task0(). The first parameter to the function TExaS_Init() will be GRADER or LOGICANALYZER. Calling TExaS_Task0() in grader mode performs the lab grading. However in logic analyzer mode, these calls implement the virtual logic analyzer and viewed with TExaSdisplay. Figure Lab1.2 shows a typical profile measured with the TExaS logic analyzer.

[Figure Lab1.2.]  
*Task profile measured on the Lab 1 solution using TExaS. Notice Task0 is running about every 1ms. Notice Tasks 1, 3, and 4 are running about every 100ms. Notice that Task 0 looses samples whenever Tasks 2 and 5 run.*

[Figure Lab1.3.]  
*Task profile measured on the Lab 1 solution using TExaS. Notice Tasks 2 and 5 are running about every 1s.*

[Figure Lab1.4]  
*Shows the grader output of a typical solution to Lab 1.*

[Figure Lab1.4.]  
*Grader output for the Lab 1 solution.*

Notice the software toggles the logic analyzer pin every time the thread runs. This results in a logic analyzer frequency that is 1/2 the thread frequency. I.e., a 500 Hz squarewave means the signal is toggled at 1000 Hz.
