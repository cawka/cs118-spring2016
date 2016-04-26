---
layout: page
title: Project 2
---

* toc
{:toc}

# Project 1: Simple TCP over UDP Sockets

## Revisions

* **April 22, 2016**: Initial version of the project description.


## Overview

The purpose of this project is to learn the basics of TCP protocol, including connection establishment and congestion control.
You will need to implement a simple data transfer client and server applications, using UDP sockets as an unreliable transport.
The goal of the project is to design and implement your own transport protocol headers on top of UDP to make it as a reliable transfer protocol.
Your client should receive data even though some packets are dropped or delayed.

## Task Description

The client and the server work in similar way as it did in the first project.
However, there are packet loss and delay on the link between them.
To make the link reliable, you need to implement some parts of TCP such as the sequence number, the acknowledgement, and the congestion control.
Once you get a reliable link, then the file transmission must be successful.

The project will be graded by testing transfer a single file over the unreliable link. 
Your second project does not need to be extended from your first project (if you extend, you will get extra credit, see below). 
Instead, you can simply implement a single threaded server that starts data transfer as soon as the connection is established.

## Basic Transmission over UDP

- In this project, you will be implementing a simple congestion window based reliable transport protocol similar to TCP.
 You must write one program implementing the client side, and another program implementing the server side. Only C/C++ are allowed to use in this project as you did in the first project.

- The client and the server will communicate using the User Datagram Protocol (UDP) socket, which does not guarantee data delivery.

- The initial window size starts from 1.

- The fixed retransmission timeout value 500 milliseconds (adaptive RTO for the extra credit)

- The client should establish a connection to the server using a three-way handshake.  During the handshake, the server has to send its initial sequence number to the client.

- After establishing the connection, the server should send the specified in the command-line file, dividing it into multiple packets and adding necessary header information. The maximum packet size should be 1 Kbytes (1024 bytes) bytes including all the headers.

- Command-line specification for client and server program:

        ./client SERVER-HOST-OR-IP PORT-NUMBER

        ./server PORT-NUMBER FILE-NAME

- You have to design your own transport layer header for this project.  It is up to you what information you want to include in the header (e.g., Sequence number, Acknowledge Number, and etc.), but you will at least need a sequence number field.  You are free to define what kind of messages you will require, and the format of the messages.
  
  **The designed packet format must be described in the project report.**

- Note that your programs will act as both a network application (file transfer program) as well as a reliable
transport layer protocol built over the unreliable UDP transport layer.


## Error Handling

Although using UDP does not ensure reliability of data transfer, the actual rate of packet loss or corruption in LAN
may be too low to test your program.  Therefore, we are going to emulate packet loss by using `tc` command in linux.

- The file transmission should be completed successfully, unless the packet loss rate is 100%.
- The timer on both the client and the server should work correctly to retransmit the lost packet.

## Congestion Control

Your server has to adjust the window size depending on the link status.

- *Slow start*: the window size will be doubled until it meets the slow start threshold value (`ssthresh`).
- *Congestion Avoidance*: the window size added by one if the window size is larger than `ssthresh`.
- If a packet is lost, the server has to adjust the window size and also the `ssthresh`.


## Hints

The best way to approach this project is in incremental steps.  Do not try to implement all of the functionality at once.

- First, assume there is no packet loss. Just have the server send a packet, the receiver respond with an ACK,
and so on.

- Second, introduce a large file transmission. This means you must divide the file into multiple packets and transmit the packets based on the current window size.

- Third, introduce packet loss.  Now you have to add a timer at the sender side for each packet. Also congestion control features should be implemented for the successful file transmission.

The credit of your project is distributed among the required functions. If you only finish part of the requirements, we still
give you partial credit. So please do the project incrementally.



## Requirements

- You must use an UDP socket for both sender and receiver.

- You should print messages to the screen when the server or the client is sending or receiving packets. There
are four types of output messages and should follow the formats below.

    * Server: Sending data packets

            "Sending data packet" [Sequence number] [CWND] [SSThresh]

        Example:

	        Sending data packet 5096 4 8 
	        Sending data packet 6000 4 8 
	        Sending data packet 6020 4 10

    * Server: Receiving ACK packets

	        "Receiving ACK packet" [ACK number]

        Example:
       
            Receiving ACK packet 5096
            Receiving ACK packet 6020

    * Client: Sending ACK packets

	        "Sending ACK packet" [ACK number]

        Example:

            Sending ACK packet 5096
            Sending ACK packet 6000
            Sending ACK packet 6020

    * Client: Receiving data packets 

            "Receiving data packet" [Sequence number]

        Example:

            Receiving data packet 5096
            Receiving data packet 6020

- The maximum packet size is 1 Kbytes (1024 bytes) including a header.

- The window size must be given in **the unit of bytes**, not in the unit of packet count. For example, if the window size is 5 Kbytes, then five packets can be transmitted simultaneously.

- The sequence number is given in **the unit of bytes** as well. The maximum sequence number should correspond to **30 Kbytes (30720 bytes)**. You have to reset back the sequence number when it reaches the maximum value.

- Packet retransmission should be triggered when the timer times out.

- Here are the default values for some variables.

    * Maximum packet length (including all your headers): **1 Kbyte**
    * Maximum sequence number: **30 Kbytes**
    * Initial window size: **1 Kbyte**
    * Initial slow start threshold: **8 Kbytes**
    * Retransmission time out value: **500 ms**


## Submission

To submit your project, you need to prepare:

1. All your source code and Makefile (no binaries) as `.tar.gz` archive.

2. A PDF project report that describes
   * the high level design of your server and client;
   * the problems your ran into and how you solved the problems;
   * additional instructions to build your project (if your project uses some other libraries);
   * how you tested your code and why.
   * the contribution of each team member (up to 3 members in one team) and their UID

Put all these above into a package and submit to CCLE.

Please make sure:

1. your code can compile
2. no unnecessary files in the package.

Otherwise, your will not get any credit.

## Grading

Your code will be first checked by a software plagiarism detecting tool. If we find any plagiarism, you will not get any credit.

Your code will then be automatically tested in some testing scenarios. If your code can pass all our automated test cases, you will get the full credit.

###  Bonus points

You can have:

* 1 extra point if you replace TCP transport in your project-1 with the transport you have implemented in this project
* 1 extra point if you implement RTT estimation and adaptive RTO, as defined in TCP specification
* 1 extra point if you implement TCP Reno fast retransmission and recovery

