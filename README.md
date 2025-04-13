#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <Servo.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);
Servo myservo;

int IR1 = 7, IR2 = 6;  // Entry/Exit IR Sensors
int slotPins[] = {2, 3, 4, 5};
int S[4];

int Slot = 4, TotalCars = 0, MaxCars = 4, CarCount = 0;
int flag1 = 0, flag2 = 0;
bool gateOpen = false;

void setup() {
  Serial.begin(9600);
  lcd.init(); lcd.backlight();
  pinMode(IR1, INPUT); pinMode(IR2, INPUT);
  for (int i = 0; i < 4; i++) pinMode(slotPins[i], INPUT);
  myservo.attach(9); myservo.write(100);
  lcd.setCursor(0,0); lcd.print("     SMART    ");
  lcd.setCursor(0,1); lcd.print(" PARKING SYSTEM ");
  delay(2000);lcd.clear();
  lcd.setCursor(0,0); lcd.print("      BY    ");
  lcd.setCursor(0,1); lcd.print("    GROUP 1 ");
  delay(3000); lcd.clear();
  lcd.setCursor(0,0); lcd.print("ADITYA   DHWANIT");
  lcd.setCursor(0,1); lcd.print("TANMAY    HARSH");
  delay(4000); lcd.clear();
  lcd.setCursor(0,0); lcd.print("     ANKIT");
  delay(1500); lcd.clear();
  lcd.setCursor(0,0); lcd.print("   EDT PROJECT");
  delay(3000); lcd.clear();
}

void loop() {
  for (int i = 0; i < 4; i++) S[i] = digitalRead(slotPins[i]);
  TotalCars = (S[0]==LOW)+(S[1]==LOW)+(S[2]==LOW)+(S[3]==LOW);
  Slot = MaxCars - TotalCars;

  if (digitalRead(IR1)==LOW && flag1==0 && !gateOpen) {
    if (TotalCars < MaxCars) {
      flag1 = 1; myservo.write(0); gateOpen = true;
    } else {
      lcd.clear();
      lcd.setCursor(0,0); lcd.print("  Parking Full  ");
      lcd.setCursor(0,1); lcd.print("  Not Allowed!  ");
      delay(7000); lcd.clear();
    }
  }

  if (flag1 && digitalRead(IR2)==LOW) {
    delay(1000); myservo.write(100);
    flag1 = 0; gateOpen = false; CarCount++;
  }

  if (digitalRead(IR2)==LOW && flag2==0 && flag1==0 && !gateOpen) {
    flag2 = 1; myservo.write(0); gateOpen = true;
  }

  if (flag2 && digitalRead(IR1)==LOW) {
    delay(1000); myservo.write(100);
    flag2 = 0; gateOpen = false;
    if (CarCount > 0) CarCount--;
  }

  lcd.setCursor(0,0);
  lcd.print("S1:"); lcd.print(S[0]==LOW ? "F " : "E ");
  lcd.print("S2:"); lcd.print(S[1]==LOW ? "F " : "E ");
  lcd.setCursor(0,1);
  lcd.print("S3:"); lcd.print(S[2]==LOW ? "F " : "E ");
  lcd.print("S4:"); lcd.print(S[3]==LOW ? "F " : "E ");
  lcd.setCursor(12,0); lcd.print("L:"); lcd.print(Slot);
  lcd.setCursor(12,1); lcd.print("C:"); lcd.print(CarCount);
  delay(200);
}
