# Security #
## Dual-mode operation ##
* You can think of the CPU as dual mode even though there are many levels of priveledges
* Sharing system resources requires the operating system to ensure that a program cannot arbitrarily interfere with other programs
* the hardware itself provides support to differentiate between at least two modes of operations:
    * user mode: execution programs
    * Monitor mode: OS level control
* WHen interrupt or fault occurs, the hardware switches to monitor mode via the _interrupt vector_ memory, which stores the _interrupt handler_ in the OS, whose functionality is defined by the OS

* The _mode bit_ is added to computer hardware to indicate teh current mode: monitor(0), user (1)
* When an interrupt or fault occurs, the hardware switches to monitor mode by following the address, stored in the _interrupt vector_, to the _interrupt handler_ function in the OS; this handler will let the OS decide what to do next

## I/O Protection ##
* All I/O instructions are privileged instructions
* Because: the OS must ensure that a user program could never gain control of the computer in monitor mode by storing a new address in the interrupt vector

## Mmeory Protection ##
* Must provide memory protection at least for the interrupt vector and the interrupt handler function
* In order to have memory protection  add two registers that determine the range of legal addresses a program may access 
    * __Base register__ holds the smallest legal phycsical memory address
    * __Limit register__ contains the size of the range
* The base and limit registers define a logical address space, which is virtualized for the process to start at address 0
* The base and limit registers are given to the processes to limit their address space
* When executing in monitor mode, the operating system has unrestricted access to both monitor and user's memory
* The load instructions for the base and limit registers are privileged instructions

## CPU Protection ##
* If the CPU is executing program instructions one after the next, how does the OS retain control?
* A timer interrupts the control flow after a specified period to ensure that the operating system has a chance to determine what to do
    * Timer is decremented every clock tick
    * When timer reaches the value 0, the interrupt vector is followed to the interrupt handler
* Timer commonly used to implement time sharing
* Also used to compute time    

## General System Architecture ##
* _System calls_ interact between the kernel and your program


## User Account Rights Protect Files ##
* User files are protected from other users by defining access based on user accounts 
* Is you are logged in as an account with access, you can manipulate the file

## Acting as a Different User - Pretexting ##
* If you want temp access as a different user, use `su`:
    * `su yoog`

* You can also execute just one action with the `sudo` command:
    * `sudo -u yoog rm -rf ~/yoogFiles/*`

* These commands change your effective user and/or group IDs, all of which can be displayed with the `id` command

## The root User Account ##
* Most UNIX systems have a super-user account, typically called root, which has permissions to do anything
    * `su root`
    * `sudo -u root pkill -u brewsteb`
* As root, you can change file ownership, change limits on how many processes users can run at once, add and delete user accounts, and other things

## SUID, SGID ##
* Each executable contains the security bits: SUID and SGID
    * SUID set means that the executable runs with effective user ID permissions of the _owner_ of the file
    * SGID set means that the executable runs with the effective user ID permissions of the _group owner_ of the file


* SUID bit can be set by chmod u+s && chmod u+x
    * Note: a capital S in the executable position means the SUID bit is set but the executable bit is not

* SUID allows you to give permission to users to do specific things with the executables you provide them. It gives limited access 

## `chmod` Revisited ##
See lecture slides for the mode bits of files that can be flipped by chmod


## Strongest forms of security ##
* Strongest forms of security invlove network and physical isolation, but these limit utility
* If you grant physical access to your computer - even disabling local login access
    * You still have to worry about:
        * Bootable devices can boot a different OS that can access the hard drive of you rcomputer
        * Hard drive could be stolen and read
        * Reading link-level NIC lights, keyboard EM
* With local logins, passwords = pain 


## Password Security ##
* Longer is better than more complicated
    * Lower case letters = 26 possibilities per char
    * Upper case letters = 26 possibilities per char
    * Numbers            = 10 possibilities
    * Special chars      = 30 possibilities
    * any char could by 1 of 92 choices
    * for an 8 character word, there are 8^92 combinations

* If you add four more characters and use only lower case, there are 26^12 combinations, much larger with a longer password
* A longer but more memorable password is more likely to be remembered and less likely to be written down

## Login Failures ##
* Brute force guessing algorithm can be stopped via placing a block on the user account if too many login attempts occur
* But this means the user that may actually need the account will then be blocked for a time
* If it takes a small amount of time to login, say a second, then the brute force will take forever, and thus is infeasible


## Password Encryption ##
* Passwords are encrypted via a hash
* It is hard to crack one way encryption such as the one-way hash, but storing the password file publicly can be insecure
    * The hacker can build a dictionary by incrementing the input to the hash, meaning the hacker could use these to search for and brute force your account


## Monitoring and Logs ##
* With all of hte insecure protocols, the log files need to be analyzed


## Getting Root Access ##
* First: The front door. Try base-level and default passwords
* Port scans and port/program insecurities
    * Attempting to connect to every port on the machine, which tells you which services are running on the machine

* Buffer overflows with system access
* Boot hacking

## Password Annihilator - with Physical Access ##
* Reset, change, or blank out any Windows password by booting from flash drive or CD
* There is a linux distro that allows you to change passwords with hardware access

## OS Internal Architecture - Booting with BIOS ##
* In old days, a computer would switch the BIOS -- Basic Input Output System
* Now UEFI is used (Unified Extensible Firmware Interface)
* The UEFI is a mini-OS that provides a ton of features

## Windows Internal Architecture ##
* Uesr programs can access the OS by using the Windows API
* The user has deep acces to lwo-level access of the GUI
* THe Registry: A hierarchical database of configuration settings for windows and applcations
    * USed by the OS itself

* Security Account Manager
    * An encrypted database that stores all the passwords
    * Can be accessed without windows

## Windows Boot Procedure ##
* Power On
* UEFI Program executed
* WIndows bootloader Winload.exe loads basic drivers required to read data from disk
* Kernel initialized
* Registry and non-biit drivers are loaded and started
* Winlogon.exe starts, requiring the user to login
* Explorer.exe is started
* Desktop window manager is started

## macOS Internal Architecture ##
* The UNIX concept and implementation of pipes allow data to move between many small interacting programs and also causes blocking system calls
* Around the 1980s, all these programs were consolidated to a single program and then it was slimmed down to only provide all necessary access to the hardware.
* New CPUs support a Memory Management Unit (MMU) allowing for virtual memory to be implemented
* Now memory and virtual memory does copy-on-write, meaning it only writes to disk if you try to write to the desk rather than virtual memory
* mascOS uses teh mach microkernel with teh BSD kernal
* Drivers are "kernel extensions" which are all loaded when the OS boots so trusted low-level code can be added to the kernel by third-parties

