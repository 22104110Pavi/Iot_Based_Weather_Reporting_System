from machine import Pin, I2C, ADC
import dht
import urequests
from time import sleep

# Initialize I2C for BMP180 (GPIO 21 for SDA, GPIO 22 for SCL)
i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=100000)

# Initialize DHT11 sensor on GPIO 15
dht_sensor = dht.DHT11(Pin(15))

# Initialize MQ135 sensor on GPIO 36 (ADC1_0)
mq135_sensor = ADC(Pin(36))
mq135_sensor.atten(ADC.ATTN_11DB)  # Set ADC range to 0-3.3V

# Initialize LDR sensor on GPIO 34 (ADC1_6)
ldr_sensor = ADC(Pin(34))
ldr_sensor.atten(ADC.ATTN_11DB)  # Set ADC range to 0-3.3V
gas_threshhold=300
# ThingSpeak API configuration
THINGSPEAK_API_KEY = ''  # Replace with your actual ThingSpeak Write API Key
THINGSPEAK_URL = 'https://api.thingspeak.com/update'

# BMP180 addresses and constants
BMP180_ADDR = 0x77
BMP180_TEMP_CMD = 0x2E
BMP180_PRESSURE_CMD = 0x34
OSS = 0  # Oversampling setting (0 to 3)

# Function to read 16-bit values from BMP180
def read_raw_value(reg):
    msb, lsb = i2c.readfrom_mem(BMP180_ADDR, reg, 2)
    return (msb << 8) + lsb

# Function to read temperature from BMP180
def read_bmp180_temperature():
    i2c.writeto_mem(BMP180_ADDR, 0xF4, bytes([BMP180_TEMP_CMD]))
    sleep(0.005)  # Wait 5 ms
    raw_temp = read_raw_value(0xF6)
    temperature = raw_temp / 10.0  # Convert raw to Celsius if needed
    return temperature

# Function to read pressure from BMP180
def read_bmp180_pressure():
    i2c.writeto_mem(BMP180_ADDR, 0xF4, bytes([BMP180_PRESSURE_CMD + (OSS << 6)]))
    sleep(0.026)  # Wait 26 ms
    msb = i2c.readfrom_mem(BMP180_ADDR, 0xF6, 1)[0]
    lsb = i2c.readfrom_mem(BMP180_ADDR, 0xF7, 1)[0]
    xlsb = i2c.readfrom_mem(BMP180_ADDR, 0xF8, 1)[0]
    raw_pressure = ((msb << 16) + (lsb << 8) + xlsb) >> (8 - OSS)
    pressure = raw_pressure / 100.0  # Convert to hPa if needed
    return pressure

# Function to calculate altitude based on pressure
def calculate_altitude(pressure, sea_level_pressure=1013.25):
    altitude = 44330 * (1.0 - (pressure / sea_level_pressure) ** (1/5.255))
    return altitude

# Function to read MQ135 data and interpret air quality
def read_air_quality():
    analog_value = mq135_sensor.read()
    gasConcentration = (analog_value / 1023.0) * 100
  
    print("Gas Concentration: ")
    print(gasConcentration)
  
  
    if (gasConcentration > 79):
        print("Warning: Gas Leakage Detected!")
  
    voltage = analog_value * (3.3 / 4095)  # Convert the analog value to voltage
    # Interpret air quality based on voltage levels
    if voltage < 0.5:
        quality = "Good"
    elif 0.5 <= voltage < 1.5:
        quality = "Moderate"
    elif 1.5 <= voltage < 2.5:
        quality = "Unhealthy for Sensitive Groups"
    elif 2.5 <= voltage < 3.0:
        quality = "Unhealthy"
    else:
        quality = "Very Unhealthy/Hazardous"
    return analog_value, voltage, quality,gasConcentration

# Function to read light level from LDR
def read_ldr():
    ldr_value = ldr_sensor.read()
    light_level = ldr_value * (3.3 / 4095)
    if light_level>3.0 :
        return "Sunny"
    elif light_level<3.0 and light_level>=0.0:
        return "Cloudy"
    else:
        return "Dark-Night"

# Function to send data to ThingSpeak
def send_to_thingspeak(temp, hum, air_quality,light_level,pressure,altitude):
  
  
    # Construct the URL with data fields
    url = f'{THINGSPEAK_URL}?api_key={THINGSPEAK_API_KEY}&field1={temp}&field2={hum}&field3={air_quality}&field4={pressure}&field5={altitude}&field6={light_level}'

    try:
        # Send data to ThingSpeak using GET request
        response = urequests.get(url)
        print('Data sent to ThingSpeak:', response.text)
        response.close()
    except Exception as e:
        print('Failed to send data to ThingSpeak:', e)

while True:
    try:
        # Read data from DHT11 sensor
        dht_sensor.measure()
        temperature = dht_sensor.temperature()  # Read temperature in Celsius
        humidity = dht_sensor.humidity()        # Read humidity percentage

        mq135_value, mq135_voltage, air_quality ,gaslevel= read_air_quality()

        # Read light level from LDR
        light_level = read_ldr()

        # Print sensor readings
        print(f'Temperature: {temperature}°C')
        print(f'Humidity: {humidity}%')
        print(f'MQ135 Air Quality: {air_quality}')
        """
        print(f'Pressure: {bmp_pressure} hPa')
        print(f'Altitude: {altitude} m')
        """
        print(f'Weather: {light_level}')

        # Send data to ThingSpeak
        send_to_thingspeak(temperature, humidity, mq135_voltage,light_level=2.3,pressure,altitude)

    except OSError as e:
        print('Failed to read from sensors:', e)
    sleep(15)  # Wait 15 seconds before the next reading
