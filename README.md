# ELECTRONIC-VOTING-MACHINE
#define echoPin 29 // attach pin D2 Arduino to pin Echo of HC-SR04
#define trigPin 27 //attach pin D3 Arduino to pin Trig of HC-SR04
// defines variables
long duration; // variable for the duration of sound wave travel
int distance; // variable for the distance measurement

#include <EEPROM.h>
#include "Arduino.h"
#include "SoftwareSerial.h"
#include "DFRobotDFPlayerMini.h"

SoftwareSerial mySoftwareSerial(11, 10); // RX, TX
DFRobotDFPlayerMini myDFPlayer;
void printDetail(uint8_t type, int value);
int dist(void) ;void result();
void setup()
{
    pinMode(trigPin, OUTPUT); // Sets the trigPin as an OUTPUT
  pinMode(echoPin, INPUT); // Sets the echoPin as an INPUT
  
  
  pinMode(7,INPUT);  // button 1
   pinMode(6,INPUT);  //Button 2
    pinMode(5,INPUT);  //button 3
     pinMode(4,INPUT);  // Button 4
      pinMode(3,INPUT);  //button 5
       pinMode(2,INPUT);  //Button 6
        pinMode(25,INPUT); // vote casting button
         pinMode(23,OUTPUT);  //Buzzer
          pinMode(31,INPUT);  // person mode(blind/normal)
           pinMode(33,INPUT_PULLUP); // EVM activation  

            pinMode(35,OUTPUT); // sp/head set chng 

            
            digitalWrite(23,LOW); 
             digitalWrite(35,LOW);   
  
           mySoftwareSerial.begin(9600);
       Serial.begin(9600);
  
  
  if (!myDFPlayer.begin(mySoftwareSerial)) {  //Use softwareSerial to communicate with mp3.
    Serial.println(F("Unable to begin:"));
    Serial.println(F("1.Please recheck the connection!"));
    Serial.println(F("2.Please insert the SD card!"));
    while(true){
      delay(0); // Code to compatible with ESP8266 watch dog.
    }
  }
  Serial.println(F("DFPlayer Mini online."));
  
  myDFPlayer.volume(20);  //Set volume value. From 0 to 30
 delay(1000);
 if(!digitalRead(25))
 {EEPROM.write(1,0);delay(100);EEPROM.write(2,0);delay(100);
 EEPROM.write(3,0);delay(100);EEPROM.write(4,0);delay(100);
 EEPROM.write(5,0);delay(100);EEPROM.write(6,0);delay(100);}
  if(!digitalRead(2))result();
}

void loop()
{ int cst=0; int val,mem;
  
 
 
  if(!digitalRead(31))//visually challeged people mode
  {
     
    while(digitalRead(33));
     digitalWrite(23,HIGH);
     while(!cst)
     {
        while(dist()>20);       
       digitalWrite(23,LOW);
       digitalWrite(35,LOW); delay(1000);
       myDFPlayer.play(2);  delay(15000);
      digitalWrite(35,HIGH);delay(800);

      while(!cst)
      {
    if(!digitalRead(7))
   {myDFPlayer.play(3);  delay(5500);mem=EEPROM.read(1);delay(100);if(!digitalRead(25)){EEPROM.write(1,mem+1);delay(200);myDFPlayer.play(1);  delay(4500);cst=1;}}
   else if(!digitalRead(6))
   { myDFPlayer.play(4);  delay(5500);mem=EEPROM.read(2);delay(100);if(!digitalRead(25)){EEPROM.write(2,mem+1);delay(200);myDFPlayer.play(1);  delay(4500);cst=1;}}
    else if(!digitalRead(5))
    {myDFPlayer.play(5);  delay(5500);mem=EEPROM.read(3);delay(100);if(!digitalRead(25)){EEPROM.write(3,mem+1);delay(200);myDFPlayer.play(1);  delay(4500);cst=1;}}
    else if(!digitalRead(4))
    {myDFPlayer.play(6);  delay(5500);mem=EEPROM.read(4);delay(100);if(!digitalRead(25)){EEPROM.write(4,mem+1);delay(200);myDFPlayer.play(1);  delay(4500);cst=1;}}
    else if(!digitalRead(3))
   { myDFPlayer.play(7);  delay(5500);mem=EEPROM.read(5);delay(100);if(!digitalRead(25)){EEPROM.write(5,mem+1);delay(200);myDFPlayer.play(1);  delay(4500);cst=1;}}
     else if(!digitalRead(2))
   { myDFPlayer.play(9);  delay(5500);mem=EEPROM.read(6);delay(100);if(!digitalRead(25)){EEPROM.write(6,mem+1);delay(200);myDFPlayer.play(1);  delay(4500);cst=1;}}
       
    }
      digitalWrite(23,HIGH);delay(1500); digitalWrite(23,LOW);delay(1000);
    }
    
    
      
  }

  else    //Normal people mode

  {


   while(digitalRead(33));
     digitalWrite(23,HIGH);delay(1000); digitalWrite(23,LOW);
     while(!cst)
     {
       
    if(!digitalRead(7))
   {mem=EEPROM.read(1);delay(100);digitalWrite(23,HIGH);delay(1000); digitalWrite(23,LOW);if(!digitalRead(25)){EEPROM.write(1,mem+1);delay(200);cst=1;}}
   else if(!digitalRead(6))
   {mem=EEPROM.read(2);delay(100);digitalWrite(23,HIGH);delay(1000); digitalWrite(23,LOW);if(!digitalRead(25)){EEPROM.write(2,mem+1);delay(200);cst=1;}}
    else if(!digitalRead(5))
    {mem=EEPROM.read(3);delay(100);digitalWrite(23,HIGH);delay(1000); digitalWrite(23,LOW);if(!digitalRead(25)){EEPROM.write(3,mem+1);delay(200);cst=1;}}
    else if(!digitalRead(4))
    {mem=EEPROM.read(4);delay(100);digitalWrite(23,HIGH);delay(1000); digitalWrite(23,LOW);if(!digitalRead(25)){EEPROM.write(4,mem+1);delay(200);cst=1;}}
    else if(!digitalRead(3))
   { mem=EEPROM.read(5);delay(100);digitalWrite(23,HIGH);delay(1000); digitalWrite(23,LOW);if(!digitalRead(25)){EEPROM.write(5,mem+1);delay(200);cst=1;}}
     else if(!digitalRead(2))
   { mem=EEPROM.read(6);delay(100);digitalWrite(23,HIGH);delay(1000); digitalWrite(23,LOW);if(!digitalRead(25)){EEPROM.write(6,mem+1);delay(200);cst=1;}}
       
    }
     digitalWrite(23,HIGH);delay(1500); digitalWrite(23,LOW);delay(2500);


  }

  
}



int dist(void) 
{
  // Clears the trigPin condition
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  // Sets the trigPin HIGH (ACTIVE) for 10 microseconds
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  // Reads the echoPin, returns the sound wave travel time in microseconds
  duration = pulseIn(echoPin, HIGH);
  // Calculating the distance
  distance = duration * 0.034 / 2; // Speed of sound wave divided by 2 (go and back)
  // Displays the distance on the Serial Monitor
  return distance;
}

void result()


{ Serial.print("RAMESH Total votes= ");delay(100); Serial.println(EEPROM.read(1));delay(100);
      Serial.print("RAJU Total votes= ");delay(100); Serial.println(EEPROM.read(2));delay(100);
     Serial.print("Krishna Total votes=");delay(100); Serial.println(EEPROM.read(3));delay(100);
     Serial.print("Srinadh Total votes=");delay(100); Serial.println(EEPROM.read(4));delay(100);
     Serial.print("Sashank Total votes=");delay(100); Serial.println(EEPROM.read(5));delay(100);
     Serial.print("NO votes=");delay(100); Serial.println(EEPROM.read(6));delay(100);


}
