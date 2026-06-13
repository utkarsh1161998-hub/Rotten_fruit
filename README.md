#include <Wire.h>
#include <LiquidCrystal_I2C.h>

// Initialize the LCD (Address 0x27 or 0x3F is common for 16x2 displays)
LiquidCrystal_I2C lcd(0x27, 16, 2);

// Pin Definitions
const int sensorPin = A0;  // MQ-135 Analog pin
const int greenLed = 8;    // Indicator for Fresh Fruit
const int redLed = 9;      // Indicator for Rotten Fruit
const int buzzer = 10;     // Alarm for Rotten Fruit

// Threshold Value (You will need to calibrate this!)
// Lower than this = Fresh, Higher than this = Spoiling/Rotten
const int threshold = 400; 

void setup() {
  // Start Serial communication for debugging
  Serial.begin(9600);
  
  // Initialize LED and Buzzer pins
  pinMode(greenLed, OUTPUT);
  pinMode(redLed, OUTPUT);
  pinMode(buzzer, OUTPUT);
  
  // Initialize LCD
  lcd.init();
  lcd.backlight();
  
  // Warm-up message (MQ sensors need a moment to stabilize)
  lcd.setCursor(0, 0);
  lcd.print("Sensor Warming Up");
  delay(3000); // Give it a few seconds on boot
  lcd.clear();
}

void loop() {
  // Read the analog value from the gas sensor
  int sensorValue = analogRead(sensorPin);
  
  // Print to Serial Monitor for calibration purposes
  Serial.print("Gas Level: ");
  Serial.println(sensorValue);
  
  // Display the current reading on the LCD
  lcd.setCursor(0, 0);
  lcd.print("Gas Level: ");
  lcd.print(sensorValue);
  lcd.print("   "); // Clear trailing characters
  
  lcd.setCursor(0, 1);
  
  // Check if gas level exceeds the "rotten" threshold
  if (sensorValue > threshold) {
    // Fruit is likely rotten
    lcd.print("Status: ROTTEN  ");
    
    digitalWrite(redLed, HIGH);
    digitalWrite(greenLed, LOW);
    
    // Beep the buzzer
    digitalWrite(buzzer, HIGH);
    delay(200);
    digitalWrite(buzzer, LOW);
  } 
  else {
    // Fruit is fresh
    lcd.print("Status: FRESH   ");
    
    digitalWrite(greenLed, HIGH);
    digitalWrite(redLed, LOW);
    digitalWrite(buzzer, LOW);
  }
  
  delay(1000); // Wait 1 second before the next read
}
