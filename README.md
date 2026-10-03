# ESP32-S3 DevBoard

USB-C, battery charging, RGB LED and an ESP32-S3, all squeezed into a board that still fits on a regular breadboard. Everything you need to start messing around with the ESP32-S3, and nothing you need to apologize for.

<img width="600" height="450" alt="IMG_8784" src="https://github.com/user-attachments/assets/6595d8a6-92f0-4970-9ed4-9bfd18eacc0f" />


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

2. **In Arduino IDE** select *ESP32S3 Dev Module* from the ESP32 board package and set:
   - **USB CDC On Boot → Enabled** – otherwise `Serial.print()` will be shouting into the void.
   - **Flash Size → 8MB**
   - **PSRAM → Disabled** (the N8 module has no PSRAM, so don't go looking for it)

3. **Check battery polarity before connecting!** PH2.0 battery connectors are *not* standardized, and plenty of batteries come with the wires the "wrong" way around. Compare with the markings on the PCB, and swap the pins in the plug if needed. Batteries are very forgiving right up until the moment they aren't.

4. **Use a single-cell 3.7V Li-Po / Li-ion battery only.** The TP4057 charges to 4.2V, so no 2S packs, no LiFePO4, no "I found it in an old vape" adventures.

5. **Use a stencil when assembling.** Some of the components are pretty small (SC-79 and SOT-416, looking at you), and hand-pasting them is a great way to learn new swear words.

## Quick test 

Blink the user LED on GPIO2 and cycle the RGB LED:

```cpp
#include <Adafruit_NeoPixel.h>

#define USER_LED 2
#define RGB_PIN  0   // <- change to the WS2812B pin from the schematic

Adafruit_NeoPixel rgb(1, RGB_PIN, NEO_GRB + NEO_KHZ800);

void setup() {
  pinMode(USER_LED, OUTPUT);
  rgb.begin();
  rgb.setBrightness(40); // it's a 2020 LED, not a stadium floodlight
}

void loop() {
  uint32_t colors[] = { rgb.Color(255, 0, 0), rgb.Color(0, 255, 0), rgb.Color(0, 0, 255) };
  for (uint32_t c : colors) {
    digitalWrite(USER_LED, !digitalRead(USER_LED));
    rgb.setPixelColor(0, c);
    rgb.show();
    delay(500);
  }
}
```

Red, green, blue and a blinking yellow LED? The board works. Anything else? The board works and the code is lying.

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

## Repository content 

- **GERBER, BOM, PNP** – everything needed to order the PCB (and assembly, if tiny components aren't your idea of a relaxing weekend) from JLCPCB or your favorite fab.
- **SCHEMATIC** – the schematic in PDF.
- **Images** – photos and renders.

## Status 

This project is tested and working as it should. Which, in hardware, is basically a miracle worth documenting.

## If you want to edit the PCB

**Project can also be found here:** https://oshwlab.com/mariusmym/PROJECT-LINK-HERE

## License 

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

This project is licensed under the [MIT License](LICENSE).

In human words: do pretty much whatever you want with it (build it, modify it, sell it, put it in your own project), just keep the copyright notice and the license text with it. And if it catches fire, that's on you, not me 🔥.

## Donate 

If you'd like to say thanks or buy me a coffee, a **[PayPal donation](https://www.paypal.com/donate/?hosted_button_id=KHR7DYJP2Z8QJ)** is always appreciated!

Have fun and enjoy it ! 😊
