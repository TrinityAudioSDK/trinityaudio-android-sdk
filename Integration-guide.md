## Trinity Audio Android SDK Integration Guide


This document describes how to integrate the Trinity Audio Player into
an Android app as well as how to configure and control it.

-   Updated: Dec 9, 2023
-   Document version: 1.0

### Integration

* * * * *

In order to integrate a player into an app, first import the SDK from the Maven Central. \
Add this to your app `build.gradle` dependencies section. Please use latest version available 
```groovy
implementation("ai.trinityaudio:trinity-player:[LATEST-VERSION]")
```

## Usage and main objects

#### SDK initialization
* * * * *
To start using the SDK - first initialize the `TrinityAudio` instance by calling the `create` method
on the `TrinityAudio` interface
```java
public interface TrinityAudio {
   public companion object {
    public final fun create(
        context: Context,
        listener: TrinityPlayerListener?
        ): TrinityAudio
    }
}
```

| Name      | Description                                                                                                               |
|-----------|---------------------------------------------------------------------------------------------------------------------------|
| context   | The current Android context, could be the main context of the application or the current activity                         |
| listener  | It would be used to monitoring and tracking the player events. it should be implement the TrinityPlayerListener interface |

Example:

Kotlin
```kotlin
var trinityAudio = TrinityAudio.create(context, listener)
```

Java
```java
TrinityAudio trinityAudio = TrinityAudio.Companion.create(context, listener);
```

The result is an instance that implements the `TrinityAudio` interface


* * * * *


#### Rendering the TTS player

* * * * *
Add a `TrinityPlayerView` to the xml layout file where you'd like the player to render
```xml
<ai.trinityaudio.sdk.TrinityPlayerView
android:id="@+id/trinityPlayerView"
android:layout_width="match_parent" 
android:layout_height="80dp"
android:layout_marginTop="10dp" />
```

To render the player, call the render method of the `TrinityAudio` instance.
```kotlin
@Throws(TrinityException::class)
fun render(
   rootView: ViewGroup,
   playerView: TrinityPlayerView,
   unitId: String,
   fabViewTopLeftCoordinates: Point? = null,
   contentURL: String,
   settings: Map<String, String>? = null,
)
```

| Parameter                 | Description                                                                                                         |
|---------------------------|---------------------------------------------------------------------------------------------------------------------|
| rootView                  | The root view in the screen's layout. It should be a subclass of ViewGroup                                          |
| playerView                | The TrinityPlayerView                                                                                               |
| unitId                    | Your player unit identifier - will be provided by TrinityAudio team                                                 |
| fabViewTopLeftCoordinates | Top left corner of the FAB (Floating Action Button) unit. Optional value. Pass null - to disable FAB functionality. |
| contentURL                | URL which contains the content.                                                                                     |
| settings                  | Map with optional* player settings                                                                                  |

Common Player Settings:

| Name     | Description                                                                               |
| -------- | ----------------------------------------------------------------------------------------- |
| language | Language of the content provided. Use this incase it differs from the language configured |
| voiceId  | Overrides the player level configuration for voice ID                                     |

For the full list of available params look in our [player setting doc](https://trinity-audio-player.s3.amazonaws.com/TTS.pdf) under
[Here](https://trinity-audio-player.s3.amazonaws.com/TTS.pdf) under
`Script Tag Parameters` section.

* Dark mode support - the SDK will attempt to identify whether the containing app is running in dark-mode, in which case it will change the player theme to a dark-mode theme if applicable

Example:

Kotlin
```kotlin
try {
   val unitID = "[PLACE_UNIT_ID]"
   val contentURL = "[URL TO READ TEXT FROM]"
   val settings: Map<String, String> =  mapOf()
   val fabViewTopLeftCoordinates = Point(50, 100)
   trinityAudio.render(rootView, playerView, unitID, fabViewTopLeftCoordinates, contentURL, settings)
} catch (ex: Exception) {
   ex.printStackTrace()
}
```

Java
```java
try {
   String unitID = "[PLACE_UNIT_ID]";
   String contentURL = "[URL TO READ TEXT FROM]";
   Map<String, String> settings = Map.of();
   Point fabViewTopLeftCoordinates = new Point(30, 100)
   trinityAudio.render(rootView, playerView, unitID, fabViewTopLeftCoordinates, contentURL, settings);
} catch (TrinityException e) {
   e.printStackTrace();
}
```
#### Autoplay
The player supports autoplay if the `autoPlay` property of `TrinityAudio` is set to `true`. The autoplay property *must* be set before calling the `render()` method for autoplay to work.
When enabled, the player will play the audio once ready, without waiting for user interaction.

```
//example 
trinityAudio.autoPlay = true
```


#### GDPR & US privacy support
GDPR & US privacy consent string can be directly passed to the player as part of the `settings` map.  
These values are not mandatory, and in the case of their absence Trinity will look for these values in the IAB standard location as detailed [here](https://github.com/InteractiveAdvertisingBureau/GDPR-Transparency-and-Consent-Framework/blob/master/Mobile%20In-App%20Consent%20APIs%20v1.0%20Final.md#cmp-internal-structure-defined-api-)

The supported params are:

Kotlin
  | name                     | description                            |
  | ------------------------ | -------------------------------------- |
  | TrinityParams.USPrivacy.raw | US Privacy consent string, e.g. 1-Y-   |
  | TrinityParams.GDPR.raw   | GDPR version 1. 1 for accepted, 0 - no |
  | TrinityParams.GDPRConsent.raw | GDPR version 2 consent string     |

Java
  | name                     | description                            |
  | ------------------------ | -------------------------------------- |
  | TrinityParams.USPrivacy.getRaw() | US Privacy consent string, e.g. 1-Y-   |
  | TrinityParams.GDPR.getRaw()   | GDPR version 1. 1 for accepted, 0 - no |
  | TrinityParams.GDPRConsent.getRaw() | GDPR version 2 consent string          |

For example:

Kotlin
```kotlin
try {
   val unitID = "[PLACE_UNIT_ID]"
   val contentURL = "[URL TO READ TEXT FROM]"
   val setting: Map<String, String> = mapOf(
    TrinityParams.US_PRIVACY.raw to "1-Y-",
    TrinityParams.GDPR.raw to "0",
    TrinityParams.GDPR_CONSENT.raw to "CPSzMzJPTeGtxADABCENB_CoAP_AAEJAAAAADGwBAAGABPADCAY0BjYAgADAAngBhAMaAAA.YAAABBBBB"
   )
   val fabViewTopLeftCoordinates = Point(50, resources.displayMetrics.heightPixels * 2 / 3)
   trinityAudio.render(rootView, playerView, unitID, fabViewTopLeftCoordinates, contentURL, setting)
} catch (ex: TrinityException) {
   ex.printStackTrace()
}
```

Java
```java
try {
    String unitID = "[PLACE_UNIT_ID]";
    String contentURL = "[URL TO READ TEXT FROM]";
    Map<String, String> settings = Map.of(
        TrinityParams.US_PRIVACY.getRaw(), "1-Y-",
        TrinityParams.GDPR.getRaw(), "1",
        TrinityParams.GDPR_CONSENT.getRaw(), "CPSzMzJPTeGtxADABCENB_CoAP_AAEJAAAAADGwBAAGABPADCAY0BjYAgADAAngBhAMaAAA.YAAABBBBB"
    );
    trinityAudio.render(rootView, playerView, unitID, new Point(30, 100), contentURL, settings);
} catch (TrinityException e) {
    e.printStackTrace();
}
```

#### Player API

To pause the player's audio use the `pause()` method of `TrinityAudioProtocol`.
Example:

Kotlin
```kotlin
trinityAudio.pause()
```

Java
```java
trinityAudio.pause();
```

*this method accepts playerID which is only required in multiple player setups.
*The `playerId` can be obtained from the `playerId` property of `TrinityAudioProtocol`. However, it is only available after the `trinityOnPlayerReady(service: TrinityAudio, playerId: String)` method of `TrinityPlayerListener` is called.

To pause all players, use the `pauseAll()` method of `TrinityAudioProtocol`.
For example : 

Kotlin
```kotin
    trinityAudio.pauseAll()
```

Java
```java
   trinityAudio.pauseAll();
```

To resume playback, use the `play()` method of `TrinityAudioProtocol`.
For example : 

Kotlin
```kotin
    trinityAudio.play()
```

Java
```java
   trinityAudio.play();
```

The trinity player offers multiple other APIs and to interact with it.
This can be done by invoking the JS player API methods.
For example - to get the text being read by the player you can invoke:

Kotlin
```kotlin
trinityAudio.evaluate("TRINITY_PLAYER.api.resultReadingText;")
```

Java
```java 
trinityAudio.evaluate("TRINITY_PLAYER.api.resultReadingText;");
```

For the full reference please check the [JS API docs](https://trinity-audio-player.s3.amazonaws.com/TTS.pdf) under `API` section.

* * * * *

#### Player Exception

When using the player, there are list exception could throwing, please handle them in runtime.

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

You can configure a listener for the TrinityAudio instance you created.

The listener is a property of the TrinityAudio instance, conforming to
the listener interface TrinityPlayerListener.

The listener can be used to subscribe to events emitted by the player. This can allow you to connect your in-app analytics platform to track player usage.

TrinityPlayerListener methods.

* * * * *

- To receive a callback when the player is ready to play, implement this optional method

```java
public void trinityOnPlayerReady(@NonNull TrinityAudio trinityAudio, @NonNull playerId: String)
```

- For detecting changes of the content height in different states use:

```java
public void trinityOnDetectUpdateForContentHeight(@NonNull TrinityAudio trinityAudio, float v, @NonNull TrinityStates trinityStates) {} 
```

TrinityState - an enum with two cases :

```java 
public final enum class TrinityStates private constructor() : kotlin.Enum<ai.trinityaudio.sdk.TrinityStates> {
    MAIN,
    FAB;
}                                                                     
```

- For tracking player events:
```java 
public void trinityDidReceivePostMessage(@NonNull TrinityAudio trinityAudio, @NonNull Map<String, ?> map) {}
```

This tracks all `postMessages` that are triggered by the player - similar to the web integration.
Event structure is :
```javascript
{
  type: 'TRINITY_TTS',
  value: {
    action: String,
    message: Object|String|undefined,
    playerId: String
  }
}
```
For a list of events please see [here](https://trinity-audio-player.s3.amazonaws.com/TTS.pdf) under `Events` section. 
