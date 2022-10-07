![Picture of speakers](https://assets2.razerzone.com/images/campaigns/nommo-pro/nommo-pro-campaign-og.jpg)

# About

Razer Nommo Pro are decent speakers and come with a phone app to control them in
pretty much every aspect. Unfortunately if you're using them as a TV speaker and
the control pod is far away, it's quite inconvenient to have to get up and turn
them on, or find your phone and launch the app.

No more! With some work and effort I was able to sniff and decrypt the Bluetooth
protocol used by Razer which allows for pretty much full control of the device.
With the power of ESP32 (sorry ESP8266 lovers, as mentioned the speakers use BT)
Home Assistant, you can now control speakers via WiFi and even integrate them with
things like automatic power-on together with a TV and so on.

Supported features:
- Volume
- Bass
- EQ preset selection
- Source selection
- Mute toggle
- Power control

![HASS overview](/hass_device.png)

# Installation

Download the project [yaml config file](https://github.com/d-rez/esphome-razer-nommo-pro-speaker-control/blob/main/esphome-razer-nommo-pro-speaker-controller.yaml) from the repository. Edit required values, compile and flash as usual.

## Important
- Make sure to add speaker's MAC address in the substitutions section.
- Also add [HASS API](https://esphome.io/components/api.html) and [ESPHome OTA](https://esphome.io/components/ota.html) passwords for security as well as [add proper WiFi credentials](https://esphome.io/components/wifi.html)

# Adding as a Media Player to Home Assistant

You can link together certain controls for a more unified look of the controller
Once you have connected with HASS via the API, add the following to your `configuration.yaml`.
Don't forget to replace `rz_spk_ctl` everywhere if you changed it

```
media_player:
  - platform: universal
    name: Razer Nommo Pro Speakers
    commands:
      turn_on:
        service: switch.turn_on
        target:
          entity_id: switch.rz_spk_ctl_nommo_power
      turn_off:
        service: switch.turn_off
        target:
          entity_id: switch.rz_spk_ctl_nommo_power
      select_source:
        service: select.select_option
        target:
          entity_id: select.rz_spk_ctl_nommo_source
        data:
          option: "\{\{ source \}\}"
      volume_set:
        service: number.set_value
        target:
          entity_id: number.rz_spk_ctl_nommo_volume
        data:
          value: "\{\{ volume_level \}\}"
      volume_mute:
        service: switch.toggle
        target:
          entity_id: switch.rz_spk_ctl_nommo_mute
      select_sound_mode:
        service: select.select_option
        target:
          entity_id: select.rz_spk_ctl_nommo_eq
        data:
          option: "\{\{ sound_mode \}\}"
    attributes:
      is_volume_muted: switch.rz_spk_ctl_nommo_mute
      volume_level: number.rz_spk_ctl_nommo_volume
      source_list: select.rz_spk_ctl_nommo_source|options
      source: select.rz_spk_ctl_nommo_source
      state: switch.rz_spk_ctl_nommo_power
      sound_mode: select.rz_spk_ctl_nommo_eq
      sound_mode_list: select.rz_spk_ctl_nommo_eq|options
    device_class: speaker
    unique_id: nommo_speaker_controller
```

# Making it even nicer

![Lovelace overview](/lovelace_overview.png)
![Lovelace EQ Selection](/lovelace_eq_selection)
![Lovelace Source Selection](/lovelace_source_selection.png)

To make it look like below, you'll need two more things:

1. Download and install https://github.com/kalkih/mini-media-player custom component in your HASS
2. Create a new Lovelace card with the following (or similar) code:

```
type: vertical-stack
title: Razer Nommo Pro
cards:
  - type: custom:mini-media-player
    entity: media_player.razer_nommo_pro_speakers
    source: full
    artwork: none
    group: false
    name: Speakers
    volume_stateless: false
    hide:
      play_pause: true
      volume_level: false
      power_state: false
      sound_mode: false
    sound_mode: full
    info: short
    icon: mdi:speaker
  - type: conditional
    conditions:
      - entity: media_player.razer_nommo_speakers
        state_not: 'off'
    card:
      type: entities
      entities:
        - entity: number.rz_spk_ctl_nommo_bass_volume
          name: Bass Level
          secondary_info: none
      show_header_toggle: false
      state_color: false
```

# Want more?

With Home Assistant you can continue creating different automations, volume changes,
power sync with other devices and more using HASS Automations or Node Red.

# Notes

The speaker can only connect to one device at a time, so if you want to use your
phone app you'll have to make sure to disconnect using the `_nommo_enable_control`
switch.
