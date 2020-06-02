#include <MPU6050_tockn.h>
#include <HCSR04.h>
#include <Servo.h>
#include <Wire.h>
#include <SoftwareSerial.h>
    
SoftwareSerial mySerial(0, 1); // RX, TX
String inString = "";    // string to hold input
int unsigned i = 0, j = 0, flag = 0; String tarf = ""; float initAngle;
Servo myservo;
UltraSonicDistanceSensor distanceSensor(5, 4);
MPU6050 mpu6050(Wire);
     
void motorStop(){
  digitalWrite(8, HIGH); delay(500);
}
      
void motorStraight(){
  digitalWrite(13, LOW); digitalWrite(8, LOW); analogWrite(11, 255);
}
      
void motorRight(){
  mpu6050.update(); initAngle = mpu6050.getAngleX();
  for (int i = 22; i <= 110; i += 1) { 
    myservo.write(i); delay(15);                       
  }
  Serial.print("motor Right :"); Serial.println(mpu6050.getAngleX() - initAngle);
  while((mpu6050.getAngleX() - initAngle)  > -70.0){
    mpu6050.update();          
    digitalWrite(13, HIGH); digitalWrite(8, LOW); analogWrite(11, 255);
  }
  motorStop();
  for (int i = 110; i >= 22; i -= 1) { 
    myservo.write(i); delay(15);                       
  }
}
      
void motorLeft(){
  mpu6050.update();
  initAngle = mpu6050.getAngleX();
  for(int i = 22; i <= 110; i += 1) { 
    myservo.write(i); delay(15);                       
  }
  Serial.print("motor Left :"); Serial.println(mpu6050.getAngleX() - initAngle);
  while((mpu6050.getAngleX() - initAngle) < 95.0){
    mpu6050.update();
    digitalWrite(13, LOW); digitalWrite(8, LOW); analogWrite(11, 255);
  }
  motorStop();
  for(int i = 110; i >= 22; i -= 1) { 
    myservo.write(i); delay(15);                       
  }
}
      
void turn(char angle){
  if(angle=='r'){
    motorRight(); motorStop();
  }
  if(angle=='l'){
    motorLeft(); motorStop();
  }
}
      
void ultraSonic(float distance){
  myservo.write(22);
  while(distanceSensor.measureDistanceCm() > distance || distanceSensor.measureDistanceCm()==-1.0){
    motorStraight();
    float cm = distanceSensor.measureDistanceCm(); delay(300);
  }
  motorStop();
}
      
void setup() {
  myservo.attach(9);
  //MOTOR
  pinMode(13, OUTPUT); pinMode(8, OUTPUT);
  //
  Serial.begin(9600); mySerial.begin(9600);
  while (!Serial) {
    ;
  }
  while (!mySerial) {
    ;
  }
  Wire.begin();
  myservo.write(22);
  mpu6050.begin(); mpu6050.calcGyroOffsets(true);
  Serial.print("\n");
}
    
void loop() {
  // Read serial input:
  while (mySerial.available() > 0) {
    char inChar = mySerial.read();
    if (inChar == '.') {
      flag = 1; i = 0;
      mySerial.println(inString);
      break;
    }
    inString += (char)inChar; 
  }

  if(flag == 1 && i<(inString.length())){
    if(inString[i]!='/')tarf += inString[i];
    if(inString[i]=='/'){
      j++;
      if(j%2==1)ultraSonic(tarf.toInt());
      if(j%2==0)turn(tarf[0]);
      tarf = "";
    }
    i++;
  }    
}
