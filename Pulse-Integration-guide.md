## Pulse Player Developer Guide


This document describes how to integrate Pulse Player into a
an Android app as well as how to configure and control it.

-   Updated: Oct 30, 2024
-   Document version: 1.0

### Integration

* * * * *

In order to integrate a player into an app, first import the SDK.
Please follow the instructions [here](https://github.com/TrinityAudioSDK/trinityaudio-android-sdk/blob/main/Integration-guide.md#integration)

## Usage and main objects

#### SDK initialization
* * * * *
To start using the SDK - first initialize the `TrinityAudioPulse` instance by calling the `create` method
on the `TrinityAudioPulse` interface
```java
public interface TrinityAudioPulse {
   public companion object {
    public final fun create(
        context: Context,
        listener: TrinityPulsePlayerListener?
        ): TrinityAudioPulse
    }
}
```

| Name      | Description                                                                                                         |
|-----------|---------------------------------------------------------------------------------------------------------------------|
| context   | The current Android context, could be the main context of the application or the current activity                   |
| listener  | Used for monitoring and tracking player events. it should implement the TrinityPulsePlayerListener interface |

Example:

Kotlin
```kotlin
var trinityAudio = TrinityAudioPulse.create(context, listener)
```

Java
```java
TrinityAudioPulse trinityAudio = TrinityAudioPulse.Companion.create(context, listener);
```

The result is an instance that implements the `TrinityAudioPulse` interface


* * * * *


#### Rendering the Pulse player

* * * * *
Add a `TrinityPlayerView` to the xml layout file where you'd like the player to render
```xml
<ai.trinityaudio.sdk.TrinityPlayerView
android:id="@+id/trinityPlayerView"
android:layout_width="match_parent" 
android:layout_height="350dp"
android:layout_marginTop="10dp" />
```

To render the player, call the render method of the `TrinityAudioPulse` instance.
```kotlin
@Throws(TrinityException::class)
fun render(
   rootView: ViewGroup,
   playerView: TrinityPlayerView,
   unitId: String,
   playlistURL: String,
   settings: Map<String, String>? = null,
)
```

| Parameter                 | Description                                                                                                         |
|---------------------------|---------------------------------------------------------------------------------------------------------------------|
| rootView                  | The `ViewGroup` that the `playerView` will overlay when expanded, only support **FrameLayout** in the current version. The `playerView` will overlay the `rootView` when expanded                                          |
| playerView                | The TrinityPlayerView                                                                                               |
| unitId                    | Your player unit identifier - will be provided by TrinityAudio team                                                 |
| playlistURL                | URL which contains the playlist content                                                                                       |
| settings                  | Map with optional* player settings                                                                                  |

**Common Player Settings:**

For the full list of available params look in our [pulse integration doc](https://github.com/TrinityAudioSDK/trinityaudio-web-sdk/blob/main/Pulse.md#tag-parameters) under
`Script Tag Parameters` section.

Example:

Kotlin
```kotlin
try {
   val unitID = "[PLACE_UNIT_ID]"
   val playlistURL = "[RSS URL]"
   val settings: Map<String, String> =  mapOf()
   trinityAudioPulse.render(rootView, playerView, unitID, playlistURL, settings)
} catch (ex: Exception) {
   ex.printStackTrace()
}
```

Java
```java
try {
   String unitID = "[PLACE_UNIT_ID]";
   String playlistURL = "[RSS URL]";
   Map<String, String> settings = Map.of();
   trinityAudio.render(rootView, playerView, unitID, playlistURL, settings);
} catch (TrinityException e) {
   e.printStackTrace();
}
```

#### Pulse Player Sliding Effect for Specific Unit IDs

The pulse player supports the sliding effect for some unit IDs provided by the Trinity Audio Team. 

The pulse unit should open in a specific location with only part of it visible, such as the top navigation.
Ensure you provide the correct `rootView` for the Pulse player to display the animation correctly.

*Note*
- The `rootView` should be a **FrameLayout**.
- If you are putting the player on a scrollView, make sure the scrollView's one direct child is a **FrameLayout** and set it as the `rootView`

#### Minimum Sizes for the Pulse Player

*Default Sizes:*
- **Width:** Min = 300 dp (no maximum)  
- **Height:** Min & Max = 350 dp  

*Pulse-Sliding Minimum Sizes:*  
- **Width:** Min = 300 dp (no maximum)  
- **Height:** Min = 150 dp, Max = 350 dp  

#### GDPR & US privacy support
For details on GDPR US privacy support please go [here](https://github.com/TrinityAudioSDK/trinityaudio-android-sdk/blob/main/Integration-guide.md#gdpr--us-privacy-support)

#### Player API

The API supports various means of controlling the player programmatically. 
For instance - To pause the player's audio use the `pause()` method of `TrinityAudioPulse`.

Kotlin
```kotlin
trinityAudioPulse.pause()
```

Java
```java
trinityAudioPulse.pause();
```

Some other API methods: 

`pauseAll()` - Pauses all players (in case of multiple player renders)
`play()` - Resumes audio playback (NOTE: will NOT begin playback without user interaction)

The trinity player offers multiple other APIs and to interact with it.
This can be done by invoking the JS player API methods.

For the full reference please check the [JS API docs](https://github.com/TrinityAudioSDK/trinityaudio-web-sdk/blob/main/Pulse.md#api) under `API` section.

* * * * *

#### Player Exception

When using the player, there are several exceptions tha may occur, please handle them in runtime.

Kotlin
```kotlin
class InvalidURLException(message: String) : TrinityException(message)

class UnknownException(message: String, cause: Throwable? = null) : TrinityException(message, cause)

class JSONParserException(message: String, cause: Throwable? = null) : TrinityException(message, cause)

class IllegalArgumentException(message: String, cause: Throwable? = null) : TrinityException(message, cause)
```

Java
```java
public final class IllegalArgumentException public constructor(message: kotlin.String, cause: kotlin.Throwable?) : ai.trinityaudio.sdk.TrinityException {}

public final class InvalidURLException public constructor(message: kotlin.String) : ai.trinityaudio.sdk.TrinityException {}

public final class JSONParserException public constructor(message: kotlin.String, cause: kotlin.Throwable?) : ai.trinityaudio.sdk.TrinityException {}

public final class UnknownException public constructor(message: kotlin.String, cause: kotlin.Throwable?) : ai.trinityaudio.sdk.TrinityException {}
```

* * * * *

#### Monitoring and Analytics 

* * * * *

In order to track the player events - You can configure a listener for the TrinityAudioPulse instance you created.

The listener is a property of the TrinityAudioPulse instance, conforming to the listener interface `TrinityPulsePlayerListener`

The listener can be used to subscribe to events emitted by the player. This can allow you to connect your in-app analytics platform to track player usage.

TrinityPulsePlayerListener methods.

* * * * *

- To receive a callback when the player is ready to play, implement this optional method

```java
public void trinityOnPlayerReady(@NonNull TrinityAudioPulse trinityAudio, @NonNull playerId: String)
```

- For detecting changes of the player browse mode. (For sliding animation units only).
```java
public void trinityOnBrowseMode(@NonNull TrinityAudioPulse trinityAudio, @NonNull Boolean toggled) {} 
```


- For tracking player events:
```java 
public void trinityDidReceivePostMessage(@NonNull TrinityAudioPulse trinityAudio, @NonNull Map<String, ?> map) {}
```

This tracks all `postMessages` that are triggered by the player - similar to the web integration.
Event structure is :
```javascript
{
  type: 'TRINITY_PULSE',
  value: {
    action: String,
    message: Object|String|undefined,
    playerId: String
  }
}
```
For a list of events please see [here](https://github.com/TrinityAudioSDK/trinityaudio-web-sdk/blob/main/Pulse.md#events) under `Events` section. 
