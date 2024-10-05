#define BLYNK_TEMPLATE_ID "TMPL3vCq7ubAw"
#define BLYNK_TEMPLATE_NAME "Weather Reporting System"


#include <Wire.h>
#include <Adafruit_BMP085.h>
#include <DHT.h>
#include <BlynkSimpleEsp32.h>  


// Blynk auth token
char auth[] = "";

// DHT Sensor
#define DHTPIN 15
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);

// MQ135 Sensor
#define MQ135_PIN 36

// LDR Sensor
#define LDR_PIN 34

// BMP180 Sensor
Adafruit_BMP085 bmp;

// Blynk setup
char ssid[] = "";  // Enter your Wi-Fi SSID
char pass[] = "";  // Enter your Wi-Fi password

void setup() {
  Serial.begin(115200);
  Blynk.begin(auth, ssid, pass);

  // Initialize DHT sensor
  dht.begin();

  // Initialize BMP180 sensor
  if (!bmp.begin()) {
    Serial.println("Could not find a valid BMP085/BMP180 sensor!");
  }

  // Initialize ADC for MQ135 and LDR sensors
  analogReadResolution(12); // 12-bit ADC for ESP32
}

void loop() {
  Blynk.run();  // Run Blynk in the loop

  // Read temperature and humidity from DHT11
  float temperature = dht.readTemperature();
  float humidity = dht.readHumidity();

  // Read air quality from MQ135
  int mq135_value = analogRead(MQ135_PIN);
  float mq135_voltage = mq135_value * (3.3 / 4095.0);  // Convert ADC to voltage
  String air_quality = interpretAirQuality(mq135_voltage);

  // Read light level from LDR
  int ldr_value = analogRead(LDR_PIN);
  float light_level = ldr_value * (3.3 / 4095.0);
  String weather = interpretLightLevel(light_level);

  // Read pressure and altitude from BMP180
  float pressure = bmp.readPressure();
  float altitude = bmp.readAltitude(1013.25);

  // Send data to Blynk
  Blynk.virtualWrite(V0, temperature);  // Temperature to V0
  Blynk.virtualWrite(V1, humidity);     // Humidity to V1
  Blynk.virtualWrite(V2, mq135_voltage); // Air quality voltage to V2
  Blynk.virtualWrite(V3, pressure / 100); // Pressure to V3
  Blynk.virtualWrite(V4, light_level);   // Light level to V4

  // Debugging prints to Serial Monitor
  Serial.print("Temp: "); Serial.println(temperature);
  Serial.print("Humidity: "); Serial.println(humidity);
  Serial.print("MQ135 Voltage: "); Serial.println(mq135_voltage);
  Serial.print("Air Quality: "); Serial.println(air_quality);
  Serial.print("Light Level: "); Serial.println(weather);
  Serial.print("Pressure: "); Serial.println(pressure / 100);
  Serial.print("Altitude: "); Serial.println(altitude);

  delay(15000); // Wait 15 seconds
}

// Function to interpret air quality based on MQ135 voltage
String interpretAirQuality(float voltage) {
  if (voltage < 0.5) {
    return "Good";
  } else if (voltage < 1.5) {
    return "Moderate";
  } else if (voltage < 2.5) {
    return "Unhealthy for Sensitive Groups";
  } else if (voltage < 3.0) {
    return "Unhealthy";
  } else {
    return "Very Unhealthy/Hazardous";
  }
}

// Function to interpret light level based on LDR voltage
String interpretLightLevel(float light_level) {
  if (light_level > 3.0) {
    return "Sunny";
  } else if (light_level > 0.0) {
    return "Cloudy";
  } else {
    return "Dark-Night";
  }
}
