# XLinkPro — XOverdrive360

**ESP32-S3-powered Xbox 360 controller bridge, with a Windows firmware installer and browser-based control dashboard.**

XLinkPro is an experimental hardware/software project that uses an ESP32 ($5 micro computer) to unlock more usability out of your console. This allows your console to connect to modern bluetooth controllers, have a comand prompt, use cheats, xbox smart glass, nintendo switch as the xbox 360 equivalent to a Wii-U gamepad, plus a lot more. This project is still under development and more features will be added.

> **Read the hardware compatibility section before flashing.** Firmware intended for one ESP32-S3 configuration is not automatically safe for every ESP32-S3 board. This project is independently developed and is not affiliated with Microsoft, Xbox or Espressif.

## 1. Supported hardware — read first

| Hardware | Status | Notes |
| --- | --- | --- |
| **ESP32-S3 N16R8 development board with separate USB/OTG and UART connections** | **Tested working** | Reference configuration: 16 MB physical flash, 8 MB PSRAM, USB-to-UART programming interface (CH343 on the tested board). |
| Other **ESP32-S3** development boards | **Experimental / unverified** | May work **only if** the board's flash settings, pinout, USB setup, power supply and partition layout are compatible with the published firmware. Enabling the experimental switch does not make a board compatible. |
| ESP32-S3 boards with different flash sizes or partition layouts | **Not confirmed** | Do not flash until the firmware image and partition layout have been validated for that board. |
| Original ESP32, ESP32-S2, ESP32-C3, ESP32-C6, ESP32-C5 and other non-S3 variants | **Not supported by this firmware** | Different chip targets; do not attempt to flash the published ESP32-S3 image. |
| Arduino Leonardo / Uno / Uno R4 / ESP8266 / Raspberry Pi Pico | **Not supported by this firmware** | These are not substitutes for the tested ESP32-S3 configuration. |

**Reference build details:** Arduino IDE board target **ESP32S3 Dev Module**, with a **4 MB Arduino flash/partition configuration** used for the currently tested firmware, even though the reference board physically reports 16 MB flash. The flasher's current NVS backup/restore assumes an NVS partition at **0x9000** of length **0x5000**. Do not assume these settings are correct for another board or another firmware build. The reference board's 8 MB PSRAM is not required to be enabled for the tested build.

**Connections:** On the tested dual-USB board, use **UART** for programming with the Windows flasher, and **USB/OTG** for the Xbox 360 controller connection. A data-capable USB cable is required. Port labels and wiring differ across manufacturers.

### What you need

- A **compatible ESP32-S3 board** (the tested N16R8 reference board is recommended).
- A **Windows 10/11 PC** for XLinkPro Flasher.
- A **USB data cable** for the board's UART/programming port.
- Internet access for automatic firmware discovery/download.
- A **2.4 GHz Wi-Fi network** reachable by the ESP32-S3 (the ESP32-S3 does not support 5 GHz Wi-Fi).
- An Xbox 360 and the appropriate USB connection for the controller interface.
- The board's USB-to-UART driver if Windows does not create a COM port (the tested board uses a CH343 interface).

## 2. Install XLinkPro Flasher (Windows)

**You do not need Arduino IDE or Python to install published firmware using the packaged flasher EXE.**

1. Go to [XLinkPro-Firmware Releases](https://github.com/TavrosBaker/XLinkPro-Firmware/releases).
2. Download the latest **XLinkPro Flasher Windows `.exe`** release, not a source-code ZIP.
3. Place the EXE in a convenient folder and run it. Windows may warn about an unsigned, unfamiliar application; only proceed if you trust the source and have verified the download.
4. Connect the ESP32-S3 **UART/programming** USB port to your PC.
5. If no COM port appears, install the correct USB-to-UART driver for your particular board and reconnect it.

The flasher is a Windows firmware installer, **not** the Xbox controller dashboard itself. Its purpose is to put the XOverdrive360 firmware onto a compatible ESP32-S3, set the Wi-Fi credentials and report the dashboard address.

## 3. Flash and set up your ESP32-S3

1. **Connect** the board's UART/programming port to your PC with a data-capable cable.
2. **Open XLinkPro Flasher** and select the detected COM port. If multiple ports appear, choose the one belonging to your ESP32-S3. Use the refresh button if needed.
3. Under **Version to install**, leave **LATEST** selected for the newest published version, or choose an older listed version.
4. Enter your **Wi-Fi network name (SSID)** and **Wi-Fi password**. These are sent to the device for storage; never post credentials in bug reports or screenshots.
5. Leave **Experimental / unsupported ESP32-S3 boards** **OFF** if you are using the tested reference hardware. Do not turn it on simply to bypass a warning.
6. Click **FLASH XLINKPRO** and confirm the hardware warning. Keep the cable connected and do not close the application during flashing.
7. Wait for the log to report a successful flash, Wi-Fi setup and a dashboard IP address.
8. Open the displayed `http://<device-ip>` address on a phone or PC connected to the same local network, or use **OPEN XLINKPRO DASHBOARD**.
9. Connect the board's controller-facing **USB/OTG** port to the Xbox 360 as required by your tested hardware setup.

**Important:** A successful flash does not guarantee that every board, cable, console or USB configuration will work. Verify functionality on your actual hardware.

### If Wi-Fi fails after flashing

Check the SSID and password, ensure the network is **2.4 GHz**, and make sure the ESP32-S3 is within range. The flasher includes **Retry Wi-Fi · No Reflash** to send corrected credentials without reinstalling firmware when the device's provisioning protocol is responding. The device stores Wi-Fi settings in non-volatile memory so they can persist across reboots. Do not publish your local dashboard IP as a universal address; it varies by network.

## 4. What XOverdrive360 can do

The capabilities below describe the project's currently developed firmware and dashboard. Availability and exact controls can vary by firmware version.

- **Xbox 360 controller output:** send virtual controller button and analogue inputs through the ESP32-S3 bridge on the tested setup.
- **Full controller input coverage:** face buttons **A/B/X/Y**, D-pad, **Start**, **Back**, shoulder buttons, triggers, left/right analogue sticks and stick-click controls supported by the controller library.
- **Browser-based dashboard:** interact with the ESP32-S3 from a browser on the same Wi-Fi network; no separate mobile app is required.
- **Live controller control:** use the dashboard to send supported button and analogue commands to the connected Xbox 360.
- **Configurable input tools:** the project includes features such as rapid-fire, input mapping, macros/routines and other controller automation tools, depending on firmware version.
- **Profiles and settings:** firmware builds may provide configurable presets and dashboard settings.
- **Serial provisioning and diagnostics:** the device supports commands for firmware identification, board information and Wi-Fi connection status.
- **Controller-session ownership:** the tested dashboard uses a first-connected-controller ownership mechanism rather than allowing every browser to control simultaneously.

Use controller automation responsibly. Game rules, multiplayer services and platform terms may prohibit automated or modified inputs. The project does not guarantee compatibility with every game or online service.

## 5. How the Wi-Fi dashboard works

After setup, the ESP32-S3 joins your configured Wi-Fi network and serves a local browser dashboard. The flasher attempts to report the device's IP address after provisioning. On the same network, open that address in a modern browser.

**Security note:** Treat the dashboard as a **local-network control interface**. Do not forward its ports to the public internet or expose it through an unsecured tunnel. Use a trusted Wi-Fi network, and avoid allowing untrusted devices to access the controller interface. A browser page loading successfully is not evidence of strong authentication.

## 6. Firmware versions and automatic updates

XLinkPro Flasher RC2 discovers firmware directly from the repository's [`firmware/XOverdrive360/` folder](https://github.com/TavrosBaker/XLinkPro-Firmware/tree/main/firmware/XOverdrive360) on the `main` branch. It does **not** need a manually maintained firmware manifest or a separate GitHub Release for each firmware binary.

A published firmware filename must:

- End with **`.merged.bin`** (including names ending in `.ino.merged.bin`).
- Contain **exactly one** version number in the form **`MAJOR.MINOR.PATCH`**, such as `1.1.0` or `2.0.3`. An optional `v` or `V` prefix is fine.
- Contain a genuine **merged ESP32-S3 firmware binary**, not a placeholder or a plain text file.

**Valid examples:**

```text
V1.1.0_XOverdrive360.ino.merged.bin
XOverdrive360_V1.2.0.ino.merged.bin
XLinkPro-2.0.0.merged.bin
```

The flasher sorts versions numerically and defaults to **LATEST**. It rejects ambiguous or duplicate versions and very small firmware files, and checks downloaded content against the GitHub-listed Git blob hash before flashing. This verifies consistency with the repository listing; it is **not** a cryptographic signature proving who published the firmware. Only download from a repository you trust.

**For users:** choose LATEST for normal installation, or select an earlier version from the dropdown if needed. Reflashing always carries risk; keep backups of anything important.

**For developers/maintainers:** compile in Arduino IDE using the correct ESP32-S3 settings, choose **Sketch → Export Compiled Binary**, locate the generated **`.ino.merged.bin`**, rename a copy with a unique version and commit it to `firmware/XOverdrive360/` on `main`. The flasher will discover it on refresh or restart. **A new flasher EXE is not needed for ordinary firmware releases.** Avoid overwriting an existing version's binary; publish a new version instead.

## 7. Advanced and experimental options

**Local firmware:** The flasher provides **Advanced → use local `.merged.bin`** for development or offline testing. Use only binaries built for the exact board and partition layout you are flashing.

**Experimental boards:** The experimental switch is an acknowledgement of additional risk, **not a compatibility converter**. The current flasher uses the tested Arduino NVS region at `0x9000` (size `0x5000`) for backup and restore. An incompatible layout can overwrite settings or cause boot failures. Do not test unknown boards unless you can verify the image/layout and recover them with the manufacturer's bootloader procedure.

## 8. Troubleshooting

| Problem | What to check |
| --- | --- |
| **No COM port detected** | Use the UART/programming port and a data-capable cable; check Windows Device Manager; install the correct USB-to-UART driver; close other programs using the port. |
| **Loaded 0 official firmware releases** | Check internet access, GitHub availability and the firmware folder. The `.merged.bin` must contain one version number and be a real binary, not a tiny placeholder. Refresh the firmware list. |
| **Firmware download rejected** | Retry with a stable connection. The flasher checks file size and GitHub blob integrity; do not bypass a mismatch. |
| **Flashing fails** | Verify board type and COM port, cable stability, permissions and whether another serial monitor is open. Some boards require manual BOOT/RESET entry into download mode. |
| **Wi-Fi won't connect** | Verify 2.4 GHz Wi-Fi, SSID/password, signal and router isolation; try **Retry Wi-Fi · No Reflash**. |
| **Dashboard won't load** | Confirm the ESP32-S3 is connected to Wi-Fi and use its current IP; ensure your browser is on the same network and client isolation/firewalls aren't blocking it. |
| **Xbox 360 doesn't respond** | Check that you used the controller-facing USB/OTG port, the proper cable, power and a firmware/hardware combination already tested with your console. |
| **Windows warns about the EXE** | Download only from the official GitHub release. Unsigned software may trigger SmartScreen; a warning alone does not prove a file is malicious or safe. |

If you report a bug, include the **flasher version**, **firmware version**, **exact ESP32-S3 board**, **Windows version**, and the **installation log** with passwords, IPs or other sensitive details removed.

## 9. Project links

- **Repository:** https://github.com/TavrosBaker/XLinkPro-Firmware
- **Windows downloads:** https://github.com/TavrosBaker/XLinkPro-Firmware/releases
- **Firmware files:** https://github.com/TavrosBaker/XLinkPro-Firmware/tree/main/firmware/XOverdrive360
- **Issues / bug reports:** https://github.com/TavrosBaker/XLinkPro-Firmware/issues

## 10. Safety, support and disclaimer

This is a community/experimental project, provided without a guarantee of hardware compatibility or fitness for a particular purpose. Flashing incorrect firmware can render a board temporarily unusable and may require recovery. Always confirm your hardware and maintain a working backup. Use a trusted network and do not expose the controller dashboard publicly.

**XLinkPro and XOverdrive360 are independent projects, not official Xbox or Microsoft products.** Xbox and Xbox 360 are trademarks of their respective owners.

For current support, compatibility findings, feature requests or bug reports, use the repository's [Issues page](https://github.com/TavrosBaker/XLinkPro-Firmware/issues).
