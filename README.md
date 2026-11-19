# FITCAMX dashcam app (com.vidure.fitcamx) phones home: privacy report

This is an independent privacy review of the **FitCamX dashcam app** for Android (**FITCAMX**, package `com.vidure.fitcamx`, publisher "Viidure-pub" on Google Play, built on the Vidure/Viidure white-label dashcam client).
It finds that the app "phones home" to a vendor cloud hosted in China (**data sent to China**), and that it initialises third-party tracking-capable SDKs before the user has given consent.
Camera functions themselves stay on the camera's local Wi-Fi.
The items below are privacy findings; they are facts about what the code does, not claims about intent.

## What was analysed

| | |
| --- | --- |
| App | FITCAMX, `com.vidure.fitcamx`, version 1.0.67.260921 |
| Date | 2026-11-19 |
| Method | Static analysis of the APK distributed through Google Play (signature verified as the Play-distributed one). No traffic was captured and no vendor server was contacted. |

## Findings

### 1. Data sent to a China-hosted vendor cloud on every start
After the one-time policy dialog, with no login and no further consent, the app contacts `app-api.lufengzhe.com`.
That host resolves to Huawei Cloud in Shenzhen, China.
The app sends:

- a persistent app-instance ID that is stored on the phone and survives app restarts
- phone brand, Android version, app version, language and region
- for each saved camera (up to 10): model, brand, firmware version and the camera's MAC address
- an empty request that lets the server learn and geolocate the phone's public IP address

The server can also instruct the app to perform an HTTP request to a URL of the server's choosing and to report the response back.
This is described only as a capability present in the code.

Not found: phone GPS, videos, contacts or advertising ID sent to this server.

### 2. Verbose debug logs, uploaded with feedback
The Play build ships with debug logging on.
Logs include cloud request and response bodies, GPS lines parsed from clips, and camera commands and connection details.
They are written to the app's folder on shared storage and the whole folder is zipped and uploaded when the user submits the in-app feedback form.

### 3. Baidu Map / Location SDK initialised with consent defaulted to "agreed"
On every start the app initialises the Baidu Map and Location SDKs and sets Baidu's privacy consent to "agreed" by default, in all regions.
With location permission granted and a camera saved, a Baidu location request runs at start (Wi-Fi scan data, cell data, GPS), contacting Baidu servers.

### 4. Facebook SDK initialises before consent
The Facebook SDK starts automatically at process start, before the privacy consent, and contacts the Facebook Graph API (IP address, user agent, app token).
App-event logging is switched off.

### 5. Clips saved in shared DCIM storage
Downloaded clips are stored in `DCIM/<brand>/`.
Other apps with media access and photo-backup apps (for example cloud photo backup) can read them.
Dashcam clips carry embedded GPS and speed, so a backed-up clip is a location record.

### 6. Over-broad permissions
The app declares permissions a dashcam companion does not need, including microphone, camera, display over other apps, modify system settings, media location (GPS data of all photos), Bluetooth advertise, biometric and advertising-service permissions.
Some need manual enabling and are dormant unless granted.

## What owners can do

- Deny permissions you do not need (microphone, camera, overlay, system settings).
- Grant location only "while using the app".
- Set a strong, unique Wi-Fi password on the camera.
- Keep dashcam clips out of cloud photo backup (exclude the `DCIM/<brand>` folder).
- Avoid the in-app feedback form, which uploads the debug logs.

## Further findings

Additional security weaknesses were found and will be published after coordinated disclosure (90 days or a vendor fix, whichever comes first).

## Limitations

This is static analysis of a single app version; later versions may differ.
Server-side behaviour and native libraries were not examined, so what the server does with the data is not known.
Corrections from the vendor are welcome via an issue in this repository.

Last updated: 2026-11-19
