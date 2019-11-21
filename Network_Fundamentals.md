# Network Fundamentals #

## IPC via Network Comms ##
* Network is a way for processes to communicate with each other not on the same machine
* __Client/Server__: A networking arrangement where a server is continuously waiting for new connections from other processes (clinets)
* __Client__: Process that initiates the connection, requesting a service
* __Server__: Process that is always running, waiting for new connections from client processes


## UNIX Daemons - a Type of Server ##
* A daemon is a long-running service that is started by the 1st process
    * `syslogd1`(system log daemon) maintains the system log
    * `lpd` (line printer daemon) manages print spooling
    * `dhcpd` (dynamoc host control protocol daemon) assigns TCP/IP configurations data to network clients that request it
    * `ntpd` (network tim eprotocol daemon) manages clock sync on a network

* A daemon are all children of init (pid == 1)
    * As of 2016, init is replaced with `systemd`

## Text and Non-text based Application Protocols ##
* HTTP is an example of text-based
* But ascii characters only take half of the bit space (0 to 127)
* Advantages of text-based:
    * Easy to debug
    * Easy to understand
* Disadvantages of text-based:
    * Not very efficient


## Network Layer Model ##

Layer | Common Protocols
------------------------
Application | HTTP
Transport | TCP and UDP
Network | IP
Link | Ethernet, 802.11
Physical | Twisted pair, radio, fiber


### Application ###
* The programs that run on the system. The programs that need to transmit data

### Transport ###
* Control how data is sent from one host to the other, regardless of number of nodes, hops, or networks the data passes through
* TCP: Transmission control protocol - connection-oriented, guarunteed, in-order data transport
* UDP: Universal Datagram Protocol - connectionless, not guaranteed

### Network ###
* Responsible for node to node communication along the path
* Determines how things are mapped out
* Defines addressing between nodes
* IP: Internet Protocol - naming, addressing, and routing from host to host and across networks
* Still independent of physical connection type

### Link ###
* Link layer is responsible for transporting between adjacent nodes in the network
* Ethernet: addressing and signaling protocol to use in modern networks (wired)
* 802.11 WIFI

### Physical ###
* Actual hardware used in the connections


## TCP Details ##
* TCP provides byte stream interface
* Connection oriented

## Applications On Top of TCP/IP ##
* TCP/IPI only passes bytes between processes - it does not interpret those bytes
* You write an "application protocol" on top of TCP/IP which defines what the bytes mean: This is what your program does with the bytes it sends & receives
* TCP/IP is like a phone connection
    * A phone transfers sound between two people
    * A phone does not interpret the meaning of the sound
## Internet Protocol Details ##
* IP specifies:
    * How a machine is addressed
    * IP address: 8 bit : 8 bit : 8 bit : 8 bit

* _interface:_ connection between host/router and physical link
    * Routers have multiple interfaces
    * A host typically has one or two interfaces

* _each_ interface has its own IP address; thus devices with more than one interface control multiple addresses

## IP 4 vs. 6 ##
* IP version 4 (IPv4) uses 32-bit addesses and has 2^32 unique addresses
* this is not enough for the world. IPv6 uses 128 bit addresses
* But there are a ton of devices, so we're moving to IPv6 slowly

## Back to Our Process ##
* A process can use just one interface to communicate with itself and other processes on the sam emachine
* Address network transmissions to your interface's own IP address, or the special hostname "localhost"
* Indicate which process you're talking yo by specifying the "port" 


