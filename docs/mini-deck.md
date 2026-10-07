# Side project: the Mini deck

An old phone and a phone-sized keyboard, turned into a pocket terminal for the homelab. Built for fun out of things that were already in a drawer.

## What it is

- **Phone:** a Samsung Galaxy A03s, the oldest spare phone in the house. Factory reset, no accounts on it, stock Android left as it is.
- **Keyboard:** a Rii i4 mini Bluetooth keyboard with a touchpad, almost exactly the same size as the phone.
- The phone is locked to landscape and set to dark mode, so the pair reads as one small computer.

## What is on it

Everything comes from F-Droid, so the phone never needs an account:

- **Termux** for a real Linux terminal
- **Jellyfin** for the media server
- **WiFiAnalyzer** and **Port Authority** as a small network toolkit
- **NewTube** for video
- A home screen shortcut to the homelab dashboard

No offline maps or offline encyclopedia on this one. Another device in the house already does that job, and this one has a different purpose.

## What it is for

- A pocket terminal and command line practice pad
- Checking the homelab from the sofa: the dashboard, a quick port scan, which Wi-Fi channel is crowded
- Media in bed

## Two things worth knowing

- **The keyboard would not show up in Bluetooth at first.** It ships in 2.4G dongle mode (green light). It only appears on the phone after switching it to Bluetooth mode (blue light).
- **The keyboard layout is set on the phone, not on the keyboard.** Adding the matching language under the on-screen keyboard's input languages made the symbols land where the keycaps say.

## Planned

- **LoRa radio.** A Seeed XIAO ESP32S3 with a Wio-SX1262 board running Meshtastic, paired to the phone over Bluetooth so the phone becomes the radio's screen and keyboard. Text messages with no mobile network. The kit is ordered and has not arrived yet.
- **A custom clip** that joins the phone and the keyboard into one unit. It has to be hinged, come apart quickly, leave both USB-C ports free, and stay clear of the keyboard's top buttons, battery cover and dongle slot. Nothing ready-made fits, so it will be made by hand or printed. Not designed yet.
- **SSH into the homelab from Termux.** Not tried yet.
