---
title: "Building a Wi-Fi Channel Analyzer on ESP32-S3 in C++"
date: 2026-04-02
description: "A walkthrough of designing and building a handheld 2.4 GHz Wi-Fi scanner with overlap-aware congestion scoring and real-time OLED visualization — all on a $15 dev board."
tags: ["C++", "ESP32", "firmware", "Wi-Fi", "embedded", "PlatformIO"]
categories: ["Projects"]
ShowToc: true
TocOpen: true
---

Enterprise site survey tools like Ekahau and AirMagnet charge thousands of dollars for software licenses. The core analysis they perform — scanning the RF environment, counting networks per channel, and recommending the least-congested frequency — is fundamentally straightforward. So I built a version that runs on a \$15 ESP32-S3 dev board, displays results on a tiny OLED, and demonstrates the same wireless domain knowledge in roughly 400 lines of C++.

This post walks through the architecture, the interesting implementation details, and what I learned building firmware that bridges wireless engineering theory with embedded systems practice.

## Why This Project

I work with 802.11 access points professionally — validating firmware across Wi-Fi 6/6E/7 hardware, writing automated test suites, and debugging RF behavior in shielded chambers. I wanted a project that takes that domain knowledge and puts it into a self-contained embedded application: something I could flash onto a board, carry around, and actually use.

The result is a handheld scanner that:

- Discovers every visible SSID with RSSI, channel, BSSID, encryption type, and a signal quality rating
- Computes per-channel congestion scores that account for 802.11 channel overlap
- Recommends the best non-overlapping channel (1, 6, or 11)
- Renders a live channel congestion bar chart on a 128x64 OLED display
- Prints structured reports over the serial monitor

## Architecture

The firmware follows a clean pipeline: scan, analyze, render. Each stage is its own class with a single responsibility.

```
┌─────────────────┐
│  NetworkScanner  │──── ESP32 WiFi.scanNetworks()
│                  │     Returns vector<AccessPointInfo>
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ ChannelAnalyzer  │──── Per-channel stats, overlap scoring,
│                  │     best channel recommendation
└────────┬────────┘
         │
    ┌────┴────┐
    ▼         ▼
┌──────────┐ ┌──────────┐
│ Display  │ │   OLED   │
│ Manager  │ │  Display │
│ (serial) │ │ (I2C)    │
└──────────┘ └──────────┘
```

| Class | Responsibility |
|-------|---------------|
| `AccessPointInfo` | Data struct for a single discovered AP — SSID, BSSID, RSSI, channel, auth type — with formatting helpers |
| `NetworkScanner` | Drives the ESP32 Wi-Fi scan, builds and sorts the result set |
| `ChannelAnalyzer` | Aggregates scan data per channel, computes overlap-aware congestion scores, recommends the best channel |
| `DisplayManager` | Renders scan results and channel reports to the serial monitor |
| `OledDisplay` | Drives the SSD1306 OLED over I2C — draws the channel congestion bar chart and scan summary |

The entry point ties it all together in a standard Arduino `setup()`/`loop()` pattern:

```cpp
void setup() {
    Serial.begin(115200);
    delay(1000);

    WiFi.mode(WIFI_STA);
    WiFi.disconnect();
    delay(100);

    if (!oled.begin()) {
        Serial.println(F("[WARN] OLED display not found — continuing without it"));
    } else {
        oled.showSplash();
    }

    display.printBanner();
}

void loop() {
    auto networks = scanner.scan();

    if (networks.empty()) {
        Serial.println(F("No networks found.\n"));
    } else {
        display.printScanResults(networks);

        auto stats = analyzer.analyze(networks);
        uint8_t best = analyzer.recommendChannel(stats);
        display.printChannelReport(stats, best);
        oled.update(stats, best, networks.size());
    }

    delay(SCAN_INTERVAL_MS);
}
```

A few things worth noting: the OLED is optional. If `oled.begin()` fails (no display connected), the firmware continues with serial-only output. The scan runs every 15 seconds, which is a reasonable interval for a passive monitoring tool — fast enough to catch changes, slow enough to avoid flooding the channel with probe requests.

## The Congestion Scoring Algorithm

This is the most interesting piece from a wireless engineering perspective. A naive channel analyzer just counts networks per channel. But in 2.4 GHz, that misses the critical problem: **channel overlap**.

Each 2.4 GHz channel occupies 22 MHz of bandwidth, but channel centers are spaced only 5 MHz apart. Channel 6 doesn't just compete with other channel-6 networks — it receives interference from anything on channels 2 through 10. Only channels 1, 6, and 11 are fully non-overlapping in the North American channel plan.

The `ChannelAnalyzer` accounts for this with a weighted overlap score:

```cpp
float ChannelAnalyzer::overlapScore(
    uint8_t channel,
    const std::array<ChannelStats, NUM_24GHZ_CHANNELS>& stats) const {

    float score = 0.0f;

    for (int offset = -OVERLAP_RANGE; offset <= OVERLAP_RANGE; ++offset) {
        int neighbor = static_cast<int>(channel) + offset;
        if (neighbor < 1 || neighbor > NUM_24GHZ_CHANNELS) continue;

        uint8_t count = stats[neighbor - 1].network_count;
        if (count == 0) continue;

        float weight = 1.0f - (std::abs(offset) /
                               static_cast<float>(OVERLAP_RANGE + 1));

        float rssi_factor = 1.0f;
        if (stats[neighbor - 1].avg_rssi > -60.0f) {
            rssi_factor = 1.5f;
        } else if (stats[neighbor - 1].avg_rssi < -80.0f) {
            rssi_factor = 0.5f;
        }

        score += count * weight * rssi_factor;
    }

    return std::min(score / 10.0f, 1.0f);
}
```

The key ideas:

1. **Linear weight decay with channel distance.** Co-channel interference (offset 0) gets full weight. A network 4 channels away contributes only 20% weight. This models the spectral mask rolloff of 802.11 DSSS/OFDM transmissions.

2. **Signal-weighted interference.** A strong nearby AP (above -60 dBm) causes 1.5x the interference of a distant one. A weak AP (below -80 dBm) contributes only half. This reflects the reality that received signal strength directly determines co-channel interference impact.

3. **Normalization.** The raw score is divided by 10 and clamped to [0, 1], so the result represents a congestion percentage. Ten or more weighted neighbor-networks means the channel is fully saturated.

I cover the theory behind this algorithm in more depth in my [companion post on 2.4 GHz channel overlap](/posts/channel-overlap-congestion/).

## OLED Visualization

The SSD1306 is a 128x64 monochrome OLED driven over I2C. On a display this small, every pixel matters. The layout:

- **Header row:** Network count ("8 nets") and recommended channel ("Best: 11")
- **Horizontal divider line**
- **Bar chart:** 13 bars for channels 1–13, height proportional to congestion score
- **Labels:** Channel numbers 1, 6, and 11 under the chart

```cpp
void OledDisplay::drawChannelChart(
    const std::array<ChannelStats, ChannelAnalyzer::NUM_24GHZ_CHANNELS>& stats,
    uint8_t recommended_channel) {

    for (uint8_t i = 0; i < NUM_BARS; ++i) {
        float score = stats[i].congestion_score;
        uint8_t bar_h = static_cast<uint8_t>(score * CHART_HEIGHT);
        if (stats[i].network_count > 0 && bar_h < 2) {
            bar_h = 2;
        }

        uint8_t x = i * (BAR_WIDTH + BAR_SPACING);
        uint8_t y = CHART_BOTTOM - bar_h;

        if (stats[i].channel == recommended_channel) {
            uint8_t rec_h = CHART_HEIGHT * 3 / 10;
            oled_.fillRect(x, CHART_TOP, BAR_WIDTH, rec_h, SSD1306_WHITE);
        } else if (bar_h > 0) {
            oled_.fillRect(x, y, BAR_WIDTH, bar_h, SSD1306_WHITE);
        }
    }
}
```

The minimum bar height of 2 pixels ensures that even lightly-used channels are visible. The recommended channel gets a distinctive filled band at the top of the chart so it stands out at a glance.

Bar width is computed at compile time to fill the screen evenly: `(128 - 12 * 1) / 13 = 8 pixels` per bar with 1-pixel spacing. All layout constants are `constexpr`, so no runtime allocation for the display math.

## Hardware

The full BOM costs under \$20:

| Component | Purpose |
|-----------|---------|
| ESP32-S3 dev board (Lonely Binary) | Wi-Fi scanning and computation |
| 0.96" OLED display (SSD1306, 128x64, I2C) | Real-time channel congestion bar chart |
| USB-C cable (data-capable) | Power and serial communication |
| 4 jumper wires (F-F) | I2C connection between ESP32 and OLED |

Wiring is four connections: VCC to 3.3V, GND to GND, SDA to GPIO 8, SCL to GPIO 9. The `Wire.begin(8, 9)` call in the firmware maps the I2C bus to those pins.

One lesson learned: not all USB-C cables carry data. I spent longer than I'd like to admit troubleshooting a power-only cable before the board would enumerate on macOS. If your ESP32 connects but doesn't show a serial port, check the cable first.

## C++ Patterns in Embedded Context

Even on a microcontroller, clean C++ pays off:

- **Namespaces** (`wifiscanner::`) prevent symbol collisions with the Arduino/ESP-IDF libraries without `#define` guards or prefixed names.
- **`std::array` over raw arrays** for channel stats — fixed size, bounds-checkable, passes by value cleanly.
- **`std::vector`** for scan results where the count is unknown at compile time. The ESP32-S3 has 512 KB of SRAM, so dynamic allocation is reasonable for a small result set.
- **`const` methods** on the analyzer — `analyze()` and `recommendChannel()` don't modify state, and marking them `const` makes that guarantee explicit.
- **`constexpr` layout math** for the OLED — all display geometry is resolved at compile time. Zero runtime overhead.

## Sample Output

```
╔══════════════════════════════════════════════════╗
║      ESP32 Wi-Fi Scanner & Channel Analyzer     ║
║                Hunter R. Jamison                 ║
╚══════════════════════════════════════════════════╝

Scanning...

────────────────────────────────────────────────────
  SCAN RESULTS  —  8 networks found
────────────────────────────────────────────────────

  SSID                        RSSI   CH  Security    BSSID              Quality
────────────────────────────────────────────────────
  MyNetwork-5G                -42     6  WPA2/WPA3   A4:CF:12:8B:3E:01  Excellent [|||||]
  Neighbors_WiFi              -58     1  WPA2        B8:27:EB:4A:D2:F3  Good      [||||]
  CoffeeShop                  -65    11  WPA2        DC:A6:32:10:CC:89  Fair      [||| ]
  ...

────────────────────────────────────────────────────
  2.4 GHz CHANNEL ANALYSIS
────────────────────────────────────────────────────

   CH  Networks   Avg RSSI  Congestion
  ---  --------   --------  --------------------
    1      2       -68 dBm   [####......] 40%
    6      3       -59 dBm   [######....] 60%
   11      1       -65 dBm   [##........] 20% << BEST

  >>> Recommended channel: 11
```

## What's Next

The current firmware covers the 2.4 GHz band. Future enhancements I'm planning:

- **5 GHz band scanning** with per-band analysis and DFS channel awareness
- **Web UI** served directly from the ESP32 — connect to the device's IP in a browser to see scan results
- **JSON API endpoint** for programmatic consumption by external tools
- **Continuous mode** with RSSI trending over time
- **Hidden SSID detection** via beacon frame analysis

The full source is on GitHub: [Vulfid/arduinoWiFiAnalyzer](https://github.com/Vulfid/arduinoWiFiAnalyzer)
