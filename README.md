# MegaPi_RFID
Ultimate 2.0 MegaPi - ArduinoMega2560 code to connect with Adafruit RFID PN532 shield


- we are changing the code provided by Makeblocks to use the Adafruit RFID library.


Repos used: 
https://github.com/Makeblock-official/Makeblock-Libraries 
https://github.com/adafruit/Adafruit_NFCShield_I2C


5th of april:

First working version with both rfid reading and phone controll.


29th of april:

Recap:

- We used the I2c communication from MegaPi to the Rfid shield

A. Hardware
The hardware connections are as follows:

On MegaPi                       - On Shield
-------------------------------------------
Pin SCL				- Analog 5
Pin SDA				- Analog 4
VCC 5 V				- 5V
GND				- GND
Digital PIN 22			- Digital Pin 2

---------------------------------------------------


B. Software

We used the Adafruit_NFCShield_I2c library, from here:
	https://github.com/adafruit/Adafruit_NFCShield_I2C 


Then, in code we did:

#define IRQ   (22) // DD 02 April 2019 - This is pin 22 on MegaPi - we lifted the Bluetooth module phisically to connect to this pin 
#define RESET (3)


Adafruit_NFCShield_I2C nfc(IRQ, RESET);

----in Setup:


  nfc.begin();
  nfc.setPassiveActivationRetries(0x01);
  uint32_t versiondata = nfc.getFirmwareVersion();
  if (! versiondata) {
    Serial.print("Didn't find PN53x board");
    //while (1); // halt
  }
  else
  {
  // Got ok data, print it out!
    Serial.print("Found chip PN5"); Serial.println((versiondata>>24) & 0xFF, HEX); 
    Serial.print("Firmware ver. "); Serial.print((versiondata>>16) & 0xFF, DEC); 
    Serial.print('.'); Serial.println((versiondata>>8) & 0xFF, DEC);
  }
    
  // configure board to read RFID tags
  nfc.SAMConfig();

  //attachInterrupt(3, onRfidReadCard, RISING); // we attach and detach the interrupt INSIDE the loop
  /* 
	WARNING:
	The 3 above is RLY RLY BAD:
   *  https://www.arduino.cc/reference/en/language/functions/external-interrupts/attachinterrupt/
   *  On MegaPi there are 6 digital pins with interrupts:
   *  2, 3, 20, 21, 18 19
   *  The pins 2,3,18 and 19 are used for the motors.
   *  Pins 20 and 21 are used (BY DEFAULT) by SCL and SDA of the I2P communication.
   *  
   *  This means that we CANNOT use the actual IRQ - Pin 22 that we pass on to the nfc as IRQ.
   *  
   *  The (BAD) workaround is to use either pin 21 - int 2 OR pin 20 - int 3
   *  as digital pins for interrups. BUT those are already used for the communication itself (SCL - clock and SDA - data).
   *  
  */


TO DO 2nd of May:
Use this link: 
http://learn.makeblock.com/ultimate2-arduino-programming/ 

to make a NON blocking program that raises the robot arm a tiny bit and lowers it in the same position ONCE every 10 seconds !


16th of MAY:

Changed the code such that the ultrasonic sensor is NO LONGER getting data from a real ultrasonic sensor but instead its readings (on the phone) 
reflect the readings of the Adafruit Rfid card reader.
Every 2 seconds, the reader reads and on a successfull reading updates a variable with the second byte of the 4th sector 
(that should contain our team IDs). We spit this byte value out to the Makeblock phone app.

Still to be done: 
	EASY tasks:
	- When reading not successfull - send a 255 value out
	- Update the reading and instead of using a full byte (which contains TWICE our team id) - use just a nibble (halfbyte)
	- Send the count of readings "somehow" ??? - if still reading the same card - update some counter and add that up to reflect how many times we read the same card.
	
  IMPORTANT hard task:
	- USE THE linefollower module (we plugged it in the hardware) - write code that detects when we have an all black zone... 
	presumably our homezone AND signal that to the Phone app - still using the "fake" ultrasonic sensor reading.

	OPTIONAL:	
	- Do what we planned in the begining (see TODO 2nd of May) - implement for the robot to move the arm up/down for a bit whenever we find our box.

17th of MAY:
  - reading and display of the value is better now. If nothing is read - we show 0
  - NEW: the robot makes a sign now - whenever reading number 5 (our team id) from the tag

  Still NOT DONE: the linefollower module (although phisically installed) is NOT USED !!!!