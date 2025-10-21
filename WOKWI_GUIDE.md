# Wokwi Simulation Guide for Lanbon L8

This guide explains how to use the Wokwi diagram to simulate the Lanbon L8 hardware.

## What is Wokwi?

Wokwi is an online electronics simulator that allows you to test ESP32 projects in a browser without physical hardware.

## Included Components

The `diagram.json` file includes the following components matching the Lanbon L8 hardware:

### Main Components
- **ESP32 DevKit V1** - with 8MB PSRAM configured
- **ILI9341 Display** - 240x320 ST7789V compatible display
- **3x Relay Modules** - for controlling lights/appliances
- **RGB LED** - representing the mood light
- **White LED** - representing the backlight

### GPIO Connections

| Component | ESP32 Pin | Function |
|-----------|-----------|----------|
| **Display (SPI)** | | |
| SCK | GPIO19 | SPI Clock |
| SDI (MOSI) | GPIO23 | SPI Data Out |
| SDO (MISO) | GPIO25 | SPI Data In |
| CS | GPIO22 | Chip Select |
| D/C | GPIO21 | Data/Command |
| RST | GPIO18 | Reset |
| **Backlight** | GPIO5 | PWM Control |
| **Relays** | | |
| Relay 1 | GPIO12 | Control Signal |
| Relay 2 | GPIO14 | Control Signal |
| Relay 3 | GPIO27 | Control Signal |
| **RGB Mood Light** | | |
| Red | GPIO26 | PWM Red Channel |
| Green | GPIO32 | PWM Green Channel |
| Blue | GPIO33 | PWM Blue Channel |

## How to Use with Wokwi

### Option 1: Wokwi for VS Code

1. **Install Wokwi Extension**
   - Open VS Code
   - Install "Wokwi Simulator" extension
   - Requires Wokwi Club subscription for ESP32

2. **Open Project**
   ```bash
   code .
   ```

3. **Start Simulation**
   - Press `F1`
   - Type "Wokwi: Start Simulator"
   - Or click the Wokwi icon in the status bar

### Option 2: Wokwi Website

1. **Go to Wokwi**
   - Visit https://wokwi.com
   - Sign in to your account

2. **Create New Project**
   - Click "New Project"
   - Select "ESP32"

3. **Import Diagram**
   - Delete the default `diagram.json`
   - Copy the contents of our `diagram.json`
   - Paste into Wokwi's `diagram.json`

4. **Add Code**
   - You'll need to add Arduino/PlatformIO code
   - ESPHome doesn't run directly in Wokwi browser version

### Option 3: Standalone Wokwi CLI

1. **Install Wokwi CLI**
   ```bash
   npm install -g @wokwi/cli
   ```

2. **Run Simulation**
   ```bash
   wokwi-cli --diagram diagram.json --elf .esphome/build/lanbon-l8/.pioenvs/lanbon-l8/firmware.elf
   ```

## Limitations

### Wokwi Limitations
- **I2C Touch Controller**: Wokwi doesn't have FT6336 component
  - Touch input simulation not available
  - GPIO4 (SDA) and GPIO0 (SCL) not shown in diagram

- **PSRAM**: Simulated but not fully accurate
  - Configured as 8MB octal PSRAM
  - Actual timing may differ from real hardware

- **LVGL**: Complex UI rendering may be slower
  - Performance doesn't match real ESP32-WROVER-B

- **Display Model**: Using ILI9341 instead of ST7789V
  - Very similar, same driver in ESPHome
  - Color/timing differences may exist

### ESPHome in Wokwi
- ESPHome firmware can run in Wokwi CLI mode
- Need to compile firmware first: `esphome compile lanbon-l8.yaml`
- Upload the `.elf` file to Wokwi

## Testing Scenarios

### What You Can Test

✅ **Relay Control**
- Toggle relays on/off
- Test restore modes
- Verify GPIO assignments

✅ **RGB Mood Light**
- Test color mixing
- Verify PWM channels
- Check brightness levels

✅ **Backlight Control**
- Test PWM dimming
- Verify on/off functionality
- Check brightness range

✅ **Display Communication**
- Verify SPI connections
- Test display initialization
- Check basic rendering

### What You Cannot Test

❌ **Touch Input** - No FT6336 component available
❌ **LVGL UI** - Limited support, may not render correctly
❌ **WiFi** - Wokwi has limited WiFi simulation
❌ **Real-time Performance** - Timing differs from real hardware

## Modifying the Diagram

### Add/Remove Components

Edit `diagram.json`:

```json
{
  "parts": [
    {
      "type": "wokwi-led",
      "id": "new_led",
      "top": 100,
      "left": 100,
      "attrs": { "color": "red" }
    }
  ]
}
```

### Change Connections

Add to `connections` array:

```json
[
  "esp32:D2",
  "new_led:A",
  "red",
  ["h0"]
]
```

### Component Types Available

- `wokwi-esp32-devkit-v1` - ESP32 board
- `wokwi-ili9341` - SPI Display
- `wokwi-relay-module` - Relay
- `wokwi-rgb-led` - RGB LED
- `wokwi-led` - Single color LED
- `wokwi-resistor` - Resistor
- `wokwi-pushbutton` - Button
- Many more at https://docs.wokwi.com/parts/

## Alternative: Physical Hardware Testing

For full functionality testing, use actual Lanbon L8 hardware:

1. Flash via USB: `esphome run lanbon-l8.yaml`
2. Monitor logs: `esphome logs lanbon-l8.yaml`
3. Test all features including touch and LVGL

## Troubleshooting

### Simulation Won't Start
- Check JSON syntax in `diagram.json`
- Verify all component IDs are unique
- Ensure all connections reference valid pins

### Display Not Working
- Verify SPI pin connections
- Check if firmware includes display driver
- Try reducing display update frequency

### Relays Not Responding
- Check GPIO pin numbers match
- Verify relay module has power (VCC/GND)
- Test with simple blink sketch first

## Resources

- [Wokwi Documentation](https://docs.wokwi.com/)
- [Wokwi ESP32 Guide](https://docs.wokwi.com/guides/esp32)
- [ESPHome Documentation](https://esphome.io/)
- [Lanbon L8 Templates](https://templates.blakadder.com/lanbon_L8.html)

## Notes

- Wokwi is great for testing GPIO connections and basic logic
- For LVGL and complex features, physical hardware is recommended
- The diagram serves as excellent documentation of pin assignments
- Use this for education and prototyping before hardware deployment
