

The objectives of Lab 4 are to

    Rework scheduler to implement priority
    Add edge triggered interrupts to signal semaphores
    Signal semaphores from periodic interrupts
    Run event tasks using the regular scheduler

Lab 4
0:00 / 0:00

Press UP to enter the speed menu then use the UP and DOWN arrow keys to navigate the different speeds, then press ENTER to change to the selected speed.

Click on this button to mute or unmute this video or press UP or DOWN buttons to increase or decrease volume level.
Maximum Volume.
Downloads and transcripts
Video
Download video file
Transcripts

    Download SubRip (.srt) file
    Download Text (.txt) file

Lab 4 is an incremental improvement over Lab 3. In particular, you will add priority to the TCB. If all threads have equal priority, then the system runs as round robin. There will be two types of interrupts: periodic and edge-triggered. On the occurrence of the interrupt, the OS will simply signal one or more semaphores, and then run the scheduler. All threads, including event threads and main threads, are run by the scheduler. Event threads will be assigned high priority to assure low jitter and low latency.

Lab 4 uses the LaunchPad and the Educational BoosterPack MKII (BOOSTXL-EDUMKII). The official grader again implements the fitness device. However, this OS can also be deployed to run a hand-held video game. The video game will give you a perspective of how a user application can be controlled by a RTOS. Similar to Labs 1, 2, 3, you can earn a Lab 4 grade without the MKII using the BSPnotMKII.c version of the BSP layer.

Just like Labs 2 and 3, the starter project will not execute until you implement the necessary RTOS functions. We encourage you to reuse code from Lab 3. Identical to Lab 3, the user code inputs from the microphone, accelerometer, light sensor, temperature sensor and switches. It performs some simple measurements and calculations of steps, sound intensity, light intensity, and temperature. It outputs data to the LCD and it generates simple beeping sounds. Figure Lab4.1 shows the data flow graph of Lab 4. Your RTOS will run eight main threads. Tasks 0, 1, 3 are event threads. The periodic interrupt will signal semaphores for Task0 and Task1. An edge triggered interrupt will signal a semaphore for Task 3.

Figure Lab4.1. Data flow graph of Lab 4 (same as Lab 3).

This simple fitness device has eight tasks: eight main threads. Since you have two periodic threads to schedule, you could use one hardware timer to run both tasks, or you could use two hardware timers, one for each task. You will continue to use SysTick interrupts to switch between the six main threads. These are the eight tasks:

        Task0: event thread samples microphone input at 1000 Hz
        Task1: event thread samples acceleration input at 10 Hz (calls Put)
        Task2: main thread detecting steps and plotting at on LCD, runs about 10 Hz (calls Get)
        Task3: event thread inputs from switches, outputs to buzzer (calls Sleep)
        Task4: main thread measures temperature, runs about 1 Hz (calls Sleep)
        Task5: main thread output numerical data to LCD, runs about 1 Hz
        Task6: main thread measures light, runs about 1.25 Hz (calls Sleep)
        Task7: main thread that does no work

Thread 7, which doesn’t do any useful task, will never sleep or block. Just like Lab 3, this thread will make your RTOS easier to implement because you do not need to handle the case where all main threads are sleeping or blocked.

Your RTOS manages these eight tasks. We will use the same metrics as described as used in Lab 3:

       Minj = minimum ΔTj for Task j, j=0 to 5
       Maxj = maximum ΔTj for Task j, j=0 to 5
       Jitterj = Maxj - Minj for Task j, j=0 to 1
       Avej = Average ΔTj for Task j, j=0, 1, 3, 4, 5, and 6 (not 2 and 7)
       Errj = 100*( Avej - Δtj)/Δtj for Task j, j=0 to 1

In addition to the above quantitative measures, you will be able to visualize the execution profile of the system using a logic analyzer. Tasks 0 to 6 toggle both the virtual logic analyzer and a real logic analyzer when they start. For example, Task0 calls TExaS_Task0(). The first parameter to the function TExaS_Init() will be GRADER or LOGICANALYZER. Just like Labs 1-3, calling TExaS_Task0() in grader mode performs the lab grading. However, in logic analyzer mode, these calls implement the virtual logic analyzer and can be viewed with TExaSdisplay. At the start of each task it also toggles an actual pin on the microcontroller. For example, Task0 calls Profile_Toggle0(). You do not need a real logic analyzer, but if you have one, it can be used.
Lab 4 Logic Analyzer
0:00 / 0:00

Press UP to enter the speed menu then use the UP and DOWN arrow keys to navigate the different speeds, then press ENTER to change to the selected speed.

Click on this button to mute or unmute this video or press UP or DOWN buttons to increase or decrease volume level.
Maximum Volume.
Downloads and transcripts
Video
Download video file
Transcripts

    Download SubRip (.srt) file
    Download Text (.txt) file

A real-time system is one that guarantees the jitters are less than a desired threshold, and the averages are close to desired values. We expect the jitter for the two periodic tasks to be quite low. Your assignment is implement the OS functions in OS.c and write the SysTick interrupt service routine in osasm.s. We do not expect you to edit the user code in Lab4.c, the board support package in BSP.c, or the interface specifications in profile.h, Texas.h, BSP.h, or OS.h. More specifically, we are asking you to develop and debug a real-time operating system, such that

    Task0: jitter between executions should be less than or equal to 10us
    Task1: jitter between executions should be less than or equal to 30us
    Task2: average time between executions should be 100 ms within 5%
    Task3: runs at least three times during the grading period
    Task4: average time between executions should be less than 1.2 s
    Task5: average time between executions should be 1.0 s within 5%
    Task6: average time between executions should be less than 1.0 s
    Task7: no specifications

