<!--panels:start-->
<!--div:title-panel-->

  # Dog Toy

<!--div:left-panel-->

Notes:

* Look at this

<!--div:right-panel-->




```clike
/*
  For Arduino boards with only one serial port like UNO board, 
  the function of software visual serial port is to be used.
*/


#include <Arduino.h> 
#include <U8x8lib.h>
U8X8_SSD1306_128X64_ALT0_HW_I2C u8x8(/* reset=*/ U8X8_PIN_NONE);


#include <SoftwareSerial.h> //header file of software serial port

SoftwareSerial Serial1(10, 11); //define software serial port name as Serial1 and define pin2 as RX and pin3 as TX

/* 
For Arduinoboards with multiple serial ports like DUEboard, 
interpret above two pieces of code and directly 
use Serial1 serial port*/


int dist; //actual distance measurements of LiDAR 
int strength; //signal strength of LiDAR
float temprature;
int check; //save check value
int i;
int uart[9]; //save data measured by LiDAR

const int HEADER = 0x59; //frame header of data package 

void setup() {
  Serial.begin(9600); //set bit rate of serial port connecting Arduino with computer
  Serial1.begin(115200); //set bit rate of serial port connecting LiDAR with Arduino 
  u8x8.begin(); 
  u8x8.setFlipMode(1);
}


void loop() {
  u8x8.setFont(u8x8_font_chroma48medium8_r); u8x8.setCursor(0, 0);
  u8x8.print("Hello World!");
  if (Serial1.available()) { //check if serial port has data input
    if (Serial1.read() == HEADER) { //assess data package frame header 0x59 
      
      uart[0]=HEADER;
      if (Serial1.read() == HEADER) { //assess data package frame header 0x59
        
        uart[1] = HEADER;
        for (i = 2; i < 9; i++) { //save data in array
          uart[i] = Serial1.read();
        }
        check = uart[0] + uart[1] + uart[2] + uart[3] + uart[4] + uart[5] + uart[6] + uart[7]; 
        
        
        if (uart[8] == (check & 0xff)) { 
          
          //verify the received data as per protocol

          
          dist = uart[2] + uart[3] * 256; 
          //calculate distance value 
          
          strength = uart[4] + uart[5] * 256; 
          //calculate signal strength value 
          
          temprature = uart[6] + uart[7] *256;
          //calculate chip temprature 
          
          temprature = temprature/8 - 256;
          
          
          Serial.print("dist = ");
          Serial.print(dist); //output measure distance value of LiDAR 
          Serial.print('\t');
          Serial.print("strength = ");
          Serial.print(strength); //output signal strength value
          Serial.print("\t Chip Temprature = ");
          Serial.print(temprature);
          Serial.println(" celcius degree"); //output chip temperature of Lidar
        }
      }
    }
  }
}
```

<!--panels:end-->



