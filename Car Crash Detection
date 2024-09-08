#include <TinyGPS++.h>
#include <SoftwareSerial.h>
#include <Wire.h>
#include <MPU6050_light.h>

SoftwareSerial sim(2, 5);
static const int RXPin = 11, TXPin = 10;
static const uint32_t GPSBaud = 9600;
static const int iPin = 13;

MPU6050 mpu(Wire);

unsigned long timer = 0;
const float rollOverThreshold = 60.0;
String number = "+919765945350"; 
// The TinyGPS++ object
TinyGPSPlus gps;

// The serial connection to the GPS device
SoftwareSerial GPS(RXPin, TXPin);

void setup(){
  sim.begin(9600);
  Serial.begin(9600);
   Wire.begin();
  mpu.begin();
  GPS.begin(GPSBaud);
   pinMode(iPin, INPUT);
  digitalWrite(iPin, HIGH);
   delay(1000);
    mpu.calcGyroOffsets();
}

void loop(){
  mpu.update();
  while (GPS.available() > 0){
    gps.encode(GPS.read());
    if (gps.location.isUpdated()){
       if (digitalRead(iPin) == LOW)
      SendMessage();

      if ((millis() - timer) > 100) { // print data every 100ms
    timer = millis();
    float rollAngle = abs(mpu.getAngleX()); // Assuming X-axis represents roll angle
   

    if (rollAngle > rollOverThreshold) {
       Serial.print("Roll Angle: ");
    Serial.println(rollAngle);
    SendMessage();
      // Add your emergency procedures here, such as activating alarms or sending alerts.
    }
  }
      // Serial.print("Latitude= "); 
      // Serial.print(gps.location.lat(), 6);
      // Serial.print(" Longitude= "); 
      // Serial.println(gps.location.lng(), 6); debugging purpose
    }
  }
}
void SendMessage() {
  
  String SMS = "http://maps.google.com/maps?q=loc:" + String(gps.location.lat(), 6) + "," + String(gps.location.lng(), 6);
  
  sim.println("AT+CMGF=1"); // Sets the GSM Module in Text Mode
  delay(200);
  sim.println("AT+CMGS=\"" + number + "\"\r"); // Mobile phone number to send message
  delay(200);
  sim.println(SMS);
  delay(100);
  sim.println((char)26); // ASCII code of CTRL+Z
  delay(200);
  delay(10000);
  digitalWrite(iPin, HIGH);
   sim.print (F("ATD"));
  sim.print (number);
  sim.print (F(";\r\n"));
  if (sim.available() > 0)
    Serial.write(sim.read());
}
