![Picture of speakers](https://assets2.razerzone.com/images/campaigns/nommo-pro/nommo-pro-campaign-og.jpg)

# About

Razer Nommo Pro are decent speakers and come with a phone app to control them in
pretty much every aspect. Unfortunately if you're using them as a TV speaker and
the control pod is far away, it's quite inconvenient to have to get up and turn
them on, or find your phone and launch the app.

No more! With some work and effort I was able to sniff and decrypt (well... technically reverse-engineer) the Bluetooth
protocol used by Razer which allows for pretty much full control of the device.
With the power of ESP32 (sorry ESP8266 lovers, as mentioned the speakers use BT), ESPHome
and Home Assistant, you can now control your Razer Nommo Pro speakers via WiFi and even integrate them with
other things for automatic power-on together with a TV, changing EQ depending on whether Plex is playing, etc, maybe making sure volume after 10pm isn't set to over 50% etc.

Supported features:
- Volume
- Bass
- EQ preset selection
- Source selection
- Mute toggle
- Power control
- Full EQ control: all bands adjustment with ~ 0.18dB precision _(Original app only allows for 1dB precision)_
- Ability to toggle automatic power-off after 20 minutes of no audio

![HASS overview](/hass_device.png)

# Installation

Download the project [yaml config file](https://github.com/d-rez/esphome-razer-nommo-pro-speaker-control/blob/main/esphome-razer-nommo-pro-speaker-controller.yaml) from the repository. Edit required values, compile and flash as usual.

## Important
- Make sure to add speaker's MAC address in the substitutions section.
- Also add [HASS API](https://esphome.io/components/api.html) and [ESPHome OTA](https://esphome.io/components/ota.html) passwords for security as well as [add proper WiFi credentials](https://esphome.io/components/wifi.html)

# Adding as a Media Player to Home Assistant

You can link together certain controls for a more unified look/feel of the controller
Once you have connected with HASS via the API, add contents of the following file to your `configuration.yaml`.
Don't forget to replace `rz_spk_ctl` device_name if you changed it in the esphome yaml.

[Preview HASS Media Player yaml here](https://github.com/d-rez/esphome-razer-nommo-pro-speaker-control/blob/main/hass_media_player_configuration_snippet.yaml)

Note that certain features (like bass level, connection status, EQ bands and some more) aren't able to be exposed to media_player component, but can be added separately in the Lovelace card group/stack, however this should add a nice logic to those entities.

# Making it even nicer

![Lovelace overview](/lovelace_overview.png)
![Lovelace EQ Selection](/lovelace_eq_selection.png)
![Lovelace Source Selection](/lovelace_source_selection.png)
![New in v1.1: Equalizer display](/lovelace_eq_bands.png)

To make it look like above, you'll need a few more things:

1. Install the [mini-media-player](https://github.com/kalkih/mini-media-player) hass custom component
2. Install [bar-card](https://github.com/custom-cards/bar-card/) hass custom component
2. Create a new Lovelace card with the following (or similar) code, adjust as needed

[Preview Lovelace yaml here](https://github.com/d-rez/esphome-razer-nommo-pro-speaker-control/blob/main/lovelace_card.yaml)

# Want more?

With Home Assistant you can continue creating different automations, volume changes,
power sync with other devices and more using HASS Automations or Node Red.

# It's all cool and nice but I'm only here for Razer's BLE protocol deets

I haven't decided if I want to just paste the details here or link them to a separate file.
For the time being you can simply access the project's [yaml config file](https://github.com/d-rez/esphome-razer-nommo-pro-speaker-control/blob/main/esphome-razer-nommo-pro-speaker-controller.yaml), and scroll down a page or two.
I tried to keep it as informative (for both you and the future me) but it might not be explained in the best way. In this case - oh well, bad luck.

# Notes

The speaker can only connect to one device at a time, so if you want to use your
phone app you'll have to make sure to disconnect using the `_nommo_enable_control`
switch.

# Future plans (v1.2 and beyond)
- Think of a way to allow users to flash their ESP directly via this website instead of having to compile themselves. Possible issues with this approach:
  - entity_ids in hass might duplicate - possibly not an issue?
  - need a way for user to enter speaker MAC address after flashing (?) since the image is compiled at commit push time. Global variables? Will see.
