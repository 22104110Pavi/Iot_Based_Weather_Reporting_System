# Iot_Based_Weather_Reporting_System
This project involves using the ESP32 microcontroller to collect environmental data from various sensors and send it to ThingSpeak, a cloud platform for data visualization and analysis. The system can monitor and report real-time weather conditions, making it useful for applications like smart agriculture, home automation, and environmental monitoring.
Components
ESP32 Development Board: The main microcontroller that handles data collection and transmission.
DHT11  Sensor: Measures temperature and humidity.
BMP180 Sensor: Measures atmospheric pressure.
MQ135 Air Quality Sensor: Measures air quality (CO2 levels).
Light Dependent Resistor: Detects light conditions (sunny, dark, cloudy).
Breadboard and Jumper Wires: For easy prototyping and connections.
Power Supply: Typically a USB cable for powering the ESP32.
ThingSpeak Account: A cloud platform to store and visualize the data.

Functionality
Data Collection: The sensors collect environmental data and send it to the ESP32.
Data Processing: The ESP32 processes the raw data and formats it for transmission.
Data Transmission: The ESP32 uses Wi-Fi to send the processed data to ThingSpeak.
Data Visualization: ThingSpeak stores the data and provides tools to visualize it in the form of graphs, charts, and dashboards.


