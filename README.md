# Radar-is-c++
#include <Servo.h>

#define trigPin 4
#define echoPin 3
#define ledPinR 8      
#define buzzerPin 6   
#define ledPinY 5  
#define ledPinG 9        

Servo myServo;
int distance;

void setup() {
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(ledPinR, OUTPUT);
  pinMode(ledPinY, OUTPUT);
  pinMode(ledPinG, OUTPUT);
  pinMode(buzzerPin, OUTPUT);
  Serial.begin(9600);
  myServo.attach(2);
}

void loop() {
 
  for (int i = 1; i <= 120; i += 2) {
    myServo.write(i);
    delay(20);
    distance = calculateDistance();

    Serial.print(i);
    Serial.print(",");
    Serial.print(distance);
    Serial.print(".");

    checkAlert(distance); 
  }

 
  for (int i = 120; i >= 1; i -= 8) {
    myServo.write(i);
    delay(40);
    distance = calculateDistance();

    Serial.print(i);
    Serial.print(",");
    Serial.print(distance);
    Serial.print(".");

    checkAlert(distance);
  }
}

int calculateDistance() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);

  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  long duration = pulseIn(echoPin, HIGH);
  int distance = duration * 0.034 / 2;

  return distance;
}

void checkAlert(int d) {
  
  digitalWrite(ledPinG, HIGH);

  if (d > 0 && d < 30) { 
   
    digitalWrite(ledPinR, HIGH);
    digitalWrite(ledPinY, LOW);
    digitalWrite(buzzerPin, HIGH);
    digitalWrite(ledPinG, LOW); 
  } 
  else if (d >= 30 && d < 70) {
   
    digitalWrite(ledPinY, HIGH);
    digitalWrite(ledPinR, LOW);
    digitalWrite(ledPinG, LOW); 
    tone(buzzerPin, 1000, 100); 
  } 
  else {
   
    digitalWrite(ledPinR, LOW);
    digitalWrite(ledPinY, LOW);
    digitalWrite(buzzerPin, LOW);
    digitalWrite(ledPinG, HIGH);
  }
}
