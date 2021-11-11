
MODBUS

MODBUS has been the de facto industrial serial communication protocol since 1979. MODBUS describes a standard for reading and writing remote data registers. The later MODBUS TCP/IP spesification has ensured that the protocol is still widely used. It’s popularity can largely be attributed to the fact that it is royalty free and easy to implement.

This article gives a brief introduction to serial Modbus, and introduces fundamental terminologies and concepts that is applicable to Modbus in general.

The MODBUS Serial Line Protocol is a Master-Slave protocol. Only one Master node is connected to the serial bus, but several slaves nodes can be connected to the same serial bus. Communication is always initiated by the master. The slaves can only transmit data after receiving a request from the master.

A request message typically contains a request to read or write data. A response message typically returns data as an answer to a read request or confirms a write request.
MODBUS Data Types

The datatypes that can be adressed in a read or write request are:
Data type	Description
Coil	A writeable single bit
Discrete Input	A read only single bit
Holding Register	A writeable 16 bit register
Input Register	A read only 16 bit register
MODBUS Register Number Convention

The ranges in the table below refers to the most common way of referering to spesific coils and registers. A technical manual will usually refer to Holding Register Number 7 by simply refering to register 40007. It will refer to Input Register Number 4 by refering to register 30004 etc.
Data type	5 Decimal 	6 Decimal
Coil	1-9999 	1-99999
Discrete Input	10001-19999 (1nnnn)	100001-199999
Holding Register	40001-49999 (2nnnn)	400001-499999
Input Register	30001-39999 (3nnnn)	500001-599999
MODBUS Message Format

Now that you have familiarized yourselves with the basic modbus terminologies, it is time to introduce the basic components of a modbus message.
Address	A byte (1-247) used in every
request and response to identify the
slave node on a serial bus
Function 	A byte used in every request
and response to identify the type of
the request, e.g.:
0x03 – Read Holding Registers
0x10 – Write Multiple Registers
Function
Spesific
	Function spesific content that contains
e.g. a reference to a data location and
the value to be written.
16-bit data is allways sent big
endian, high byte before low byte.(*)
CRC	2 bytes containing a CRC checksum is
appendedto each modbus request and
response to ensure data integrity.
The CRC are allways sent litle endian,
low byte before high byte!

*) Some manufacturers do not use big endian for the data actually transferred. For example the 32 bit IEEE floating point can be represented in 4 ways since both the byte order and the (16 bit) word order can be threateed as both big and litle endian (1,2,3,4 vs 2,1,4,3 vs 3,4,1,2 vs 4,3,2,1).

MODBUS functions

The MODBUS spesification defines several functions. Modbus devices can support all or some of the functions listed in the table below. Other rare functions are defined as well as custom function codes that are product/vendor spesific.
01	Read Coils	Common
02	Read Discrete Inputs	Common
03	Read Multiple (Holding) Registers	Very common
04	Read Input Registers	Common
05	Write Single Coil	Common
06	Write Single Register	Common
07	Read Exception Status	Common
08	Diagnostics	Less common
0B
	Get Comm Event Counter	Less common
0C
	Get Comm Event Log 	Less common
0F
	Write Multiple Coils	Less common
10
	Write Multiple (Holding) Registers	Very Common
11	Report Slave ID	Less common
14	Read File Record	Less common
15	Write File Record	Less common
16	Mask Write Register 	Less common
17	Read/Write Multiple Registers	Less common
18	Read FIFO Queue	Less common
MODBUS message example

Example: Read registers 40100-40102 from node 5:

REQUEST:   05 03 00 63 00 02 XX XX
Corresponding to:
  05    - Slave ID: 5
  03    - Function Code: 3 - Read multiple registers
  00 63 - Address: 0x63 = 99 - Register 100
  00 02 - Number of registers: 2 
  XX XX - Checksum

RESPONSE:  05 03 04 13 88 88 B8 XX XX
Corresponding to:
  05    - As in request
  03    - As in request
  04    - Num bytes: 4 => 2 x 16 bit values follows
  13 88 - First register: 0x1388 = 5000
  88 B8 - Second register: 0x88B8 = 35000
  XX XX - Checksum

More MODBUS message examples

By playing around with the simple MODBUS generator you will be able to learn MODBUS without reading the manuals.
