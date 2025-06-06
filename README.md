#include <LiquidCrystal.h>

// Ultrasonic sensor pins
#define trigPin 9
#define echoPin 10

// LCD pin mapping: RS, E, D4, D5, D6, D7
LiquidCrystal lcd(A0, A1, A2, A3, A4, A5);

// Traffic Light Pins for 4 roads (only Red and Green)
int r1_red = 2, r1_green = 3;
int r2_red = 4, r2_green = 5;
int r3_red = 6, r3_green = 7;
int r4_red = 8, r4_green = 11;

void setup() {
  // Initialize ultrasonic sensor
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);

  // LCD setup
  lcd.begin(16, 2);
  lcd.print("Traffic System");
  delay(2000);
  lcd.clear();

  // Set all traffic light pins as OUTPUT
  int pins[] = {r1_red, r1_green, r2_red, r2_green, r3_red, r3_green, r4_red, r4_green};
  for (int i = 0; i < 8; i++) {
    pinMode(pins[i], OUTPUT);
  }
}

void loop() {
  long duration;
  int distance;

  // Measure distance from ultrasonic
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  duration = pulseIn(echoPin, HIGH);
  distance = duration * 0.034 / 2;

  if (distance > 0 && distance <= 100) {
    // Ambulance detected on Road 4
    activateAmbulancePath();
  } else {
    // Normal traffic cycle
    runTrafficCycle();
  }

  delay(500);
}

// Function to simulate normal traffic flow
void runTrafficCycle() {
  setTrafficLights(r1_green);
  delay(4000);
  setTrafficLights(r2_green);
  delay(4000);
  setTrafficLights(r3_green);
  delay(4000);
  setTrafficLights(r4_green);
  delay(4000);
}

// Function to give green to ambulance road (Road 4)
void activateAmbulancePath() {
  // Turn all lights off first
  turnAllLightsOff();

  // Red for all except Road 4 green
  digitalWrite(r1_red, HIGH);
  digitalWrite(r2_red, HIGH);
  digitalWrite(r3_red, HIGH);
  digitalWrite(r4_green, HIGH);

  // LCD Message
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("AMBULANCE ON RD4");
  lcd.setCursor(0, 1);
  lcd.print("Please Cooperate");
  delay(5000);
  lcd.clear();
}

// Function to set one green road and all others red
void setTrafficLights(int greenPin) {
  turnAllLightsOff();

  if (greenPin == r1_green) {
    digitalWrite(r1_green, HIGH);
    digitalWrite(r2_red, HIGH);
    digitalWrite(r3_red, HIGH);
    digitalWrite(r4_red, HIGH);
  } else if (greenPin == r2_green) {
    digitalWrite(r2_green, HIGH);
    digitalWrite(r1_red, HIGH);
    digitalWrite(r3_red, HIGH);
    digitalWrite(r4_red, HIGH);
  } else if (greenPin == r3_green) {
    digitalWrite(r3_green, HIGH);
    digitalWrite(r1_red, HIGH);
    digitalWrite(r2_red, HIGH);
    digitalWrite(r4_red, HIGH);
  } else if (greenPin == r4_green) {
    digitalWrite(r4_green, HIGH);
    digitalWrite(r1_red, HIGH);
    digitalWrite(r2_red, HIGH);
    digitalWrite(r3_red, HIGH);
  }
}

// Turn all traffic lights off
void turnAllLightsOff() {
  int pins[] = {r1_red, r1_green, r2_red, r2_green, r3_red, r3_green, r4_red, r4_green};
  for (int i = 0; i < 8; i++) {
    digitalWrite(pins[i], LOW);
  }
}
