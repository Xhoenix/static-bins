# README for GDBserver & GDBreplay by Stu Grossman and Fred Fish


## Introduction:

This is GDBserver, a remote server for Un\*x-like systems. It can be used to control the execution of a program on a target system from a GDB on a different host. GDB and GDBserver communicate using the standard remote serial protocol. They communicate via either a serial line or a TCP connection.

For more information about GDBserver, see the GDB manual:

https://sourceware.org/gdb/current/onlinedocs/gdb/Remote-Protocol.html

## Usage (server (target) side):

First, you need to have a copy of the program you want to debug put onto the target system. The program can be stripped to save space if needed, as GDBserver doesn't care about symbols. All symbol handling is taken care of by the GDB running on the host system.

To use the server, you log on to the target system, and run the `gdbserver` program. You must tell it **(a)** how to communicate with GDB, **(b)** the name of your program, and **(c)** its arguments.  The general syntax is:

```sh
target> gdbserver COMM PROGRAM [ARGS ...]
```

For example, using a serial port, you might say:

```sh
target> gdbserver /dev/com1 emacs foo.txt
```

This tells GDBserver to debug emacs with an argument of foo.txt, and to communicate with GDB via `/dev/com1`.  GDBserver now waits patiently for the
host GDB to communicate with it.

To use a TCP connection, you could say:

```sh
target> gdbserver host:2345 emacs foo.txt
```

This says pretty much the same thing as the last example, except that we are going to communicate with the host GDB via TCP. The `host:2345` argument means that we are expecting to see a TCP connection to local TCP port 2345. (Currently, the `host` part is ignored.) You can choose any number you want for the port number as long as it does not conflict with any existing TCP ports on the target system. This same port number must be used in the host GDB's `target remote` command, which will be described shortly. Note that if you chose a port number that conflicts with another service, GDBserver will print an error message and exit.

On some targets, GDBserver can also attach to running programs. This is accomplished via the --attach argument.  The syntax is:

```sh
target> gdbserver --attach COMM PID
```

PID is the process ID of a currently running process. It isn't necessary to point GDBserver at a binary for the running process.

## Usage (host side):

You need an unstripped copy of the target program on your host system, since GDB needs to examine it's symbol tables and such. Start up GDB as you normally would, with the target program as the first argument. (You may need to use the --baud option if the serial line is running at anything except 9600 baud.) i.e: `gdb TARGET-PROG`, or `gdb --baud BAUD TARGET-PROG`. After that, the only new command you need to know about is `target remote`. It's argument is either a device name (usually a serial device, like `/dev/ttyb`), or a HOST:PORT descriptor. For example:

```sh
	(gdb) target remote /dev/ttyb
```

communicates with the server via serial line `/dev/ttyb`, and:

```sh
	(gdb) target remote the-target:2345
```

communicates via a TCP connection to port 2345 on host `the-target`, where you previously started up GDBserver with the same port number. Note that for TCP connections, you must start up GDBserver prior to using the `target remote` command, otherwise you may get an error that looks something like `Connection refused`.

## Using GDBreplay:

A special hacked down version of GDBserver can be used to replay remote debug log files created by GDB. Before using the GDB "target" command to initiate a remote debug session, use "set remotelogfile \<filename\>" to tell GDB that you want to make a recording of the serial or tcp session. Note that when replaying the session, GDB communicates with GDBreplay via tcp, regardless of whether the original session was via a serial link or tcp.

Once you are done with the remote debug session, start GDBreplay and tell it the name of the log file and the host and port number that GDB should connect to (typically the same as the host running GDB):

```sh
	$ gdbreplay logfile host:port
```

Then start GDB (preferably in a different screen or window) and use the "target" command to connect to GDBreplay:

```sh
	(gdb) target remote host:port
```


Repeat the same sequence of user commands to GDB that you gave in the original debug session. GDB should not be able to tell that it is talking to GDBreplay rather than a real target, all other things being equal. Note that GDBreplay echos the command lines to stderr, as well as the contents of the packets it sends and receives. The last command echoed by GDBreplay is the next command that needs to be typed to GDB to continue the session in sync with the original session.
