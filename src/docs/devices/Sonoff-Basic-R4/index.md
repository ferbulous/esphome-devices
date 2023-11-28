---
title: Sonoff BASIC R4 V1.0
date-published: 2023-11-28
type: relay
standard: global
board: esp32c3
---

The sonoff basic r4 now includes ESP32C3 and a new 'magic switch mode' that can be enabled using custom component by @ssieb

Pinout and pics taken from https://digiblur.com/wiki/devices/relays/sonoff-basic-r4/

## GPIO Pinout

| Pin    | Function                           |
| ------ | ---------------------------------- |
| GPIO4  | Relay  |
| GPIO6 | LED     |
| GPIO9 | Button                              |
| GPIO5  | Pin for 'magic switch mode'    |

## PCB

![alt text](/Sonoff-BASIC-R2-v1.4_pcb.jpg "Sonoff BASIC R2 v1.4 PCB")

![alt text](/Sonoff-BASIC-R2-v1.4_pcb_rear.jpg "Sonoff BASIC R2 v1.4 PCB rear")

## Basic Configuration

```yaml
esphome:
  name: basic-r4
  friendly_name: basic-r4
  platformio_options:
    board_build.mcu: esp32c3
    board_build.variant: esp32c3  

esp32:
  variant: ESP32C3
  board: esp32dev
  framework:
    type: esp-idf
    sdkconfig_options:
      CONFIG_BT_BLE_50_FEATURES_SUPPORTED: y
      CONFIG_BT_BLE_42_FEATURES_SUPPORTED: y
      CONFIG_ESP_TASK_WDT_TIMEOUT_S: "10"    
# Would not boot with the stock ESP32-C3 ESPHome 2023.9 default YAML config until I added/change the settings above for ESP32C3

logger:
captive_portal:
api:
ota:

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
  ap:

esp32_ble_tracker:
  scan_parameters:
    active: true

bluetooth_proxy:
  active: true

# for magic switch mode
external_components:
  - source: github://ssieb/custom_components@sswitch
    components: [ magic_switch ]
    refresh: 1min

magic_switch:
  pin: 5
  on_switch:
    - switch.toggle: relay_1

binary_sensor:
  - platform: gpio
    pin:
      number: GPIO9
      mode:
        input: true
        pullup: true
      inverted: true
    id: button_1
    on_press:
      then:
        - switch.toggle: relay_1

switch:
  - platform: gpio
    name: Switch
    pin: GPIO4
    id: relay_1

status_led:
  pin:
    number: GPIO6
    inverted: yes  

```
