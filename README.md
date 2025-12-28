# Week-1
#include <LiquidCrystal.h>

// ------------------------------------------------------------------
// 1. LCD PIN DEFINITIONS (MANDATORY PINS FOR LCD KEYPAD SHIELD)
// ------------------------------------------------------------------
// RS=8, EN=9, D4=4, D5=5, D6=6, D7=7 (Fixed by the shield's hardware)
const int rs = 8, en = 9, d4 = 4, d5 = 5, d6 = 6, d7 = 7;
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);

// ------------------------------------------------------------------
// 2. L298N MOTOR DRIVER PIN DEFINITIONS (Conflict-Free Wiring)
// ------------------------------------------------------------------
// Channel A (Right Side Motors)
const int ENA = 11; // Enable A (PWM pin)
const int IN1 = 13; // Input 1 for Direction
const int IN2 = 12; // Input 2 for Direction

// Channel B (Left Side Motors)
const int ENB = 3;  // Enable B (PWM pin)
const int IN3 = A1; // Input 3 for Direction (Safe pin, relocated from D2)
const int IN4 = A2; // Input 4 for Direction (Safe pin, relocated from D1)

// ------------------------------------------------------------------
// 3. CONSTANTS AND VARIABLES
// ------------------------------------------------------------------
const long DURATION_MS = 10000; // 10 seconds in milliseconds
const int CAR_SPEED = 200;      // Motor speed (0-255).
unsigned long startTime = 0;

void setup() {
  // Set all motor control pins as outputs
  pinMode(ENA, OUTPUT);
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(ENB, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);

  // Initialize the LCD
  lcd.begin(16, 2);
  lcd.print("Project Week 1");
  lcd.setCursor(0, 1);
  lcd.print("Ready to Start");

  delay(1500);
  lcd.clear();
}

void loop() {
  // ------------------------------------------------------------------
  // A. START MOVEMENT AND TIMER
  // ------------------------------------------------------------------
  startTime = millis();

  // Right side (A) motors: Forward
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);

  // Left side (B) motors: REVERSED direction to go forward
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);

  // Set motor speed (Uses single constant)
  analogWrite(ENA, CAR_SPEED);
  analogWrite(ENB, CAR_SPEED);

  lcd.setCursor(0, 0);
  lcd.print("Car is MOVING");

  // ------------------------------------------------------------------
  // B. TIMING LOOP (Runs for exactly 10,000 milliseconds)
  // ------------------------------------------------------------------
  while (millis() - startTime < DURATION_MS) {
    unsigned long elapsedTimeMs = millis() - startTime;
    long elapsedTimeDeci = elapsedTimeMs / 100;

    lcd.setCursor(0, 1);
    lcd.print("Time: ");
    lcd.print(elapsedTimeDeci / 10);
    lcd.print(".");
    lcd.print(elapsedTimeDeci % 10);
    lcd.print("s ");

    delay(50);
  }

  // ------------------------------------------------------------------
  // C. STOP MOVEMENT
  // ------------------------------------------------------------------
  // 1. Stop speed using PWM (0)
  analogWrite(ENA, 0);
  analogWrite(ENB, 0);

  // 2. Brake by setting inputs HIGH
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, HIGH);

  // ------------------------------------------------------------------
  // D. DISPLAY FINAL MESSAGE
  // ------------------------------------------------------------------
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("CHALLENGE COMPLETE");
  lcd.setCursor(0, 1);
  lcd.print("10.0s STOPPED");
  
  while(true);
}
