## Media Control Integration Guide

This document describes how to integrate Media Control with TTS or Pulse player into an Android app.

-   Updated: Dec 6, 2024
-   Document version: 1.0

### Integration

#### Declare the service in the manifest
An app requires permission to run a foreground service. 
Add the POST_NOTIFICATIONS, FOREGROUND_SERVICE permission to the manifest, and if you target API 34 and above also FOREGROUND_SERVICE_MEDIA_PLAYBACK:

```
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
<uses-permission android:name="android.permission.FOREGROUND_SERVICE_MEDIA_PLAYBACK" />
<uses-permission android:name="android.permission.POST_NOTIFICATIONS"/>
```

Declare Trinity Service class in the manifest with an intent filter of MediaSessionService

```
<service
    android:name="ai.trinityaudio.sdk.TrinityPlaybackService"
    android:foregroundServiceType="mediaPlayback"
    android:exported="true">
    <intent-filter>
        <action android:name="androidx.media3.session.MediaSessionService"/>
    </intent-filter>
</service>
```

Request post notification permission on runtime before render the player if needed.
```kotlin
if (Build.VERSION.SDK_INT >= 33 &&
    checkSelfPermission(Manifest.permission.POST_NOTIFICATIONS) !=
    PackageManager.PERMISSION_GRANTED) {
        requestPermissions(arrayOf(Manifest.permission.POST_NOTIFICATIONS), 0)
    }
```


#### Enable media control
The player supports media control if the `enableMediaControl` property of `TrinityAudioPulse` or `TrinityAudio` is set to `true`. This property *must* be set before calling the `render()` method for displaying media control.
When enabled, the player will display the media control on the notification center once ready.

The media control will be removed after calling `invalidate()` on the `TrinityAudioPulse` or `TrinityAudio` instance.

#### Not supported multiple TTS players
In the current release, the media control doesn't support using multiple TTS/Pulse players on the same screen.