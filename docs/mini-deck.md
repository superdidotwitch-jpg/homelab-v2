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
- **Meshtastic** for the LoRa radio (see below)
- A home screen shortcut to the homelab dashboard

No offline maps or offline encyclopedia on this one. Another device in the house already does that job, and this one has a different purpose.

## What it is for

- A pocket terminal and command line practice pad
- Checking the homelab from the sofa: the dashboard, a quick port scan, which Wi-Fi channel is crowded
- Media in bed
- Logging in to the homelab's servers over SSH, straight from Termux (installed with `pkg install openssh`)

## Three things worth knowing

- **The keyboard would not show up in Bluetooth at first.** It ships in 2.4G dongle mode (green light). It only appears on the phone after switching it to Bluetooth mode (blue light).
- **The keyboard layout is set on the phone, not on the keyboard.** Adding the matching language under the on-screen keyboard's input languages made the symbols land where the keycaps say.
- **The @ key would not type in Termux**, which gets in the way of `ssh user@host`. The same command without it: `ssh -l user host`.

## The LoRa radio

The Mini deck has its own radio now: a Seeed XIAO ESP32S3 with a Wio-SX1262 board, running Meshtastic. It pairs to the phone over Bluetooth, so the phone is the radio's screen and keyboard. Text messages with no mobile network and no internet.

- **Node name:** Minideck, short name MND
- **Region:** EU_868
- **Role:** Client, the right one for a radio that gets carried around
- **App:** Meshtastic from F-Droid, like everything else on the phone. No account needed.

### Putting it together

The kit is two small boards and two antennas. The order matters:

1. The small 2.4 GHz antenna snaps onto the XIAO first. Its connector is covered once the boards are joined.
2. The radio board presses onto the XIAO by the small board-to-board connector, and by nothing else. The XIAO's pin headers stay empty. They are only there for breadboard use.
3. The LoRa antenna snaps onto the radio board.
4. Only then does it get power. A LoRa radio should never be powered with no antenna attached.

### The one real problem: old firmware

The kit is sold as pre-flashed, and it is. But it shipped with Meshtastic 2.5.2, and the current phone app could not finish loading the radio's settings from firmware that old. The app found the radio, accepted the pairing code, then sat in a loop of connecting and reconnecting.

The fix was a firmware update from a laptop, all in the browser:

1. `client.meshtastic.org` over a USB cable showed the firmware version, which is how the cause was found.
2. `flasher.meshtastic.org`, device "Seeed XIAO S3", newest release in the stable group, full erase and install.
3. Unplug and replug once the flash finishes. The board does not always restart by itself.
4. Back in `client.meshtastic.org`: Settings, LoRa, set the region, save. The radio does not transmit until a region is set.
5. On the phone, remove the old Bluetooth pairing and pair again. The update gives the board a fresh identity.

Things that went wrong on the way, and what they meant:

- **"The port is already open"**: another browser tab still had the board, or the flash button got a second click.
- **"Failed to set control signals"**: the flasher restarted the board into install mode, the board dropped off USB for a moment, and the flasher lost it. Trying again got through. The fallback is holding the XIAO's B button while plugging it in, which on this kit means lifting the radio board off first, because it covers the button.
- **A USB-C to USB-C cable from the phone showed no device.** Not every cable carries data.
- **The stable firmware group is labelled "Beta" on the flasher.** That is the stable line. Alpha and nightly are the ones to leave alone.
- **After the update the light is solid green and no longer blinks.** That is normal on the new firmware.

### How it travels

The Mini deck is four parts that all come apart: power bank, keyboard, phone and radio. The radio has no battery of its own. It runs from the power bank over USB-C, and the power bank was tested first, because many of them switch off when the draw is this small.

The radio lives in a small clear case that opens, with two openings: the USB-C port and the antenna.

### Still to do

- A better antenna. The small one in the kit is meant for testing. A short 868 MHz SMA antenna on a U.FL to SMA pigtail is ordered, with the pigtail's threaded end going through the case wall. One thing to check when buying: plain SMA, not RP-SMA. They look the same and do not fit each other.
- The two openings in the case.

## Planned

- **A custom clip** that joins the phone and the keyboard into one unit. It has to be hinged, come apart quickly, leave both USB-C ports free, and stay clear of the keyboard's top buttons, battery cover and dongle slot. Nothing ready-made fits, so it will be made by hand or printed. Not designed yet.
