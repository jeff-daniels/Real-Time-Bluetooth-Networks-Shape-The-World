# Lab 5: File System using a Solid-State Disk  
The goal of Lab 5 is to implement the write-once file system as described in Section 5.5.  

All OS_File operations will have read-modify-write critical sections. So in general, it would be necessary to use a mutex semaphore or StartCritical/EndCritical synchronization to make all OS_File operations atomic. However, in this simple lab, there is no OS and all OS_File operations are called from a single main() program. So in this lab, critical sections are no an issue.  



Even though the previous approaches were indeed simple, we can simplify the file system even more if we make the following usage restrictions/specifications:

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

One sector will be reserved for the operating system to manage the directory and allocation scheme and the other 255 sectors will contain data. When the program is loaded into flash, the entire flash is erased. This erase event will serve to "format" the disk. All 255 data sectors will be free and the file system will have no files.

While using this disk we could have 255 individual files, each with one sector. We could have 51 files each with 5 sectors. Alternately, we could have one file with 255 sectors. Any combination is possible where the number of files is less than or equal to 255, and the total allocated sectors is also less than or equal to 255.

There will be a function, OS_File_New, which will return the file number of an empty file. This function will fail if there are no more files left, because there are already 254 files created, or if there are no free sectors, because the disk is full.

`//********OS_File_New*************  `  
`// Returns a file number of a new file for writing  `  
`// Inputs: none  `  
`// Outputs: number of a new file  `  
`// Errors: return 255 on failure or disk full  `  
`uint8_t OS_File_New(void);`  

To check the status of a file, we can call OS_File_Size. This function returns the number of sectors allocated to this file. If the size is zero, this is an empty file.

`//********OS_File_Size*************  `  
`// Check the size of this file  `  
`// Inputs: num, 8-bit file number, 0 to 254 `   
`// Outputs: 0 if empty, otherwise the number of sectors  `  
`// Errors: none  `  
`uint8_t OS_File_Size(uint8_t num);  `  

To write data to an existing file we need to specify the file number into which we will store the data. The write data function will allocate another sector to the file and append 512 bytes of new data to the file. The input parameters to OS_File_Append are the file number and a sector of 512 bytes of data to write. This function will fail if there are no free sectors, because the disk is full.

`//********OS_File_Append*************`  
`// Save 512 bytes into the file`  
`// Inputs: num, 8-bit file number, 0 to 254`  
`//         buf, pointer to 512 bytes of data`  
`// Outputs: 0 if successful`  
`// Errors: 255 on failure or disk full`  
`uint8_t OS_File_Append(uint8_t num, uint8_t buf[512]);`  

To read data from a file we call OS_File_Read. The three parameters to this function are the file number, the location, and a pointer to RAM. The location parameter defines the logical address of the data in a file. Location 0 will access the first sector of the file. For example, if a file has 5 sectors, the location parameter could be 0, 1, 2, 3, or 4. The read data function will copy 512 bytes of data from the file into the RAM buffer. This function will fail if this file does not have data at this location.

`//********OS_File_Read*************`  
`// Read 512 bytes from the file`  
`// Inputs: num, 8-bit file number, 0 to 254`  
`//         location, logical address, 0 to 254`  
`//         buf, pointer to 512 empty spaces in RAM`  
`// Outputs: 0 if successful`  
`// Errors: 255 on failure because no data`  
`uint8_t OS_File_Read(uint8_t num, uint8_t location,`  
`      uint8_t buf[512]);`  

We will load into RAM versions of the directory and the FAT when the system starts. When we call OS_File_Flush the RAM versions will be stored onto the disk. Notice that due to the nature of how this file system is designed, bits in the directory and FAT never switch from 0 to 1. We can either call this function periodically or call it once just before the system is shut down.

`//********OS_File_Flush*************`  
`// Update working buffers onto the disk`  
`// Power can be removed after calling flush`  
`// Inputs:  none`  
`// Outputs: 0 if success`  
`// Errors:  255 on disk write failure`  
`uint8_t OS_File_Flush(void);`  

During the software download, the flash is erased. When the flash is erased, the disk in essence is formatted, because we defined the all ones state as empty. However, if one wishes to erase the entire disk removing all data and all files, one could call OS_File_Format. This function will erase the flash from 0x00020000 to 0x0003FFFF. You will not need to implement this function in Lab 5. However, on the TM4C123, it could be implemented by erasing all blocks from 0x00020000 to 0x0003FFFF. Notice that this implementation skips the eDisk layer and directly calls the physical layer.

`//********OS_File_Format*************`  
`// Erase all files and all data`  
`// Inputs: none`  
`// Outputs: 0 if success`  
`// Errors: 255 on disk write failure`  
`uint8_t OS_File_Format(void){`  
`uint32_t address;`  
`  address = 0x00020000; // start of disk`  
`  while(address <= 0x00040000){`  
`    Flash_Erase(address); // erase 1k block`  
`    address = address+1024;`  
`  }`  
`}`  
