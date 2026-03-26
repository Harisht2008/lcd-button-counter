# lcd-button-counter

A button-controlled counter displayed on an LCD1602 screen.
Pressing the button increments the count. Resets to 0 after 20.

## Components
- Elegoo Mega R3
- LCD1602 16x2 display
- 10k potentiometer (contrast control)
- Push button
- 10k resistor (pull-down)
- Jumper wires

## Wiring
LCD RS  → pin 12
LCD E   → pin 11
LCD D4  → pin 5
LCD D5  → pin 4
LCD D6  → pin 3
LCD D7  → pin 2
Button  → pin 9

## Concepts Demonstrated
- Parallel data bus communication (4-bit LCD mode)
- Hardware debouncing to filter button noise
- LCD cursor positioning to prevent display artifacts
- Serial monitor debugging alongside physical output

## What I Learned
The LCD communicates through a parallel data bus where 4 bits
are sent at a time using an enable pin as a clock signal.
This is the same principle used in memory interfaces and
processor buses in chip design. Debouncing filters the
mechanical noise from button contacts the same way
synchronizer circuits handle metastability in hardware.


IMGS:
![IMG_3758](https://github.com/user-attachments/assets/9c2cbacb-723b-4ba1-94b9-9dc98024fef4)

![IMG_3759](https://github.com/user-attachments/assets/99f1a979-e5f6-43f0-9427-045e7c2cb2cd)

![IMG_3760](https://github.com/user-attachments/assets/8aa8e3fb-7040-47cd-a9f2-9e215738f7f0)

![IMG_3760](https://github.com/user-attachments/assets/4a00db22-9f3f-46e5-9745-81d33625094c)




#include <LiquidCrystal.h>

LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

int pressed = 0;
int lastReading = LOW;
unsigned long lastDebounce = 0;
unsigned long debounceDelay = 50;

void setup() {
  lcd.begin(16,2);
  pinMode(9, INPUT);
  lcd.setCursor(0,0);
  lcd.print("Button Counter");
  lcd.setCursor(0,1);
  lcd.print("Count: 0");
  Serial.begin(9600);
}

void loop() {
  int reading = digitalRead(9);

  if (reading != lastReading) {
    lastDebounce = millis();
  }

  if((millis() - lastDebounce) > debounceDelay) {
    if(digitalRead(9) == HIGH) {
      pressed++;
      Serial.print("Press Count:");
      Serial.println(pressed);
      lcd.setCursor(0,1);
      lcd.print("Count:");
      lcd.setCursor(7, 1);
      lcd.print(pressed);
      lcd.print("   ");
    }
    if(pressed > 20) {
      pressed = 0;
      lcd.setCursor(8, 1);
      lcd.print(pressed);
      lcd.print("   ");  
    }
  }
}
