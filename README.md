# VEXION × VERA — Android companion app (source project)

VEXION (**V**ision-**E**nhanced e**X**ploration and **I**ntelligent **O**rientation **N**etwork) is a *proposed* assistive wearable for blind and
visually impaired users. This app is its companion, powered by **VERA** (Voice-Enabled Responsive Assistant). Package `com.vexion.app`, minSdk 26, target/compile 34.

> **Status honesty:** the physical wearable and its ESP32-S3 firmware do not exist. The app runs in **Simulated mode** (all data labelled simulated). The
> BLE client implements a *proposed* protocol and has **not** been tested against real hardware. This source has **not been compiled** by its author's
> environment — see `VERIFICATION_REPORT.md`.

## Build the APK
**Android Studio:** open this folder, let Gradle sync (it creates the wrapper), then *Build ▸ Build APK(s)* or `./gradlew assembleDebug`
→ `app/build/outputs/apk/debug/app-debug.apk`. Tests: `./gradlew testDebugUnitTest`.
Requires JDK 17 and Android SDK 34.

**GitHub Actions:** push this folder to a GitHub repo. `.github/workflows/build-apk.yml` runs unit tests, builds `app-debug.apk`, and uploads it as the
`app-debug-apk` workflow artifact (Actions tab ▸ run ▸ Artifacts). The workflow installs Gradle 8.7 itself, so no wrapper JAR is needed.

**Install:** enable *Install unknown apps*, copy the APK to the phone, open it; or `adb install app-debug.apk`.

## Using it
* **Talk to VERA** opens the mic only while listening (push-to-talk). There is **no always-on "Hey Vera" wake word**. Typing works for everything (Ask tab).
* Try: "Check everything and fix whatever is wrong", "Test the right motor", "Set intensity to 80 percent", "Explain the circuit", "हिंदी में समझाओ".
* **Simulator tab:** choose `ALL_HEALTHY`, `RECOVERABLE_BLE_DROP`, `RECOVERABLE_FRONT_SENSOR_GLITCH`, or `UNRECOVERABLE_RIGHT_MOTOR_FAULT`, then ask VERA to
  check everything. Watch the **Trace** tab for Understand → Plan → Execute → Verify → Recover → Report steps.
* In simulator mode the phone vibrates as a *phone haptic demonstration* (left: short double, right: long, both: rapid triple). It is not proof a motor works,
  and the simulator never marks a motor `VERIFIED_PHYSICAL`.

## Hardware integration (planned)
Firmware must expose service `00005658-0000-1000-8000-00805f9b34fb` with characteristics `…5659` (command, write), `…565a` (ACK, notify), `…565b`
(telemetry, notify). Frames: `0x56 | ver 0x01 | seq | cmd | len | payload | XOR`. ACK payload byte 0 = error code. Telemetry id `0x10`, payload = 3×uint16 BE mm
(`0xFFFF` = invalid) + battery byte. Advertise a name starting with `VEXION`. The ACK/telemetry frame layouts are this project's own proposal.

## Troubleshooting
* *Real BLE finds nothing:* expected without a compatible peripheral. Grant Nearby devices permission (Simulator tab).
* *No Hindi voice:* install Hindi TTS data in system settings; text is still shown.
* *Mic denied / no recognizer:* use typed input.
* *Cloud AI:* optional; key stored in app-private SharedPreferences (not hardware-backed). The Gemini model name is a constant in `OptionalGeminiClient` and may need updating.
* The app declares **no CAMERA permission** by design.

## Known limitations
Interface labels are English; VERA's speech/answers switch to Hindi. Screens were not visually tested on a device. Hindi strings were written without native-speaker review.
