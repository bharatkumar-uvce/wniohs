#include <TM1637Display.h>

const int pulsePin = 2;
unsigned long pulseWidth; // in microseconds
float speed_kmph;

// TM1637 display pins
#define CLK 3
#define DIO 4

TM1637Display display(CLK, DIO);

void setup() {
  pinMode(pulsePin, INPUT);
  Serial.begin(9600);
  display.setBrightness(7);     // Set brightness (0 to 7)
  display.showNumberDec(0);     // Initialize display with 0
}

void loop() {
  pulseWidth = pulseIn(pulsePin, HIGH, 1000000); // Wait for pulse, 1 sec timeout

  if (pulseWidth > 0) {
    // Calculate speed in km/h
    speed_kmph = (1.0 / pulseWidth) * 1e6 * 0.07589;

    // Get integer part
    int speed_int = (int)speed_kmph;

    // If there's a decimal part, increment
    if (speed_kmph > speed_int) {
      speed_int += 1;
    }

    Serial.print("Pulse Width (us): ");
    Serial.print(pulseWidth);
    Serial.print(" | Speed: ");
    Serial.print(speed_kmph);
    Serial.print(" | Displaying: ");
    Serial.println(speed_int);

    // Show on display
    display.showNumberDec(speed_int, true);
  } else {
    Serial.println("No pulse detected");
    display.showNumberDec(0, true);
  }

  delay(100);
}
