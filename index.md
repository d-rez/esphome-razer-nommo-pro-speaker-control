![Picture of speakers](https://assets2.razerzone.com/images/campaigns/nommo-pro/nommo-pro-campaign-og.jpg)

# About

Razer Nommo Pro are decent speakers and come with a phone app to control them in
pretty much every aspect. Unfortunately if you're using them as a TV speaker and
the control pod is far away, it's quite inconvenient to have to get up and turn
them on, or find your phone and launch the app.

No more! With some work and effort I was able to sniff and decrypt the Bluetooth
protocol used by Razer which allows for pretty much full control of the device.
With the power of ESP32 (sorry ESP8266 lovers, as mentioned the speakers use BT), ESPHome
and Home Assistant, you can now control speakers via WiFi and even integrate them with
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
Once you have connected with HASS via the API, add contents of the following file to your `configuration.yaml`.
Don't forget to replace `rz_spk_ctl` if you changed it.

[Preview HASS Media Player yaml here](https://github.com/d-rez/esphome-razer-nommo-pro-speaker-control/blob/main/hass_media_player_configuration_snippet.yaml)

# Making it even nicer

![Lovelace overview](/lovelace_overview.png)
![Lovelace EQ Selection](/lovelace_eq_selection.png)
![Lovelace Source Selection](/lovelace_source_selection.png)

To make it look like below, you'll need two more things:

1. Download and install the [mini-media-player](https://github.com/kalkih/mini-media-player) custom component by kalkih in your HASS instance
2. Create a new Lovelace card with the following (or similar) code:

[Preview Lovelace yaml here](https://github.com/d-rez/esphome-razer-nommo-pro-speaker-control/blob/main/lovelace_card.yaml)

# Want more?

With Home Assistant you can continue creating different automations, volume changes,
power sync with other devices and more using HASS Automations or Node Red.

# Notes

The speaker can only connect to one device at a time, so if you want to use your
phone app you'll have to make sure to disconnect using the `_nommo_enable_control`
switch.
