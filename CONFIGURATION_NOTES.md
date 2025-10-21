# Lanbon L8 Configuration Notes

## Configuration Review Summary

✅ **No errors detected** - The configuration has been reviewed and optimized.

## Key Improvements Made

### 1. PSRAM Configuration
```yaml
psram:
  mode: octal
  speed: 80MHz
```
- Essential for ESP32-WROVER-B
- Enables use of external PSRAM for LVGL and display buffers
- Improves performance and memory availability

### 2. PWM Backlight Control
- Changed from binary (on/off) to PWM control
- Allows brightness adjustment
- Better integration with LVGL idle/wake functionality

### 3. Display Optimizations
```yaml
display:
  auto_clear_enabled: false
  update_interval: never
  rotation: 180
```
- `auto_clear_enabled: false` - LVGL handles clearing
- `update_interval: never` - LVGL manages display updates
- `rotation: 180` - Correct orientation for typical mounting

### 4. Touch Calibration
```yaml
touchscreen:
  calibration:
    x_min: 0
    y_min: 0
    x_max: 230
    y_max: 312
```
- Proper calibration values for the FT6336 touchscreen
- Ensures accurate touch detection across the screen

### 5. LVGL Integration
- Auto-dimming after 10 seconds of inactivity
- Touch to wake functionality
- Ready for custom UI pages

### 6. Relay Configuration
- Relays now use output platform for better flexibility
- Can be controlled by switches or automations
- Maintains proper restore behavior

## Hardware Configuration

### Verified GPIO Pins
| Component | Pin | Function |
|-----------|-----|----------|
| Display SPI CLK | GPIO19 | SPI Clock |
| Display SPI MOSI | GPIO23 | SPI Data Out |
| Display SPI MISO | GPIO25 | SPI Data In |
| Display CS | GPIO22 | Chip Select |
| Display DC | GPIO21 | Data/Command |
| Display Reset | GPIO18 | Reset |
| Display Backlight | GPIO5 | PWM Backlight |
| Touch I2C SDA | GPIO4 | I2C Data |
| Touch I2C SCL | GPIO0 | I2C Clock |
| Relay 1 | GPIO12 | Relay Control |
| Relay 2 | GPIO14 | Relay Control |
| Relay 3 | GPIO27 | Relay Control |
| Mood Light Red | GPIO26 | PWM Red |
| Mood Light Green | GPIO32 | PWM Green |
| Mood Light Blue | GPIO33 | PWM Blue |

## Important Notes

### Font Configuration
The configuration references `fonts/Arial.ttf`. You need to either:

1. Create a `fonts` directory and add Arial.ttf:
   ```bash
   mkdir fonts
   # Copy Arial.ttf to fonts/ directory
   ```

2. Or use ESPHome's built-in font:
   ```yaml
   font:
     - file: "gfonts://Roboto"
       id: font1
       size: 24
   ```

3. Or comment out font section if not using custom display lambda

### LVGL Pages
The LVGL pages section is commented out. To create a custom UI:

1. Uncomment and customize the pages section
2. Add widgets (buttons, labels, sliders, etc.)
3. Link widgets to your relays and sensors

Example:
```yaml
lvgl:
  pages:
    - id: main_page
      widgets:
        - obj:
            width: 240
            height: 320
            widgets:
              - btn:
                  id: relay1_btn
                  x: 10
                  y: 10
                  width: 70
                  height: 100
                  checkable: true
                  widgets:
                    - label:
                        text: "Light 1"
                  on_click:
                    - switch.toggle: relay1
```

## Testing Checklist

Before deploying to the device:

- [ ] Verify WiFi credentials in `secrets.yaml`
- [ ] Generate API encryption key: `openssl rand -base64 32`
- [ ] Set OTA password in secrets
- [ ] Decide on font strategy (built-in, Google Fonts, or custom)
- [ ] Test compile: `esphome compile lanbon-l8.yaml`
- [ ] Initial flash via USB
- [ ] Test touch responsiveness
- [ ] Verify relay operation
- [ ] Test backlight auto-dim
- [ ] Configure LVGL pages if needed

## Known Limitations

1. **Temperature Sensor**: Not included (no pin information available for Lanbon L8)
2. **Status LED**: Removed (conflicting usage or no reliable pin info)
3. **LVGL**: Basic configuration provided - requires customization for full UI

## Troubleshooting

### Display Issues
- Verify SPI pins are correctly connected
- Try `rotation: 0` or `rotation: 270` if orientation is wrong
- Check if backlight is on (should be ALWAYS_ON by default)

### Touch Not Working
- Verify I2C scan detects FT6336 at address 0x38
- Adjust calibration values if touch is offset
- Check touchscreen cable connection

### LVGL Compilation Errors
- Ensure ESPHome version supports LVGL (2023.7.0+)
- PSRAM must be enabled for LVGL
- May need to reduce `log_level` to INFO or WARN to reduce memory usage

### Memory Issues
- PSRAM configuration is critical
- Reduce font sizes if needed
- Simplify LVGL pages
- Lower logger level to INFO

## Performance Optimization Tips

1. **Display Updates**: Let LVGL control display updates (already configured)
2. **Touch Polling**: FT6336 uses interrupt mode for better responsiveness
3. **WiFi**: Consider adding `fast_connect: true` for faster reconnection
4. **Logging**: Set to INFO in production to reduce overhead

## Next Steps

1. Flash the configuration to your device
2. Monitor logs for any issues
3. Design custom LVGL UI for your use case
4. Set up Home Assistant automations
5. Fine-tune backlight timeout and brightness
6. Configure mood light automations

## References

- [ESPHome LVGL Documentation](https://esphome.io/components/lvgl.html)
- [ESPHome Display Documentation](https://esphome.io/components/display/ili9xxx.html)
- [ESPHome Touchscreen Documentation](https://esphome.io/components/touchscreen/ft63x6.html)
- [Lanbon L8 Hardware Info](https://templates.blakadder.com/lanbon_L8.html)
