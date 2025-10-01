# ESP32 Relay Controller (4-Channel)

This project configures an **ESP32 Relay Development Board (ESP32-WROOM, 4-Channel Relay, AC/DC Power Supply)** to control outdoor lights and a 12V solenoid valve.  
The system integrates with **Home Assistant** for scheduling, weather-based automation, and manual override.  

---

## Features

- **4 Channel Relay Control**
  - Relay 1: Outdoor Lights (scheduled 19:00–22:30, manual override button)
  - Relay 2: Solenoid Valve (runs at 10:00am for 30 min, weather + humidity conditions, manual override button, failsafe auto-off)
  - Relay 3: Free for user
  - Relay 4: Free for user

- **Manual Override Buttons**
  - Each relay can be toggled via physical button
  - Schedule and failsafe always enforce correct state after override

- **Failsafe Timer**
  - Solenoid always turns off after 30 minutes, even if ESP32 restarts mid-cycle

- **LED Indicators**
  - Solenoid: Blinking LED while watering (GPIO2 by default)
  - (Optional) Other relays can be mapped to LEDs

- **Sensors**
  - DHT22 temperature/humidity sensor (GPIO21)
  - (Optional) Battery voltage monitoring with ADC + resistor divider

- **Home Assistant Integration**
  - Syncs time from HA
  - Reads weather and humidity from HA entities
  - Fully controllable from HA dashboard

---

## Hardware Setup

- **Board**: ESP32 Relay Dev Board (ESP32-WROOM, 4-Channel)
- **Relays**: 10A rated, used to switch:
  - 12V Outdoor Lights
  - 12V Normally Closed Solenoid Valve
- **Power**: 12V Car Battery with charger connected
- **Sensors**:
  - DHT22 → GPIO21
  - Battery voltage monitor → voltage divider into GPIO36
- **LED**:
  - Status LED for solenoid → GPIO2 (onboard LED)

### Wiring Notes

- Solenoid and lights are powered by **12V battery through relays**.  
- ESP32 is powered by onboard regulator from the same 12V source.  
- Voltage divider needed for battery monitoring:  
  - Example: R1 = 100kΩ, R2 = 27kΩ → safe scaling to ESP32 ADC.

---

## YAML Configuration

ESP32-X4-Relay.yaml

[`ESP32-X4-Relay.yaml`](ESP32-X4-Relay.yaml).  
Key sections:
- Relays, Buttons, LED Indicator
- Light schedule
- Solenoid watering automation with HA weather + humidity
- Failsafe timer script
- Optional DHT22 + battery sensor

---

## How It Works

1. **Outdoor Lights**  
   - Turn on automatically at 19:00, off at 22:30.  
   - Manual button toggle allowed.  
   - Scheduler enforces correct state.

2. **Water Solenoid**  
   - Runs daily at 10:00am for 30 minutes **only if**:
     - Forecast is not rain (from HA weather entity)
     - Humidity < 80% (from HA sensor)  
   - Blinking LED shows active watering.  
   - Manual button toggle allowed.  
   - Always auto-off after 30 minutes.

---

## Home Assistant Entities Required

- `weather.home` → Provides forecast state
- `sensor.outdoor_humidity` → Provides humidity %

You can adapt entity names in the YAML to match your HA setup.

---

## Optional: Battery Voltage Monitoring

- Add resistor divider to GPIO36  
- Uncomment ADC sensor in YAML  
- Calibrate multiplier with multimeter for accuracy  

---

## Future Improvements

- LED indicators for other relays  
- Web UI for local status and control  
- Soil moisture sensor input for better watering logic  

---

## License

MIT License
