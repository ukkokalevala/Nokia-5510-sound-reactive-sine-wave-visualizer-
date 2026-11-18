Pocket Sound Wave — ESP8266 + Nokia 5510 Audio Visualizer
A compact real-time audio visualizer that turns ambient sound into a smooth, scrolling sine wave on a classic Nokia 5510 LCD (84×48 monochrome, PCD8544). An electret condenser microphone module (KE0034) captures sound on the ESP8266's analog pin, and the wave's amplitude, frequency, and scroll speed all react to volume — quiet rooms produce a gentle ripple, loud music produces a big fast-moving wave. A status LED pulses on beats.

Features
Smooth integer-math sine wave (256-entry lookup table, no float drift)

Amplitude, frequency, and scroll speed all driven by a single smoothed envelope

Auto-calibrated noise floor at startup (measures the mic's resting DC bias)

Guaranteed minimum amplitude — the wave never goes flat, even in silence

Sound-triggered LED on D0 with hold timer (no flicker)

Runs at ~30 FPS on a 4 MHz SPI bus

No FFT, no I2S — works with any analog sound sensor

Hardware
Component	Connection
Nokia 5510 LCD RST	D2
LCD CE/CS	D1
LCD DC	D6
LCD DIN/MOSI	D7
LCD CLK/SCK	D5
LCD VCC / BL	3.3V (BL via 47Ω–330Ω)
LCD GND	GND
KE0034 S (signal)	A0
KE0034 VCC / GND	3.3V / GND
LED anode	D0 via 220Ω
LED cathode	GND
Software
Libraries: Adafruit_PCD8544, Adafruit_GFX

Key tuning values:

MAP_MAX_RAW — set to your mic's peak raw value (tune after baseline calibration)

NOISE_FLOOR — silence cutoff

AMP_MIN / AMP_MAX — wave height range (3–18px)

FREQ_MIN / FREQ_MAX — wave density (12–40)

LCD_CONTRAST — 100 for dim clones, 45–60 for genuine modules

How it works
The KE0034 outputs an analog voltage that rises and falls with sound. The ESP8266 samples it on A0, subtracts a baseline measured at startup (so silence reads as zero), smooths the result into an envelope, and maps it to three wave parameters: amplitude (how tall), frequency (how many waves fit on screen), and phase increment (scroll speed). The wave is drawn pixel-by-pixel using a precomputed sine lookup table with pure integer math — no floats in the hot path, no phase wraparound glitches. A uint8_t phase counter wraps naturally at 256, giving seamless scrolling forever.


