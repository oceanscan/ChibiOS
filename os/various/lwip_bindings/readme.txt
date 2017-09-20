This directory contains the ChibiOS/RT "official" bindings with the lwIP
TCP/IP stack: http://savannah.nongnu.org/projects/lwip

In order to use lwIP within any ChibiOS/RT project, unzip the current PATCHED 
version of lwIP (found in the ./ext directory - just "unzip here") then in the
makefile:
    include $(CHIBIOS)/os/various/lwip_bindings/lwip.mk
    add $(LWSRC) to the CSRC variable
    add $(LWINC) to the INCDIR variable

The lwipopts.h file must be suitably configured and placed on the project's 
include path. File lwipopts_ex.h (in the $(CHIBIOS)/os/various/lwip_bindings
directory) is a useful starting point which explains many of the options. 

This version updated to work with lwIP V2.0.2

In the call to initialise lwip, it's possible to pass a structure which sets the
network address, addressing mode (static/DHCP) and host name (see example at end). Otherwise, default 
values from lwipthread.h and lwipopts.h are used. (Not recommended - possibility
of MAC address conflicts in the simplest configurations).

With the provided make file, a call to httpd_init(); in main() initialises the
simple web server to serve a single page.

Note that certain options increase the stack requirement set in lwipthread.h

The 'patched' version of lwIP is identical to the standard distribution version, 
except that there is a ChibiOS-specific modification to handle processor states in
    src/include/sys.h
    src/api/sockets.c

The bindings patch as described at http://www.chibios.com/forum/viewtopic.php?f=35&t=3866
has been added to lwipthread.c. (File lwipthread_prev.c is the same file
without the patch).


Example Initialisation
======================

#include "lwipthread.h"



const uint8_t localMACAddress[6] = {0x00, 0x0C, 0x22, 0x00, 0x00, 0x01 };

struct lwipthread_opts netOptions = { (uint8_t *)localMACAddress,
                                      IP4_ADDR_VALUE(192, 168, 1, 25),
                                      IP4_ADDR_VALUE(255, 255, 255, 0),
                                      IP4_ADDR_VALUE(192, 168, 1, 254),
                                      NET_ADDRESS_STATIC,
//                                      NET_ADDRESS_DHCP,
                                      "Test_host"
};


  lwipInit(&netOptions);           // Initialise network task.

  httpd_init();                    // Initialise web server if required/enabled



