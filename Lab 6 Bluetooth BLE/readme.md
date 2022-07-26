# Lab 6: Bluetooth BLE  

## About Lab 6  
The objectives of Lab 6 are:
- Interface the 2650 BLE module to the LaunchPad  
- Develop a set of NPI message packets to support BLE communication  
- Connect the fitness device to a cell phone  
- Understand the concepts of service, characteristic, and advertising  

There are two aspects of Lab 6 that are graded.  First, you must implement the following 11 functions that produce NPI messages. These functions do not send the messages, they just create the message with the proper syntax. Prior to launching the fitness device, the grader will evaluate these functions.  
- BuildGetStatusMsg
- BuildGetVersionMsg
- BuildAddServiceMsg
- BuildRegisterServiceMsg
- BuildAddCharValueMsg
- BuildAddCharDescriptorMsg
- BuildAddNotifyCharDescriptorMsg
- BuildSetDeviceNameMsg
- BuildSetAdvertisementData1Msg
- BuildSetAdvertisementDataMsg
- BuildStartAdvertisementMsg

The second part of the grading involves Bluetooth communication.
