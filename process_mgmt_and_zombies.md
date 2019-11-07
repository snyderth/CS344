# Process Management #

## Running Processes ##
* `ps` tells us info on currently running processes


## Zombies? ##
* WHen a child process terminates but its parent does not wait for it
* Child processes must report to their parents before their reources will be released buy the OS
* If the parents aren't waiting for their children, the processes become the living undead
* The purpose of a zombie process is to retain the state that `wait()` can retrieve; they want to be harvested
* If you actually want printf to output to screen, you have to `fflush(stdout/stderr)` (if other processes are running) 
* Zombies handg arounded until they are `wait()`ed for by a parent

## Orphan Zombies ##
* If a parent process terminates without cleaning up its zombies, the zombies become orphan zombies
* Orphans are adopted by the `init` process (usually pid = 1) which periodically `wait()`s for orphans

##`kill`##
* This UNIX command is used to kill programs
* "kill" is a misnomer -- it really just sends signals
```
kill -TERM 1234
     ^signal ^pid
```
* The given PID affects who the signal is sent to
* If PID > 0, signal sent to process PID that is given
* PID == 0, the signal is sent to all processes in the same process group as the sender (i.e. all in your shell)
* PID < 0... more later
* the `KILL` signal will tell a process to immediately terminate with no clean-up

## `top` ##
* Runs interactive animated listing of all programs that are running on the machine in real time


## Diagnosing a Slow CPU ##
* The _uptime_ command shows you the average number of runnable processes over several different periods of time (the same info top displays)
* This shows the average number of runnablei (current running processes plus the queue of processes waiting to be run) or uninterruptable (waiting for IO) processes over the last 1, 5, and 15 minutes
* If the number of processes in your runnable queue is larger than the number of cores, your CPU is the bottleneck

## Job Control ##
* How do we start a program and still retain access to the command line?
* Can we run multiple processes at once? --> Job control

### Foreground/ Background###
* HTere can only be one shell __foreground__ process-it's the one you're currently interacting with
* If you're at the command prompt, then your foreground is the shell
* You can have processes running in the __background__ executing, but can also be in stopped states
* THere really isn't any differnce between processes in these two states, but a user cannot interact with background processes
* When a user enters a command that is intended to run in the foreground, the process started runs to completion before prompting the user again
* WHen a command is run, but the prompt is returned immediately, the process is a background process
* Terminal is not interrupted by a background command

## Start Backgrounded ##
```
$ ping www.oregonstate.edu &
```
* The ampersnad means to start in hte background and must be the last character
* Note that stdout and stderr are still going to the termianl for that process and stdin might be too if the shell is badly programmed

## Stopping a Process ##
* SEnding the `TSTP` signal stops (not terminates) a process, and puts it into the background
    * Control-z also sends this signal

* Use the `jobs` command to see what you're running

##`fg`##
* Manipulates teh foreground
* Use the job numbers provided by `jobs` to manipulate processes
* Bring job 1 from the background to the foreground and start it running again `fg %1`

##`bg`##
* STart a specific stopped program that is currently in the background and keep it in the background.
```
bg %1
```
* Start the most recently stopped background command:
```
bg
```
## `history`-a Command Visbility Utility ##
* Provides a listing of your previous commands
* 5 most recent: `history 5`


