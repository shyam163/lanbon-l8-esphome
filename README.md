# Lanbon L8 ESPHome Configuration

This is an ESPHome configuration for the Lanbon L8 smart wall switch with touchscreen display.

## Features

- **3 Relay Control**: Control 3 separate relays for lights/appliances
- **Touchscreen Interface**: 240x320 ST7789V display with FT6336 touch controller
- **RGB Mood Light**: Controllable RGB LED for ambient lighting
- **Display Backlight**: Controllable backlight (GPIO5)
- **WiFi Connectivity**: Connect to Home Assistant or other home automation platforms
- **OTA Updates**: Over-the-air firmware updates

## Hardware Specifications

- **MCU**: ESP32-WROVER-B
- **Display**: ST7789V (240x320 pixels) via ili9xxx platform
- **Touch Controller**: FT6336 (I2C)
- **Relays**: 3x GPIO-controlled relays
- **RGB Mood Light**: Built-in RGB LED

## Setup Instructions

### 1. Configure Secrets

Edit `secrets.yaml` with your credentials:

```yaml
wifi_ssid: "YourWiFiSSID"
wifi_password: "YourWiFiPassword"
fallback_password: "fallback12345"
api_encryption_key: "generate_using_openssl"
ota_password: "your_ota_password"
```

Generate API encryption key:
```bash
openssl rand -base64 32
```

### 2. Install ESPHome

```bash
pip install esphome
```

### 3. Download Font (Optional)

If you want to use custom fonts on the display, download a TrueType font:

```bash
mkdir -p fonts
# Download Arial or any other font you prefer
```

Or comment out the font section in the YAML if not needed.

### 4. Compile and Upload

First time (via USB):
```bash
esphome run lanbon-l8.yaml
```

After initial flash (OTA):
```bash
esphome run lanbon-l8.yaml --device lanbon-l8.local
```

### 5. Add to Home Assistant

Once flashed and connected to WiFi, the device should auto-discover in Home Assistant if you have the ESPHome integration installed.

## Pin Configuration

### SPI (Display)
| Pin | Function |
|-----|----------|
| GPIO19 | CLK |
| GPIO23 | MOSI |
| GPIO25 | MISO |
| GPIO22 | CS |
| GPIO21 | DC |
| GPIO18 | Reset |

### I²C (Touchscreen)
| Pin | Function |
|-----|----------|
| GPIO4 | SDA |
| GPIO0 | SCL |

### Display
| Pin | Function |
|-----|----------|
| GPIO5 | Backlight |

### Relays (3-gang model)
| Pin | Function |
|-----|----------|
| GPIO12 | Relay #1 |
| GPIO14 | Relay #2 |
| GPIO27 | Relay #3 |

### RGB Mood Light
| Pin | Function |
|-----|----------|
| GPIO26 | Red |
| GPIO32 | Green |
| GPIO33 | Blue |

## Customization

### Modify Touch Areas

Adjust the touch button coordinates in the `binary_sensor` section to match your UI layout:

```yaml
binary_sensor:
  - platform: touchscreen
    x_min: 0
    x_max: 80
    y_min: 0
    y_max: 240
```

### Change Relay Behavior

Modify the `restore_mode` to change how relays behave after power loss:
- `RESTORE_DEFAULT_OFF`: Always off after reboot
- `RESTORE_DEFAULT_ON`: Always on after reboot
- `RESTORE_INVERTED_DEFAULT_OFF`: Restore previous state, default off

### Custom Display Content

Modify the `display` lambda to show custom information:

```yaml
lambda: |-
  it.print(0, 0, id(font1), "Custom Text");
  it.printf(0, 30, id(font1), "Temp: %.1f°C", id(temperature).state);
```

## Troubleshooting

### Device not connecting to WiFi
- Check WiFi credentials in `secrets.yaml`
- Look for the fallback AP and connect to configure WiFi

### Touch not working
- Verify I2C pins (GPIO4=SDA, GPIO0=SCL) are correct
- FT6336 doesn't require interrupt pin configuration

### Relays not switching
- Verify relay GPIO pins match your hardware
- Check if relays need inverted logic

### Display issues
- Confirm SPI pins are correct (CLK=GPIO19, MOSI=GPIO23, MISO=GPIO25)
- Verify CS=GPIO22, DC=GPIO21, Reset=GPIO18
- Adjust rotation parameter (0, 90, 180, 270)
- Check backlight is enabled (GPIO5)

## Notes

- Pin assignments may vary between different Lanbon L8 hardware revisions
- Some pins might need adjustment based on your specific model
- The configuration assumes 3-gang version; adjust for 1-gang or 2-gang versions

## References

- [ESPHome Documentation](https://esphome.io/)
- [Lanbon L8 Hardware Info](https://templates.blakadder.com/lanbon_L8.html)
