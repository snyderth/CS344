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
## Filling the Address Struct: IP Address ##
```
struct sockaddr_in serverAddress;

serverAddress.sin_family = AF_INET;
serverAddress.sin_port = htons(7000); <= Changes endian-ness (LSB first/MSB first) because network is big-endian
serverAddress.sin_addr.s_addr = inet_addr("192.168.1.1");
```

## Filling the Address Struct: Domain Name ##
* Connecting to server:
```
struct sockaddr_in serverAddress;
struct hostent* serverHostInfo;

serverHostInfo = gethostbyname("www.oregonstate.edu"); <= Do a DNS lookup and return address information
if(serverHostInfo == NULL){
    fprintf(stderr, "could not resolve server host name\n");
    exit(1);
}

serverAddress.sin_family = AF_INET;
serverAddress.sin_port = htons(80);

memcpy((char*)&serverAddress.sin_addr.s_addr,
        (char*)... See slides
```
## Sending Data ##
```
ssize_t send(int sockfd, void *messsage, size_t message_size, int flags);

char msg[1024];
...
r = send(socketFD, msg, 1024, 0);
if (r < 1024){//handle error}
```


## Sending Data ##
* Send will block until all the data has been sent, the connection goes away, or a signal handler interrupts the `write()` system call
* Remember that internet connections fail all the time
    * Client intentionally disconnects (STOP button in a web browser)
    * Network failure
    * etc.

* You MUST check if you have sent all the data. If not all was sent, you have to send the number of bytes that have not been sent

## Receiving Data ##
```
ssize_t recv(int sockfd, void *buffer, size_t buffer_size, int flags);


char buffer[1024];
memset(buffer, '\0', sizeof(buffer));
r = recv(socketFD, buffer, sizeof(buffer) -1, 0);
if( r < sizeof(buffer) -1) {//handle possible error}
```



## Receiving Data ##
* Data may arrice in odd size bundles
* `recv()` or `read()` will return exactly the amount of data that has already arrived
* `recv()` and `read()` will block if the connnection is open, but no data is available



## Receiving Data - Using Control Codes ##
* Similar to controlling data being sent through pipes, you can watch for the amount of data coming through `recv()` if you know how much there should be
* See the slides for a receive algorithm



## Debugging the Contents of Buffers ##
* Often when writing send and receive funcitons, you will get garbage. Check the buffer via the algorithm shown in slides


