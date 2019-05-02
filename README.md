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
