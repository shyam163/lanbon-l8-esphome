# Lanbon L8 ESPHome Configuration

This is an ESPHome configuration for the Lanbon L8 smart wall switch with touchscreen display.

## Features

- **3 Relay Control**: Control 3 separate relays for lights/appliances
- **Touchscreen Interface**: 240x320 ST7789V display with FT6336 touch controller
- **LVGL UI Framework**: Advanced UI with auto-dimming and touch-to-wake
- **RGB Mood Light**: Controllable RGB LED for ambient lighting
- **PWM Backlight**: Dimmable backlight with brightness control
- **PSRAM Support**: Optimized for ESP32-WROVER-B with octal PSRAM
- **WiFi Connectivity**: Connect to Home Assistant or other home automation platforms
- **OTA Updates**: Over-the-air firmware updates

## Hardware Specifications

- **MCU**: ESP32-WROVER-B with 8MB PSRAM (octal mode)
- **Display**: ST7789V (240x320 pixels) via ili9xxx platform
- **Touch Controller**: FT6336 (I2C, calibrated)
- **Relays**: 3x GPIO-controlled relays
- **RGB Mood Light**: Built-in RGB LED (PWM controlled)
- **Backlight**: PWM dimmable backlight

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

### 3. Configure Font

Choose one of these options:

**Option A - Use Google Fonts (recommended):**
Edit `lanbon-l8.yaml` and change font section to:
```yaml
font:
  - file: "gfonts://Roboto"
    id: font1
    size: 24
```

**Option B - Use custom font:**
```bash
mkdir -p fonts
# Copy your .ttf font file to fonts/ directory
```

**Option C - Comment out font section** if using LVGL only

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

### Configure LVGL UI

The configuration includes LVGL framework for advanced UI. Customize the pages section:

```yaml
lvgl:
  pages:
    - id: main_page
      widgets:
        - btn:
            id: relay1_btn
            checkable: true
            widgets:
              - label:
                  text: "Light 1"
            on_click:
              - switch.toggle: relay1
```

See [ESPHome LVGL docs](https://esphome.io/components/lvgl.html) for widget examples.

### Change Relay Behavior

Modify the `restore_mode` to change how relays behave after power loss:
- `RESTORE_DEFAULT_OFF`: Always off after reboot
- `RESTORE_DEFAULT_ON`: Always on after reboot
- `RESTORE_INVERTED_DEFAULT_OFF`: Restore previous state, default off

### Adjust Backlight Timeout

Modify the idle timeout in the LVGL section:

```yaml
lvgl:
  on_idle:
    timeout: 30s  # Change from 10s to 30s
    then:
      - light.turn_off: backlight
      - lvgl.pause:
```

### Backlight Brightness

Control backlight brightness from Home Assistant or automations:

```yaml
# Set to 50% brightness
- light.turn_on:
    id: backlight
    brightness: 50%
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
