# YourLight — Firmware Updates

This repository hosts the published firmware for **YourLight**, an ESP32-S3
WS2812B LED controller. The source code is kept in a separate private
repository; only the compiled release files live here so the device can
download updates over the air.

Each release attaches three files:

| File | Purpose |
|------|---------|
| `firmware.bin` | The program image (the controller's code) |
| `spiffs.bin` | The web UI / file image (pages, scripts, images) |
| `version.json` | Update manifest the device reads to detect new versions |

## Configuration password

Changing settings or installing updates requires the configuration password:

```
8700
```

> Note: this password is a deterrent, not strong security — it is plaintext
> over HTTP on your local network and is recoverable from `firmware.bin`.

## How to update over the air (recommended)

1. Open the controller in a browser (`http://YourLight.local`, or whatever name
   you set) and go to **Manage WiFi & Device Name → Online updates**.
2. In **Update manifest URL**, paste:
   ```
   https://github.com/torjrg/YourLight-updates/releases/latest/download/version.json
   ```
   Enter the password `8700` in the **Configuration password** field, then **Save**.
3. Click **Check for updates**. If a newer version is available, the
   **Install firmware** and **Install web UI** buttons appear.
4. Click **Install firmware** (and/or **Install web UI**). The device downloads
   the image, flashes it, and reboots. Reconnect after ~15 seconds.

Do a **Web UI** update on a stable connection — if it is interrupted the page
files can be left incomplete (the device still runs and is recoverable; the
program firmware is never at risk).

## How to update manually

If you'd rather not use the online pull:

1. Download `firmware.bin` and/or `spiffs.bin` from the
   [latest release](../../releases/latest).
2. On the controller's setup page, open **Software update**.
3. Choose **Firmware** or **Web UI / files**, pick the matching `.bin`, enter
   the password, and click **Upload & install**.

## Safety notes

- **Firmware updates are rollback-safe.** The image is written to the inactive
  flash slot; if the upload is interrupted, the device keeps running the
  previous version. A bad upload cannot brick the board.
- **Web UI (SPIFFS) updates write in place.** An interrupted upload can leave
  the web pages incomplete, but the firmware and its API keep working — just
  re-run the update (or reflash over USB).
- The board can always be recovered over USB with PlatformIO
  (`pio run -t upload` / `-t uploadfs`), regardless of any failed OTA.

## Versioning

Releases are tagged `vMAJOR.MINOR.PATCH`. The device compares its installed
version against `version.json` and only offers an update when the published
version is newer.
