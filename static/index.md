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
- (v1.1) Full EQ control: all bands adjustment with ~ 0.18dB precision _(Original app only allows for 1dB precision)_
- (v1.1) Ability to toggle automatic power-off after 20 minutes of no audio
- (v1.2) Ability to control volume using the TV remote (currently uses LG remote infrared codes) easily via an infrared receiver on pin GPIO18

![HASS overview](/hass_device.png)

# Installation

Download the project [yaml config file](https://github.com/d-rez/esphome-razer-nommo-pro-speaker-control/blob/main/esphome-razer-nommo-pro-speaker-controller.yaml) from the repository. Edit required (`#CHANGE ME`) values, compile and flash as usual. Best to flash via serial connection.

## Important
- Make sure to add speaker's MAC address in the substitutions section.
- Also add [HASS API](https://esphome.io/components/api.html) and [ESPHome OTA](https://esphome.io/components/ota.html) passwords for security as well as [add proper WiFi credentials](https://esphome.io/components/wifi.html)
- Adjust `remote_receiver` configuration as needed

# Adding as a Media Player to Home Assistant

You can link together certain controls for a more unified look/feel of the controller. Adding the speaker like that will also let you use them with the [Google Assistant integration](https://www.home-assistant.io/integrations/google_assistant/#available-domains) to control certain features of your speaker, specifically on/off and volume. Source selection is supposed to be supported as well, but I wasn't able to get it to work myself.

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

[You can access  my findings regarding the protocol used here](https://github.com/d-rez/esphome-razer-nommo-pro-speaker-control/blob/main/razer-ble-protocol-reverse-engineering.md).
I tried to keep it as informative (for both you and the future me) but it might not be explained in the best way. In this case - oh well, bad luck.

# Notes

The speaker can only connect to one device at a time, so if you want to use your
phone app you'll have to make sure to disconnect using the `_nommo_enable_control`
switch.
