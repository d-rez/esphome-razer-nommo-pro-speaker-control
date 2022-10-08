```
=========================================================
Razer's BLE protocol reverse engineering
Done in 2022 by dark_skeleton
No dogs have been harmed while sniffing and analysing Bluetooth packets
=========================================================
Service                     5052494d-2dab-0341-6972-6f6861424c45
Notification characteristic 43484152-2dab-3141-6972-6f6861424c45
Write characteristic        43484152-2dab-3241-6972-6f6861424c45

Example notification packets dissected:

04 ff 08 c0 49 04 01 00 00 01 01
^^ ^^       ^^                                    04, ff and 49 bytes never change
      ^^                                          response length in bytes
         ^^                                       indicates type of response. See list further down, there's generally only two we care about (0xCC and 0xC0)
               ^^                                 selected source (details below)
                  ^^                              mute state (inverted)
                     ^^ ^^                        equalizer presets (details below)
                           ^^                     automatic 20min power off state
                              ^^                  power state

04 ff 0d cc 49 0c 19 00 38 32 2c 27 2c 32 38 3d
^^ ^^ ^^ ^^ ^^                                    here we have a longer packet but same logic applies as above to these bytes
               ^^                                 volume level (0..100)
                  ^^                              bass level (0..100)
                     ^^                           ?? zeroes?
                        ^^ ^^ ^^ ^^ ^^ ^^ ^^ ^^   EQ bands (0x0..0x64), middle  being 0x32, each step being between... 0x06 and 0x05? Differs. Weird. I.e. 0x32, 0x2c, 0x27, 0x21?
                                                           9 steps in app above and below 0dB, so 19 steps total, 5.26 per step? rounded up?

Example WRITE packets dissected:

01 00 fc 02 d5 48
^^ ^^ ^^                                          static for all write requestse
         ^^                                       number of bytes that follow - 0x02 if it's only a broadcast request and we're not changing settings. 0x03 if we are.
            ^^                                    type of the request (see below)
               ^^                                 always 0x48 here, probably indicates end of type request

01 00 fc 03 c6 48 00
^^ ^^ ^^ ^^    ^^                                 same as above
            ^^                                    write operation type/id. See below.
                  ^^                              value of the parameter to write

These have been gathered by probing the speaker, I couldn't decrypt all but they're probably not needed anyway

Format below:
request bytes - description
  response => description

01 00 fc 02 ce 48 - firmware version request
  04 ff 11 ce 49 00 00 0c 0a 00 00 00 31 01 03 00 00 00 00 00 => Translates to FW C0A.31.1030000
01 00 fc 02 cf 48 - ??? requested when mobile app starts up
  04 ff 11 cf 49 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 => seems to always return zeroes? not important
01 00 fc 02 d0 48 - ???
  04 ff 04 d0 49 00 00 => zeroes again?
01 00 fc 02 d4 48 - ???
  04 ff 08 d4 49 7c 96 d2 5a 8e dc => ???
01 00 fc 02 d5 48 - ???
  04ff0ad54910000000000000ff => ???
01 00 fc 02 c0 48 -- request power, mute, eq, dolby, source etc. broadcast
  04 ff 08 c0 49 04 01 00 01 01 01 => already described above and below
01 00 fc 02 cc 48 - volume, bass, eq bands
  04ff0dcc49071700423732323232373d => already described above and below

argument write operation types:
once a write request is sent, speaker will reply with a notification and updated states of given type
  0xc6:
    type: power
    values: 0x01 (on), 0x00 (off)
    reply: 0xc0
  0xc1:
    type: mute
    values: 0x01 (mute off), 0x00 (mute on)
    reply: 0xc0
  0xc8:
    type: volume
    values: 0..100 (0x00 .. 0x64)
    reply: 0xcc
  0xc7:
    type: bass volume
    values: 0..100 (0x00 .. 0x64)
    reply: 0xcc
  0xc4:
    type: EQ
    values: 03: Movie, 02: Music, 01: Game, 00: THX (dolby must be disabled), 0A: Custom EQ (dolby must be disabled).
                                       //  Invalid values seem to work but just crash the phone app when it refreshes
    reply: 0xc0
  0xc2:
    type: Dolby
    values: 0,1 (should be followed up with a 0xc4 write to make sure we're not using invalid values)
    reply: 0xc0
  0xc5:
    type: Source
    values: 01: optical, 03: bt, 02: analog, 04: usb
    reply: 0xc0, 0xcc
  0xc3:
    type: auto power-off toggle (after 20 minutes)
    values: 1/0 (on/off)
    reply: 0xc0
  0xd3:
    type: equalizer bands (8x 1byte)
    values: 0..100 (0x0..0x64), 0x32 (50) is 0dB, range seems to be -9dB..+9dB
    known steps: 0, 0x5, 0xB, 0x10, 0x16, 0x1B, 0x21, 0x26, 0x2C, 0x32, 0x37, 0x3D, 0x42, 0x48, 0x4D, 0x53, 0x58, 0x5E, 0x64
    steps set by phone app are +/- by 0x01 which means these can be set with a precision of 1
    reply: 0xcc
```
