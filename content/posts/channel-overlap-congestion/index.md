---
title: "2.4 GHz Channel Overlap and Congestion Scoring — The Theory Behind the Code"
date: 2026-04-01
description: "Why 'just pick channel 1, 6, or 11' oversimplifies 2.4 GHz planning, and how to build an overlap-aware congestion model that accounts for spectral width, adjacent-channel interference, and signal strength."
tags: ["Wi-Fi", "802.11", "RF", "wireless", "channel-planning"]
categories: ["Wireless Engineering"]
ShowToc: true
TocOpen: true
---

If you've ever configured a home router, you've probably seen the advice: "use channel 1, 6, or 11." That's correct as far as it goes, but it skips over *why* those three channels matter and what happens when you (or your neighbors) ignore the rule. Understanding the underlying RF behavior is essential for anyone working with wireless firmware, site survey tools, or access point configuration at scale.

This post covers the 2.4 GHz channel structure, explains co-channel and adjacent-channel interference, and walks through an overlap-aware congestion scoring algorithm I implemented in C++ for an [ESP32-based Wi-Fi analyzer](/posts/wifi-channel-analyzer/).

## The 2.4 GHz Channel Plan

The 2.4 GHz ISM band allocates spectrum from 2.400 GHz to 2.4835 GHz. The IEEE 802.11 standard defines 14 channels within this range (13 in most regulatory domains; 14 is Japan-only for 802.11b). Each channel is identified by its center frequency:

| Channel | Center Frequency |
|---------|-----------------|
| 1 | 2.412 GHz |
| 2 | 2.417 GHz |
| 3 | 2.422 GHz |
| 4 | 2.427 GHz |
| 5 | 2.432 GHz |
| 6 | 2.437 GHz |
| 7 | 2.442 GHz |
| 8 | 2.447 GHz |
| 9 | 2.452 GHz |
| 10 | 2.457 GHz |
| 11 | 2.462 GHz |
| 12 | 2.467 GHz |
| 13 | 2.472 GHz |

Channel centers are spaced **5 MHz apart**. But each channel occupies approximately **22 MHz of bandwidth** (the spectral mask for 802.11 DSSS and legacy OFDM transmissions). This creates the fundamental tension in 2.4 GHz planning: there are 13 channel numbers, but only enough spectrum for **three non-overlapping channels**.

## Why Only 1, 6, and 11?

For two channels to be non-overlapping, their center frequencies must be at least 25 MHz apart (the 22 MHz channel width plus a small guard margin). With 5 MHz spacing between channel centers, that means a separation of at least 5 channels:

- Channel 1 (2.412 GHz) to Channel 6 (2.437 GHz) = 25 MHz separation
- Channel 6 (2.437 GHz) to Channel 11 (2.462 GHz) = 25 MHz separation
- Channel 1 (2.412 GHz) to Channel 11 (2.462 GHz) = 50 MHz separation

Any other combination — say channels 1, 4, and 8 — results in spectral overlap between at least two of the channels. This is why enterprise wireless deployments exclusively use 1, 6, and 11 for their 2.4 GHz radios (in the FCC regulatory domain).

```
Channel:   1    2    3    4    5    6    7    8    9   10   11

           ├─────────22 MHz──────────┤
                                     ├─────────22 MHz──────────┤
                                                                ├─────────22 MHz──────────┤
           |    |    |    |    |    |    |    |    |    |    |
         2.412       2.427       2.442       2.457       2.472  (GHz)
                        5 MHz spacing
```

Channels 1, 6, and 11 tile the band with minimal overlap. Every other arrangement creates interference between neighbors.

## Co-Channel vs. Adjacent-Channel Interference

Two types of interference affect 2.4 GHz performance, and they behave very differently.

### Co-Channel Interference (CCI)

When two access points operate on the same channel, they share the medium. 802.11 uses CSMA/CA (Carrier Sense Multiple Access with Collision Avoidance) — devices listen before transmitting and defer if they detect energy above the Clear Channel Assessment (CCA) threshold. Co-channel APs within detection range of each other will take turns, effectively splitting airtime.

CCI is *managed* by the protocol. It reduces throughput (more contention, more deferrals) but doesn't cause corruption. The 802.11 MAC is designed to handle it.

### Adjacent-Channel Interference (ACI)

When two APs operate on overlapping but different channels — say channel 4 and channel 6 — their spectral masks overlap. But because they're on different channels, 802.11's CSMA/CA mechanism doesn't help. The APs don't detect each other as same-channel peers, so they don't defer. They transmit simultaneously, and the overlapping energy appears as noise to each other's receivers.

ACI is **not managed** by the protocol. It raises the noise floor, degrades the signal-to-noise ratio, and causes frame errors that require retransmissions. In many cases, adjacent-channel overlap is worse than co-channel sharing, because at least co-channel devices cooperate.

This is the core reason the wireless industry recommends strict use of 1, 6, and 11. Using channel 3 doesn't give you a "less crowded" frequency — it gives you unmanaged interference with everyone on channels 1 through 7.

## Modeling Overlap in Code

For the [ESP32 Wi-Fi analyzer](https://github.com/Vulfid/arduinoWiFiAnalyzer), I needed a congestion score that reflects this reality. A naive "count networks per channel" metric treats channel 6 with three co-channel APs as equal to channel 3 with three APs. But channel 3 likely has *additional* unmodeled interference from channels 1 and 6 that the simple count misses.

The overlap scoring algorithm considers every channel within a ±4 channel window (the `OVERLAP_RANGE`), applies a distance-based weight, and factors in signal strength.

### The Weight Function

The interference contribution of a neighboring channel decreases with spectral distance. A co-channel AP (offset 0) contributes full weight. An AP 4 channels away contributes minimal weight — its spectral mask barely overlaps.

The weight function is linear:

```
weight = 1.0 - (|offset| / (OVERLAP_RANGE + 1))
```

For `OVERLAP_RANGE = 4`:

| Channel Offset | Weight |
|---------------|--------|
| 0 (co-channel) | 1.00 |
| ±1 | 0.80 |
| ±2 | 0.60 |
| ±3 | 0.40 |
| ±4 | 0.20 |

This is a simplified model. The real 802.11 spectral mask doesn't decay linearly — it has defined rolloff points at ±11 MHz and ±22 MHz from center. But the linear approximation is reasonable for a scoring heuristic, and it avoids the complexity of modeling the actual mask shape on a microcontroller.

### Signal-Weighted Interference

Not all APs contribute equally to interference. A nearby AP with a strong received signal (say -45 dBm) will dominate the noise floor on overlapping channels. A distant AP at -85 dBm is barely detectable and contributes negligible interference.

The algorithm applies a simple RSSI-based multiplier:

| Avg RSSI Range | Factor | Interpretation |
|---------------|--------|---------------|
| Above -60 dBm | 1.5x | Strong nearby AP — high interference impact |
| -60 to -80 dBm | 1.0x | Moderate — standard weight |
| Below -80 dBm | 0.5x | Weak distant AP — minimal interference |

This is a coarse model — three tiers rather than a continuous function — but it captures the important distinction: a strong AP two channels away is a bigger problem than a weak AP on the same channel.

### Putting It Together

The complete scoring function in C++:

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

For each of the 14 channels, the algorithm sums across all channels within ±4 of the target, weighting each by spectral distance and signal strength. The result is normalized to [0, 1] by dividing by 10 — meaning 10 or more weighted neighbor-networks represents a fully congested channel.

### A Worked Example

Consider a scan that finds:

- 2 APs on channel 1, average RSSI -55 dBm (strong)
- 1 AP on channel 3, average RSSI -72 dBm (moderate)
- 3 APs on channel 6, average RSSI -64 dBm (moderate)

What's the congestion score for **channel 4**?

| Neighbor | Offset from 4 | Weight | RSSI Factor | Count | Contribution |
|----------|--------------|--------|-------------|-------|-------------|
| Ch 1 | -3 | 0.40 | 1.5 (strong) | 2 | 2 × 0.40 × 1.5 = 1.20 |
| Ch 3 | -1 | 0.80 | 1.0 (moderate) | 1 | 1 × 0.80 × 1.0 = 0.80 |
| Ch 6 | +2 | 0.60 | 1.0 (moderate) | 3 | 3 × 0.60 × 1.0 = 1.80 |

**Total score:** (1.20 + 0.80 + 1.80) / 10.0 = **0.38 (38% congestion)**

Channel 4 has no APs on it, yet it scores 38% congestion because of overlap from channels 1, 3, and 6. This is exactly the kind of hidden interference that a naive per-channel count would miss.

## Channel Recommendation

With congestion scores computed for all channels, the recommendation is straightforward: check only the three non-overlapping candidates and pick the lowest score.

```cpp
uint8_t ChannelAnalyzer::recommendChannel(
    const std::array<ChannelStats, NUM_24GHZ_CHANNELS>& stats) const {

    static constexpr uint8_t candidates[] = {1, 6, 11};

    uint8_t best = 1;
    float lowest = 2.0f;

    for (uint8_t ch : candidates) {
        float score = stats[ch - 1].congestion_score;
        if (score < lowest) {
            lowest = score;
            best = ch;
        }
    }

    return best;
}
```

Only channels 1, 6, and 11 are considered. There's no point recommending channel 3 or channel 9 — using them would create the adjacent-channel interference problems described above. The recommendation engine enforces the non-overlapping channel plan regardless of what the scores look like.

## How Enterprise Tools Compare

Commercial site survey tools (Ekahau, AirMagnet, iBwave) use the same fundamental analysis with additional sophistication:

- **Actual spectral mask modeling** rather than a linear weight approximation
- **Path loss models** that account for walls, floors, and distance rather than just RSSI thresholds
- **Temporal analysis** capturing interference patterns over time rather than a single snapshot
- **Multi-band coordination** across 2.4 GHz, 5 GHz, and 6 GHz simultaneously
- **AP placement optimization** using the interference model to suggest physical locations

The ESP32 analyzer captures the essential logic — overlap-aware, signal-weighted congestion scoring on the non-overlapping channel set — in a form factor you can carry in your pocket. It won't replace a full site survey, but it demonstrates the same core reasoning that drives enterprise channel planning.

## Takeaways

1. **Channel numbers are not frequencies.** In 2.4 GHz, 13 channel numbers map onto 83.5 MHz of spectrum — not enough for 13 independent channels. Only three fit without overlap.

2. **Adjacent-channel interference is worse than co-channel sharing.** Co-channel APs cooperate via CSMA/CA. Overlapping-channel APs corrupt each other's frames.

3. **Signal strength matters.** A strong nearby AP on an adjacent channel is a bigger problem than a weak one on the same channel. Any congestion model that ignores RSSI is incomplete.

4. **The "best" channel depends on the environment.** There's no universally correct answer — it depends on what your neighbors are doing. Scanning and scoring is the only way to make an informed decision.

The full firmware implementation is open source: [Vulfid/arduinoWiFiAnalyzer](https://github.com/Vulfid/arduinoWiFiAnalyzer). The companion post on the [project architecture and C++ implementation](/posts/wifi-channel-analyzer/) covers the code in more detail.
