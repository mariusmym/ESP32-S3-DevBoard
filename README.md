# ESP32-S3 DevBoard

USB-C, battery charging, RGB LED and an ESP32-S3, all squeezed into a board that still fits on a regular breadboard. Everything you need to start messing around with the ESP32-S3.

<img width="800" height="400" alt="esp32-s3" src="https://github.com/user-attachments/assets/cf47ec8c-619f-4c09-a11e-34cfd1266b6c" />


## MAIN FEATURES :

- **ESP32-S3-WROOM-1-N8** – dual-core, Wi-Fi + Bluetooth LE and 8MB of flash. Enough horsepower to make your old ESP8266 projects feel slightly embarrassed.
- **Native USB-C** – no USB-to-serial chip in between, the S3 talks USB all by itself. Proper 5.1kΩ CC resistors included, so even the pickiest USB-C chargers and C-to-C cables will play along.
- **Li-Po / Li-ion battery support** – PH2.0 connector plus a **TP4057** charger, so the battery charges whenever USB is plugged in.
- **Automatic power path** – P-MOSFET + Schottky diode switch between USB and battery on their own. Plug in, unplug, the board doesn't even notice.
- **Dual-color charging status LED** – tells you whether the battery is charging or full, so you don't have to stare at it and guess.
- **XC6210 700mA LDO** – plenty of headroom for Wi-Fi bursts, sensors, and that one extra module you swore you wouldn't add.
- **WS2812B-2020 RGB LED** – fully customizable. Status indicator, mood light, or very small disco: your call.
- **User LED on GPIO2** – for the classic blink sketch. Every board needs one, it's the law.
- **Breadboard friendly** – 2 × 16-pin headers, compact enough to leave free rows on both sides.

<img width="600" height="453" alt="3D-top" src="https://github.com/user-attachments/assets/c1f54b7f-f728-4718-9f3c-d1459db158b6" />


## IMPORTANT INFORMATIONS ! 

1. **Hold down the BOOT button while you plug in the USB cable** to put the board in download mode, then upload your firmware. There is no RESET button, so unplugging and plugging back in *is* the reset button. Minimalism! The number one reason for "Failed to connect to ESP32-S3" errors is forgetting this step; number two is a charge-only USB cable, so check that too 🙃

2. **In Arduino IDE**: this board has only a native USB port (no USB-to-UART chip), so the upload goes through the ESP32-S3's built-in USB. These settings work:
 
| Setting                         | Value                                          |
|---------------------------------|------------------------------------------------|
| Board                           | ESP32-S3-USB-OTG                               |
| Port                            | the COM port that appears when the board is plugged in |
| Upload Mode                     | UART0 / Hardware CDC                           |
| USB Mode                        | USB-OTG                                        |
| Partition Scheme                | Default 4MB with spiffs (1.2MB APP/1.5MB SPIFFS) |
| Core Debug Level                | None                                           |
| Erase All Flash Before Upload   | Disabled                                       |
 

3. **Check battery polarity before connecting!** PH2.0 battery connectors are *not* standardized, and plenty of batteries come with the wires the "wrong" way around. Compare with the markings on the PCB, and swap the pins in the plug if needed. Batteries are very forgiving right up until the moment they aren't.

4. **Use a single-cell 3.7V Li-Po / Li-ion battery only.** The TP4057 charges to 4.2V, so no 2S packs, no LiFePO4, no "I found it in an old vape" adventures. 

5. **Use a stencil when assembling.** Some of the components are pretty small (SC-79 and SOT-416, looking at you), and hand-pasting them is a great way to learn new swear words.

## Quick test

Press BOOT to cycle the RGB LED and toggle the user LED on GPIO2:

```cpp
#include <Adafruit_NeoPixel.h>

#define USER_LED 2
#define RGB_PIN  40
#define BTN_PIN  0   // BOOT button, active LOW

Adafruit_NeoPixel rgb(1, RGB_PIN, NEO_GRB + NEO_KHZ800);

uint32_t colors[4];
uint8_t  idx = 0;            // 0-3 = fixed colors, 4 = rainbow
const uint8_t MODES = 5;
uint16_t hue = 0;

void showColor() {
  if (idx < 4) {
    rgb.setPixelColor(0, colors[idx]);
  } else {
    rgb.setPixelColor(0, rgb.gamma32(rgb.ColorHSV(hue)));
  }
  rgb.show();
}

void setup() {
  pinMode(USER_LED, OUTPUT);
  pinMode(BTN_PIN, INPUT_PULLUP);

  rgb.begin();
  rgb.setBrightness(40); // it's a 2020 LED, not a stadium floodlight

  colors[0] = rgb.Color(255, 0, 0);
  colors[1] = rgb.Color(0, 255, 0);
  colors[2] = rgb.Color(0, 0, 255);
  colors[3] = rgb.Color(255, 255, 255);

  showColor();
}

void loop() {
  static bool lastState = HIGH;
  bool state = digitalRead(BTN_PIN);

  if (state == LOW && lastState == HIGH) {   // button just pressed
    idx = (idx + 1) % MODES;
    digitalWrite(USER_LED, !digitalRead(USER_LED));
    showColor();
  }
  lastState = state;

  if (idx == 4) {                            // rainbow mode
    hue += 256;                              // full wheel in ~5s
    showColor();
  }

  delay(20); // simple debounce + rainbow step
}
```

Red, green, blue, white, then a rainbow, and the IO2 LED flips on every button press? The LEDs and the button all work. Nothing happens when you press? Check `BTN_PIN` before blaming the board. Anything else? The board works but the code is lying.

## Main components 

| Part | Component | LCSC |
|---|---|---|
| MCU | ESP32-S3-WROOM-1-N8 | C2913198 |
| LDO 3.3V / 700mA | XC6210B332MR-G | C47719 |
| Battery charger | TP4057 | C12044 |
| Power path MOSFET | NTA4151PT1G | C54876 |
| Schottky diode | BAS52-02V | C533599 |
| USB-C connector | GT-USB-7010ASV | C2988369 |
| Battery connector | PH2.0 2P SMD | C47647 |
| RGB LED | WS2812B-2020 | C965555 |
| Charging status LED | Everlight 19-223/R6BHC-A05/2T | C131286 |

Full BOM in the **GERBER, BOM, PNP** folder.

## Status 

This project is tested and working as it should. Which, in hardware, is basically a miracle worth documenting.

## If you want to edit the PCB

**Project can also be found here:** [(https://oshwlab.com/noobsmakingstuff/esp32-s3_devbrd_copy)](https://oshwlab.com/noobsmakingstuff/esp32-s3_devbrd_copy)

## License 

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

This project is licensed under the [MIT License](LICENSE).

Do pretty much whatever you want with it (build it, modify it, sell it, put it in your own project), just keep the copyright notice and the license text with it. And if it catches fire, that's on you, not me 🔥.

## Donate 

If you'd like to say thanks or buy me a coffee, a **[PayPal donation](https://www.paypal.com/donate/?hosted_button_id=KHR7DYJP2Z8QJ)** is always appreciated!

Have fun and enjoy it ! 😊
