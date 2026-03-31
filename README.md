# Radar-is-c++-ArdIDE
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
//#java-in-processingApp


import processing.serial.*;

Serial myPort;
float pixsDistance;
int iAngle, iDistance;
int index1 = 0;
PFont orcFont;

void setup() {
  size(1200, 700);
  smooth();
  myPort = new Serial(this, "COM3", 9600);
  myPort.bufferUntil('.');
  orcFont = createFont("Arial", 20, true);
}

void draw() {
  // Dark background with fading effect
  noStroke();
  fill(0, 40);
  rect(0, 0, width, height);

  // Call drawing functions
  drawRadar();
  drawLine();
  drawObject();
  drawText();
}

void serialEvent(Serial myPort) {
  String data = myPort.readStringUntil('.');
  if (data != null && data.length() > 0) {
    data = data.substring(0, data.length() - 1);
    index1 = data.indexOf(",");
    if (index1 > 0) {
      iAngle = int(data.substring(0, index1));
      iDistance = int(data.substring(index1 + 1));
    }
  }
}

void drawRadar() {
  pushMatrix();
  translate(width / 2, height - height * 0.1);
  noFill();
  strokeWeight(1.5);
  stroke(0, 200, 100, 180);

  // Radar circles
  for (int r = 400; r <= 1000; r += 200) {
    arc(0, 0, r, r, PI, TWO_PI);
  }

  // Angle lines
  for (int a = 0; a <= 180; a += 30) {
    line(0, 0, 500 * cos(radians(a)), -500 * sin(radians(a)));
  }
  popMatrix();
}

void drawLine() {
  pushMatrix();
  translate(width / 2, height - height * 0.1);
  strokeWeight(3);

  // Gradient glow effect
  for (int glow = 0; glow < 5; glow++) {
    stroke(0, 255 - glow * 40, 100 + glow * 30, 180 - glow * 30);
    line(0, 0,
      500 * cos(radians(iAngle)),
      -500 * sin(radians(iAngle)));
  }
  popMatrix();
}

void drawObject() {
  pushMatrix();
  translate(width / 2, height - height * 0.1);
  pixsDistance = iDistance * 3;

  if (iDistance > 0 && iDistance < 200) {
    noStroke();
    fill(255, 0, 0, 200);

    ellipse(pixsDistance * cos(radians(iAngle)),
            -pixsDistance * sin(radians(iAngle)),
            20, 20);

    // Glow effect for the object
    fill(255, 50, 50, 100);
    ellipse(pixsDistance * cos(radians(iAngle)),
            -pixsDistance * sin(radians(iAngle)),
            40, 40);
  }
  popMatrix();
}

void drawText() {
  fill(0, 150);
  rect(30, height - 80, 420, 50);

  fill(0, 255, 0);
  textFont(orcFont);
  textSize(22);

  text("Angle: " + iAngle + "°", 40, height - 50);
  text("Distance: " + iDistance + " cm", 220, height - 50);
}
