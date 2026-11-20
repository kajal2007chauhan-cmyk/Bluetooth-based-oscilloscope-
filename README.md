#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SH110X.h>
#include "BluetoothSerial.h"

#define SIGNAL_PIN 34
#define GRAPH_WIDTH 128
#define SAMPLE_INTERVAL 100

Adafruit_SH1106G display(128, 64, &Wire, -1);
BluetoothSerial SerialBT;

const float DIVIDER_RATIO = 2.0;

float samples[GRAPH_WIDTH];
float liveVoltage = 0;
float vMax = 0;
float vMin = 0;
float vPP = 0;

int sampleCount = 0;

bool running = true;

unsigned long previousSample = 0;
unsigned long previousDisplay = 0;

// ----------------------------------
// READ VOLTAGE
// ----------------------------------

float readVoltage() {
  uint32_t total = 0;

  for (int i = 0; i < 10; i++) {
    total += analogReadMilliVolts(SIGNAL_PIN);
    delayMicroseconds(200);
  }

  float adcVoltage = (total / 10.0) / 1000.0;

  return adcVoltage * DIVIDER_RATIO;
}

// ----------------------------------
// UPDATE GRAPH
// ----------------------------------

void updateGraph(float voltage) {
  for (int i = 0; i < GRAPH_WIDTH - 1; i++) {
    samples[i] = samples[i + 1];
  }

  samples[GRAPH_WIDTH - 1] = voltage;

  if (sampleCount < GRAPH_WIDTH) {
    sampleCount++;
  }
}

// ----------------------------------
// CALCULATE VALUES
// ----------------------------------

void calculateValues() {
  if (sampleCount == 0) return;

  int start = GRAPH_WIDTH - sampleCount;

  vMax = samples[start];
  vMin = samples[start];

  for (int i = start; i < GRAPH_WIDTH; i++) {
    if (samples[i] > vMax) vMax = samples[i];
    if (samples[i] < vMin) vMin = samples[i];
  }

  vPP = vMax - vMin;
}

// ----------------------------------
// OLED DISPLAY
// ----------------------------------

void drawDisplay() {
  display.clearDisplay();
  display.setTextColor(SH110X_WHITE);
  display.setTextSize(1);

  display.setCursor(0, 0);
  display.print("DC OSCILLOSCOPE");

  display.setCursor(98, 0);
  display.print(running ? "RUN" : "STOP");

  display.drawLine(0, 10, 127, 10, SH110X_WHITE);

  display.setCursor(0, 13);
  display.print("LIVE:");
  display.print(liveVoltage, 2);
  display.print("V");

  display.setCursor(0, 24);
  display.print("MAX:");
  display.print(vMax, 2);

  display.setCursor(67, 24);
  display.print("MIN:");
  display.print(vMin, 2);

  display.setCursor(0, 35);
  display.print("VPP:");
  display.print(vPP, 2);
  display.print("V");

  display.drawRect(0, 45, 128, 19, SH110X_WHITE);

  int start = GRAPH_WIDTH - sampleCount;

  for (int i = start + 1; i < GRAPH_WIDTH; i++) {
    int x1 = i - start - 1;
    int x2 = i - start;

    int y1 = 62 - (int)(samples[i - 1] * 3.0);
    int y2 = 62 - (int)(samples[i] * 3.0);

    y1 = constrain(y1, 46, 62);
    y2 = constrain(y2, 46, 62);

    display.drawLine(x1, y1, x2, y2, SH110X_WHITE);
  }

  display.display();
}

// ----------------------------------
// BLUETOOTH COMMANDS
// ----------------------------------

void checkBluetoothCommands() {
  while (SerialBT.available()) {
    char command = SerialBT.read();

    if (command == 'S' || command == 's') {
      running = false;
      SerialBT.println("STOPPED");
    }

    else if (command == 'R' || command == 'r') {
      running = true;
      previousSample = millis();
      SerialBT.println("RUNNING");
    }
  }
}

// ----------------------------------
// SEND SAME WAVEFORM SAMPLES
// ----------------------------------

void sendBluetoothWaveform() {
  if (!SerialBT.hasClient()) return;

  // Format: voltage, maximum, minimum, Vpp
  SerialBT.print(liveVoltage, 3);
  SerialBT.print(",");
  SerialBT.print(vMax, 3);
  SerialBT.print(",");
  SerialBT.print(vMin, 3);
  SerialBT.print(",");
  SerialBT.println(vPP, 3);
}

// ----------------------------------
// SETUP
// ----------------------------------

void setup() {
  Serial.begin(115200);

  analogReadResolution(12);
  analogSetPinAttenuation(SIGNAL_PIN, ADC_11db);

  Wire.begin(21, 22);

  if (!display.begin(0x3C, true)) {
    Serial.println("OLED ERROR");
    while (true) {
      delay(100);
    }
  }

  SerialBT.begin("ESP32_Oscilloscope");

  for (int i = 0; i < GRAPH_WIDTH; i++) {
    samples[i] = 0;
  }

  display.clearDisplay();
  display.setTextColor(SH110X_WHITE);
  display.setCursor(20, 25);
  display.println("OSCILLOSCOPE");
  display.display();

  delay(1000);
}

// ----------------------------------
// LOOP
// ----------------------------------

void loop() {
  checkBluetoothCommands();

  unsigned long now = millis();

  if (running) {
    if (now - previousSample >= SAMPLE_INTERVAL) {
      previousSample = now;

      liveVoltage = readVoltage();

      updateGraph(liveVoltage);
      calculateValues();

      sendBluetoothWaveform();

      Serial.println(liveVoltage, 3);
    }
  }

  if (now - previousDisplay >= 200) {
    previousDisplay = now;
    drawDisplay();
  }
}
