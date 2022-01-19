# Lab 5: File System using a Solid-State Disk  
The goal of Lab 5 is to implement the write-once file system as described in Section 5.5.  

All OS_File operations will have read-modify-write critical sections. So in general, it would be necessary to use a mutex semaphore or StartCritical/EndCritical synchronization to make all OS_File operations atomic. However, in this simple lab, there is no OS and all OS_File operations are called from a single main() program. So in this lab, critical sections are no an issue.  
