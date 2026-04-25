🔒 Privacy Policy 🔒
VBAN Stream - Audio & Video — Privacy Policy
Last updated: 25 April 2026

In Plain English (TL;DR)
The App does not have user accounts.
The App does not collect, send, or store any of your data on our servers — because we don't have any.
Audio, video and touch events go directly from your phone to your PC (or another phone) on your local WiFi, never through us.
Settings you type in (PC IP, port, stream names, FPS, volume) stay only on your phone.
We do not use Google Analytics, Firebase, Crashlytics, advertising, or any other third-party SDK.
If that's all you wanted to know, you're done. Details below.

1. Who We Are (Data Controller)
The "data controller" under EU Regulation 2016/679 (GDPR) is zMark816, contactable at contact@example.com.

2. Android Permissions Declared by the App
Below is the complete list of permissions the App declares in its AndroidManifest.xml, what each one is used for, and what data — if any — it touches.

2.1 RECORD_AUDIO (runtime, user-prompted)
Why: capture your microphone for the Microphone card and (combined with screen capture) capture audio played by other apps for the Screen Audio card.
Data flow: raw PCM audio → encoded as VBAN packets → sent via UDP to the PC IP address you typed in. Nothing is recorded to a file or sent to us.
2.2 INTERNET
Why: open UDP sockets for sending and receiving VBAN packets. Despite the name, this permission is also required for purely local-network traffic on Android.
Data flow: only to the IP address(es) you specify in the App. The App makes no outbound connections to the developer or any third-party server.
2.3 ACCESS_NETWORK_STATE
Why: check whether the phone has an active network connection before binding sockets.
Data flow: read-only system state, never transmitted.
2.4 ACCESS_WIFI_STATE
Why: read your phone's local WiFi IP address so the App can show it on the Connection card ("Phone IP: 192.168.x.x — tap to copy"). This is how you tell your PC where to reach the phone.
Data flow: the IP is displayed on screen and stays on the device. It is not sent anywhere automatically.
2.5 FOREGROUND_SERVICE
Why: run the streaming engine reliably in the background so audio/video keeps flowing when you switch apps or lock the screen.
Data flow: none. This is a runtime capability, not a data permission.
2.6 FOREGROUND_SERVICE_MICROPHONE (Android 14+)
Why: declares to Android that the foreground service uses the microphone. Required since Android 14 to capture audio while in background.
Data flow: see RECORD_AUDIO.
2.7 FOREGROUND_SERVICE_MEDIA_PROJECTION (Android 14+)
Why: declares to Android that the foreground service captures the screen. Required since Android 14 to use MediaProjection while in background.
Data flow: see Section 3 (MediaProjection).
2.8 FOREGROUND_SERVICE_DATA_SYNC (Android 14+)
Why: declares to Android that the foreground service moves data over the network. Used in receiver-only mode (when the PC is sending audio to the phone and no mic/screen capture is active).
Data flow: only between the phone and the IP you specify; nothing is uploaded elsewhere.
2.9 POST_NOTIFICATIONS (Android 13+)
Why: show the persistent "Streaming active" notification with a Stop button — Android requires this notification while a foreground service is running.
Data flow: notification content is local only ("Audio Streaming / Streaming active").
3. Android System Services the App Uses
The App relies on these standard Android system APIs. They run on your device; the data they touch never leaves the phone unless explicitly sent by VBAN over your WiFi.

3.1 MediaProjection / MediaProjectionManager
Used to capture screen audio and screen video after you accept Android's "Start recording or casting?" consent dialog (which appears every single time per Android policy).
Captured frames are encoded as JPEG; captured audio is encoded as PCM. Both are streamed as VBAN packets to the PC IP you set.
We never store screenshots, recordings, or thumbnails.
3.2 AudioRecord (with AudioPlaybackCaptureConfiguration)
Reads microphone PCM samples and, when Screen Audio is on, samples of audio currently playing through other apps (subject to those apps' DRM/opt-in flags set by their developers).
Samples are streamed live; no file is written.
3.3 AudioTrack
Plays back audio that arrives from the PC when you use the PC → Phone receiver. Buffer is in RAM only.
3.4 VirtualDisplay + ImageReader (Screen Video)
A virtual display is created from the MediaProjection token to receive frames in JPEG form. Frames are sent over UDP and immediately discarded; nothing is cached on disk.
3.5 WindowManager / DisplayMetrics
Read-only access to screen size, density and rotation, used to compute the correct stream resolution and to scale incoming touch coordinates. No personal data.
3.6 ClipboardManager
Used only when you tap the "Phone IP" label so the App can copy your local IP address to the clipboard. The App never reads or pastes from your clipboard.
3.7 SharedPreferences (local storage)
Stores your user choices on the device:
PC IP address you entered (per card)
Port numbers
Stream names (e.g. "Mic", "Audio_Screen", "Video_Screen")
FPS and volume sliders
Selected language
Whether you have completed the welcome screen
These values never leave your device through the App. They are subject to Android's standard app-data backup behaviour (allowBackup="true"), which means Google Backup may include them if you have that feature enabled in your Google account settings — that is a Google service governed by Google's own privacy policy.
3.8 NotificationManager / NotificationChannel
Creates the foreground notification "Audio Streaming / Streaming active". Local only.
3.9 Handler / Looper / Coroutines
Internal threading primitives. No data implications.
4. App-Defined Components
The App declares these components in its manifest:

4.1 MainActivity (exported)
The main user interface. Exported so it appears in the launcher. Receives no data from outside the App.
4.2 RemoteDisplayActivity (not exported)
Optional fullscreen view that displays the incoming video stream from the PC (for the experimental PC View feature). Frames are decoded in RAM and never saved.
4.3 AudioStreamService (foreground service, not exported)
The core background service that handles microphone, screen-audio, screen-video, audio-receiver, and second-receiver streams. Declares the foreground-service types microphone | mediaProjection | dataSync. Not exported, so other apps cannot start or bind to it.
4.4 TouchInjectionService (AccessibilityService, not exported, optional)
What it is: an Android Accessibility Service that injects touch gestures (down, move, up, click, scroll) onto your phone screen. It is required only for the experimental Remote Input and Phone↔Phone (Slave) features, where a paired PC or phone can move a "remote mouse cursor" on this phone.
When it runs: only after you explicitly enable "VBAN Stream" in Android Settings → Accessibility → Installed services. It is OFF by default and not started at install time.
What data it sees: incoming VBAN-Text packets on the listening port you configured. Each packet is a short string in the form type;x;y;extra (e.g. D;0.42;0.55). The service translates these to dispatchGesture() calls — which is the only Accessibility API the App uses.
What data it does NOT see: Although Android Accessibility Services technically can read screen content, VBAN Stream does not request or use any accessibility "read" capabilities. The service config (accessibility_service_config.xml) does not enable content observation, key-event capture, or window-content retrieval. We do not read text on screen, do not read app names, and do not record keystrokes.
How to disable: turn the service off in Settings → Accessibility, or simply do not enable it in the first place.
5. What Data the App Does Not Collect
❌ Name, email, phone number.
❌ Location, contacts, calendar, photos, files.
❌ Payment information.
❌ Device identifiers (Android ID, advertising ID, IMEI).
❌ Crash reports or analytics.
❌ No third-party SDKs (no Firebase, no Google Analytics, no Crashlytics, no ads, no Stripe).
6. Children
VBAN Stream - Audio & Video is not aimed at children under 16 and we do not knowingly collect data from them. If you believe a child has used the App in a way that requires our attention, please contact us.

7. Your Rights Under GDPR
Because the App does not store personal data on our side, in practice there is nothing for us to look up, export, correct, or delete. You can however:

Delete all local data by uninstalling the App or clearing its storage in Android Settings.
Withdraw permissions at any time from Android Settings (per-permission revocation is supported on all modern Android versions).
Disable Accessibility at any time in Settings → Accessibility.
Contact us with any privacy concern at contact@example.com. We will respond within 30 days.
You also have the right to lodge a complaint with the Italian Data Protection Authority (Garante per la Protezione dei Dati Personali — www.garanteprivacy.it).

8. Security
Because data is not stored on remote servers, the main security perimeter is your WiFi network. We strongly recommend:

Use a WPA2 or WPA3-protected WiFi network.
Avoid using the App on open / public networks (airport, café), as VBAN traffic is not encrypted by design — it is a real-time LAN protocol.
Keep the Accessibility Service disabled unless you actively use Remote Input.
9. International Transfers
None — your data does not leave your local network through the App.

10. Changes to This Policy
If we change this Policy, the new version will be published in the App's Help section and on the store listing, with the "Last updated" date refreshed. Material changes that affect your rights will be highlighted.

11. Contact
For any privacy question: marcocrisostemo@gmail.com.
