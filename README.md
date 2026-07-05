#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET -1
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, OLED_RESET);

int soilPin = A0;
int lightPin = A1;

void setup() {
  Serial.begin(9600);
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.clearDisplay();
  display.setTextColor(SSD1306_WHITE);
  display.display();
}

void setMood(String line1, String line2) {
  display.clearDisplay();
  display.setTextSize(1);
  display.setCursor(10, 0);
  display.println("Plant Mood");
  display.drawLine(0, 10, 127, 10, SSD1306_WHITE);
  display.setTextSize(2);
  display.setCursor(10, 20);
  display.println(line1);
  display.setTextSize(1);
  display.setCursor(10, 50);
  display.println(line2);
  display.display();
}

void loop() {
  int soil = analogRead(soilPin);
  int light = analogRead(lightPin);

  if (soil < 300 && light < 300) {
    setMood("Cold &", "Thirsty!");
  } else if (soil < 300 && light >= 300) {
    setMood("Water", "Me Please!");
  } else if (soil >= 300 && light < 300) {
    setMood("Sleepy", "zzz...");
  } else {
    setMood("I Feel", "Great! JAY");
  }

  delay(2000);
}
