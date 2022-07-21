#include <IRremote.h>
#include <Servo.h> 

int servoPin = 10; 
int servoPower = 8; 
int eyes = 4; 
int Button = 11;
int ButtonPower = 12;
int eyedelay = 200;
int state = HIGH;   
int lightstate = LOW;   
int reading;         
int previous = LOW;
long time = 0;
long debounce = 200;
Servo Servo1; 
const int ledPin =  LED_BUILTIN;

int RECV_PIN = 7;
IRrecv irrecv(RECV_PIN);
decode_results results; 

#define CIRCLE 0x9614E178
#define MINUS 0x56484D64
#define SUN 0xCB18E2E4
#define MINUTE 0x3C51AEF8

//------------------------------------------------------------

void setup()
{
  Serial.begin(9600);
  irrecv.enableIRIn(); // Start the receiver
  pinMode(ButtonPower, OUTPUT);
  digitalWrite(ButtonPower, HIGH);
   pinMode(eyes, OUTPUT); 
   pinMode(servoPower, OUTPUT);
   pinMode(Button, INPUT);
   Servo1.attach(servoPin); 
   pinMode(ledPin, OUTPUT);
}

void loop()
{
  if (irrecv.decode(&results))
  {
    if (results.value == CIRCLE) //open and close helmet with LED, NORMAL OPERATION
    {
      Serial.println("CIRCLE 0x9614E178");
      if (state == HIGH){
      state = LOW; //system start helmet closed /ON
      digitalWrite(servoPower, HIGH);
      Servo1.write(80);
      delay (100);
      eyenimation();
      delay (1000);
      digitalWrite(servoPower, LOW);
      Serial.println("HELMET CLOSED");
    }
    else{ //helmet open
      state = HIGH;
      digitalWrite(servoPower, HIGH);
      digitalWrite(eyes, LOW);
      Servo1.write(170);
      delay (2000);
      digitalWrite(servoPower, LOW);
      Serial.println("HELMET OPEN");
    }
    time = millis();
    }
    else if (results.value == MINUS)
    {
      Serial.println("MINUS 0x56484D64 ");
    }
    else if (results.value == SUN) //eye light
    {
      Serial.println("SUN 0xCB18E2E4 ");
      if (lightstate == LOW){
      lightstate = HIGH; //system start helmet closed /ON
      digitalWrite(ledPin, HIGH);
      Serial.println("EYE ON");
    }
    else{ //helmet open
      lightstate = LOW;
      digitalWrite(ledPin, LOW);
      Serial.println("EYE OFF");
    }
    time = millis();
    }
    else if (results.value == MINUTE)
    {
      Serial.println("MINUTE 0x3C51AEF8");
    }
    else {
      Serial.println("Unknown Code Received");
    }
    irrecv.resume();
  }
}

void eyenimation(){
digitalWrite(ledPin, HIGH);
digitalWrite(eyes, HIGH);
delay (eyedelay);
digitalWrite(eyes, LOW);
digitalWrite(ledPin, LOW);
delay (eyedelay);
digitalWrite(eyes, HIGH);
digitalWrite(ledPin, HIGH);
delay (eyedelay);
digitalWrite(eyes, LOW);
digitalWrite(ledPin, LOW);
delay (eyedelay);
digitalWrite(eyes, HIGH);
}
