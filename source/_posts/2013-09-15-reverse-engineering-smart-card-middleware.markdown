---
layout: post
title: "Smart card middleware reversing tricks"
date: 2013-09-15 00:30
comments: true
categories: re
---
In order for a smart card to be usable by a third party application, a vendor-supplied
driver or a middleware needs to be available. On the Windows platform, a smart card
middleware must follow the [Windows Smart Card Minidriver specification][1] which is
designed to present a consistent interface to the card. This is not always the case,
and card vendors sometimes implement custom and non-standard interfaces.

Since smart cards are designed to be tamper-resistant and secure, reverse 
engineering of the smart card itself can be time consuming and expensive so it 
is ofthen the best to take a look at the middleware. 

Identifying key middleware functions
------------------------------------

When faced with a smart card of a unknown specification,  the middleware functions
that directly communicate with the  card should be examined. The middleware is 
responsible for abstracting the card to the programmer and examining these 
abstractions gives further insight into the smart card's design. The smart card 
middleware is distributed as a DLL (Dynamic Loadable Library) file which exports
a specific set of functions defined by the minidriver specification.

Before accessing the card in any way, the middleware must be initialized. 
Initialization is done by calling [CardAcquireContext][2] function. 
*CardAcquireContext* must be exported by the middleware and is usually the only 
exported function. 

*CardAcquireContext* is responsible, amongst other tasks, for setting the function
pointers in the *CARD_DATA* structure. In the assembled code, this will usually be
represented by the series of the pointer assignment operations to the appropriate
offsets into the *[CARD_DATA][3]* structure. By loadin apropriate C header file 
for *CARD_DATA* structure in IDA, it will be able to determine the locations of 
specific functions: 
{% img /images/scard_data.png %}

From this, we can clearly determine what each function does (at least on the)
high level. 

Capturing data trafic
---------------------

middleware sends the APDU commands with specific parameters to the smart card and
the smart card responds with the APDU response. Capturing this data exchange can
help greatly in understanding the smart card and middleware design.  The APDU structure
is defined by the [ISO/IEC 7816-4 standard][4]. The command APDU contains a 
mandatory 4 byte long header and up to 255 bytes of data.
The response APDU is sent by the card to the reader and contains 2 byte status 
word and up to 255 bytes of data. . The command status __00 90__ in hexadecimal 
signifies that the command has been executed successfully. 

On the Windows operating systems, all smart card communication is done trough 
__WinSCard API__ which defines the low level smart card access function. The *[ScardTransmit][5]*
function sends the command APDUs to the card and returns the response. One solution 
for monitoring the data transmission from and to the cards is to intercept 
*SCardTransmit* function calls and inspecting the arguments and return values. 

I wrote a simple DLL that hooks *SCardTransmit* and records sent and received data.
The code is available on [my github repository][6] and precompiled binaries are
available [here][7]. During the communication between the Windows process and 
the smart card, a log file is created. Log file is named after the process. 
The transmitted data is prefixed with the __>>>__ symbol, while the received data
is prefixed with the __<<<__ symbol. Each data transmission is recorded in the 
log file immediately and file access is freed to combine the log analysis with 
the process instrumentation in the debugger.  

Here's the example dump:
```
Winscard!SCardTransmit:
>>> 00:A4:08:00:02:0F:02
<<< 6F:38:62:36:83:02:0F ... 90:00
Winscard!SCardTransmit:
>>> 00:B0:00:00:06
<<< 00:0D:02:00:63:00:90:00
Winscard!SCardTransmit:
>>> 00:B0:00:00:60
<<< 00:0D:02:00:63:00:0A ... 90:00
Winscard!SCardTransmit:
>>> 00:B0:00:60:09
<<< 00:53:43:11:06:02:00:53:43:90:00
Winscard!SCardTransmit:
>>> 00:A4:04:00:0B:A0:00:00:03:97:43:49:44:5F:01:00
<<< 6A:82
```

The first APDU command in the listing 4 has the instruction A4 which, by the 
[ISO/IEC 7816-4 standard][4], specifies the _SELECT FILE_ instruction responsible 
for selecting a particular elementary file on the smart card with the name __0F 02__.
The last two bytes of the response APDU are _90 00_ which indicates the success.
By enumerating all unique _SELECT FILE_ instructions executed, a list of the elementary
files present on the card can be created. The next three commands have the _INS_
byte set to __B0__ which corresponds to the _READ BINARY_ instruction. The _READ BINARY_
instruction can read the maximum of 255 bytes of data at a time so multiple 
requests with incrementing offsets would be needed to read a large file. The 
file can then be reconstructed by the log. The Last command is the _SELECT FILE_
instruction which response indicates an error has occurred. 

The extensive list of known command APDU instructions can be found in [this page on web archive][8].
Repackaging the sniffed data into something that Wireshark can eat would be 
useful but I'll leave that for the next time.

Peace out

 



[1]: http://msdn.microsoft.com/en-us/windows/hardware/gg487500.aspx Windows Smart Card Minidriver specification
[2]: http://msdn.microsoft.com/en-us/library/dd627600%28v=vs.85%29.aspx CardAcquireContext
[3]: http://msdn.microsoft.com/en-us/library/dd627628%28v=vs.85%29.aspx CARD_DATA
[4]: http://www.ttfn.net/techno/smartcards/iso7816_4.html 
[5]: http://msdn.microsoft.com/en-us/library/windows/desktop/aa379804%28v=vs.85%29.aspx
[6]: https://github.com/ea/smartcard-sniffer
[7]: https://github.com/ea/smartcard-sniffer/releases/download/v0.1/SmartcardSniffer-0.1.zip
[8]: http://web.archive.org/web/20090630004017/http://cheef.ru/docs/HowTo/APDU.info