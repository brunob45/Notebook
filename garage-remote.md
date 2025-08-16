# Overview
I wanted to control my garage door using Home Assistant. I have a few ESP32 laying around, so I decided to flash Tasmota on one of them and try it out.

## Parts
My garage is equipded with a Mac-Lift Deluxe 2005.

There are 4 terminals:
1. Photo Eye Safety
2. Photo Eye Safety
3. Wall Control
4. Wall Control

To control it over Home Assistant, I assembled a [Tasmota relay](https://tasmota.github.io/docs/) using:
- [M5Stack Stamp-C3U](https://docs.m5stack.com/en/core/stamp_c3u)
- 2N2222 transistor
- 5V relay
- 1k resistor
- Cheap USB wall charger

## Setup
After flashing `Tasmota (english)` on my Stamp-C3U, I configured the pins as:
- GPIO2: WS2812
- GPIO6: Relay
- GPIO9: Button

I chose a meaningful name (menu `Other`):
- Device Name: Porte de Garage
- Friendly Name 1: Relais
- Friendly Name 2: Lumière

I configured the MQTT settings to communicate with Home Assistant
- Host: `homeassistant.local` (or Home Assistant IP address)

To activate the garage door, the circuit simply needs to short the terminals 3 & 4 together for a short period of time.
I decided to use a [Tasmota rule](https://tasmota.github.io/docs/Rules/], which I implemented as
```
ON Power1#State=1
DO Backlog
  Power2 1;
  RuleTimer1 1
ENDON
ON Power1#State=0
DO Backlog
  Power2 0;
  RuleTimer1 0
ENDON
ON Rules#Timer=1
DO Power1 0
ENDON
```

With this rule, when the relay is automatically turned off after 1 second.
