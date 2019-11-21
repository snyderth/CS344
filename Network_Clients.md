# Network Clients #

## Berkeley Sockets ##
* Developed for BSD Unix under a DARPA grant
* The _de facto_ network communication method accross LAN and WAN
* Other transmission methods exist, but require different transport protocols

## Network Sockets ##
* Essentially two pipes
* When you write, you write byte by byte
* When you read, your read is destructive
* Sockets API treats the sockets as files, thus the `write()` and `read()` funcitons may be used for this

## Sockets show up as files ##
* The file descriptor number returned by `open()` is an index into an array of pointers to internal OS data structures
* Socets are added to this table of descriptors in the same way

## Multiple Process Communication ##
* Many differnet processes can be running on one computer
* AN IP address only identifies the interface on the computer, not the process
* How do we know which process is talking? __Ports__

## Ports ##
* A program can ask for a port, and TCP will direct packets to that port
* There are deignated ports from 0 to 1023 that are "well-known" ports that should not be used (are blocked by the OS)
* Port specifed at the end of the IP address: <IP>:<Port>, Ex: 125.232.121.222:43

## Socket Documentation ##
Process to create a socket:
1) Create the socket endpoint with `socket()`
2) Connect the socket to the server with `connect()`
3) Use `read()` and `write()`, or `send()` and `recv()` to tranfer data to and from the socket

```
int socket(int domain, int type, int protocol);
```
```
domain:     AF_INET/AF_UNIX <= Use AF_INET
type:       SOCK_STREAM (TCP) / SOCK_DGRAM (UDP)
protocol:   0 <= Could be used to get special behavior, but 0 is all that is needed
```
## Connecting the Socket to an address ##
```
int connect(int sockfd, struct sockaddr* address, size_t address_size);
```
```
if (connect(socketFD, (struct sockaddr*)&serverAddress, sizeof(serverAddress))){
    perror("Hull breach: connect()");exit(1);
}
```

