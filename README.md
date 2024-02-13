![Arduino Soil Analyzer Box](https://github.com/sourceduty/Soil_Analyzer/assets/123030236/e65d65ca-640c-434a-a4cc-a17d2b79c458)

## Arduino-based soil analysis concept.

### DESCRIPTION

The Soil Analyzer is an advanced Arduino-based device designed to provide comprehensive soil analysis by measuring its weight, color, temperature, and moisture content. It employs a combination of specialized sensors, including a load cell with an HX711 amplifier for precise weight measurement, a TCS3200 color sensor for detecting soil color, a waterproof DS18B20 temperature sensor for monitoring soil temperature, and a soil moisture sensor to assess the moisture levels within the soil. This versatile system is structured with a clear and well-organized Arduino code that facilitates real-time data acquisition and logging, making it an invaluable tool for agriculturalists, horticulturists, and environmental researchers to gain in-depth insights into soil conditions, thereby enabling informed decisions for soil management and crop cultivation.

### PARTS

1. Arduino Uno or any compatible microcontroller
2. HX711 Load Cell Amplifier
3. Load Cell (weight sensor)
4. TCS3200 Color Sensor
5. DS18B20 Waterproof Temperature Sensor
6. Soil Moisture Sensor
7. 4.7k Ohm Resistor (for DS18B20)
8. Breadboard and Jumper Wires

### CONCEPT CODE 

```
#include <HX711.h>
#include <Adafruit_TCS34725.h>
#include <OneWire.h>
#include <DallasTemperature.h>

// Pin Definitions
#define LOADCELL_DOUT_PIN  3
#define LOADCELL_SCK_PIN   2
#define ONE_WIRE_BUS       4
#define MOISTURE_SENSOR_PIN A0

// Initialize HX711 for the load cell
HX711 scale;

// Initialize one-wire communication for temperature sensor
OneWire oneWire(ONE_WIRE_BUS);

// Pass our oneWire reference to Dallas Temperature library
DallasTemperature tempSensor(&oneWire);

// Initialize TCS34725 color sensor
Adafruit_TCS34725 colorSensor = Adafruit_TCS34725(TCS34725_INTEGRATIONTIME_50MS, TCS34725_GAIN_4X);

void setup() {
  Serial.begin(9600);

  // Start the HX711 scale
  scale.begin(LOADCELL_DOUT_PIN, LOADCELL_SCK_PIN);

  // Start the DS18B20 temperature sensor
  tempSensor.begin();

  // Start the TCS3200 color sensor
  if (!colorSensor.begin()) {
    Serial.println("Could not find a valid TCS34725 sensor, check wiring!");
    while (1); // Freeze!
  }
}

void loop() {
  measureWeight();
  measureColor();
  measureTemperature();
  measureMoisture();
  delay(5000); // Wait for 5 seconds between readings
}

void measureWeight() {
  scale.set_scale(); // Adjust to this scale factor (you need to find this value through calibration)
  Serial.print("Weight: ");
  Serial.print(scale.get_units(), 2); // Change to scale.get_units(10) for averaging 10 readings
  Serial.println(" grams");
}

void measureColor() {
  uint16_t clear, red, green, blue;
  colorSensor.getRawData(&red, &green, &blue, &clear);
  Serial.print("R: "); Serial.print(red);
  Serial.print(" G: "); Serial.print(green);
  Serial.print(" B: "); Serial.print(blue);
  Serial.println();
}

void measureTemperature() {
  tempSensor.requestTemperatures(); // Send the command to get temperatures
  Serial.print("Temperature: ");
  Serial.print(tempSensor.getTempCByIndex(0)); // Index 0 for the first sensor
  Serial.println(" °C");
}

void measureMoisture() {
  int moistureValue = analogRead(MOISTURE_SENSOR_PIN);
  Serial.print("Moisture: ");
  Serial.println(moistureValue);
}
```

***

🛈 This software is free and open-source; anyone can redistribute it and/or modify it.
