# ElevenLabs Python-Bibliothek

![LOGO](https://github.com/elevenlabs/elevenlabs-python/assets/12028621/21267d89-5e82-4e7e-9c81-caf30b237683)

[![fern shield](https://img.shields.io/badge/%F0%9F%8C%BF-SDK%20von%20Fern%20erzeugt-brightgreen)](https://buildwithfern.com/?utm_source=fern-elevenlabs/elevenlabs-python/readme)
[![Discord](https://badgen.net/badge/black/ElevenLabs/icon?icon=discord&label)](https://discord.gg/elevenlabs)
[![Twitter](https://badgen.net/badge/black/elevenlabsio/icon?icon=twitter&label)](https://twitter.com/elevenlabsio)
[![PyPI - Python Version](https://img.shields.io/pypi/v/elevenlabs?style=flat&colorA=black&colorB=black)](https://pypi.org/project/elevenlabs/)
[![Downloads](https://static.pepy.tech/personalized-badge/elevenlabs?period=total&units=international_system&left_color=black&right_color=black&left_text=Downloads)](https://pepy.tech/project/elevenlabs)

Die offizielle Python-API für [ElevenLabs](https://elevenlabs.io/) [Text-zu-Sprache-Software.](https://elevenlabs.io/text-to-speech) Eleven bringt mit nur wenigen Codezeilen die überzeugendsten, reichhaltigsten und lebensechtesten Stimmen für Kreative und Entwickler.

## 📖 API & Dokumentation

Sieh dir die [HTTP-API-Dokumentation](https://elevenlabs.io/docs/api-reference) an.

## ⚙️ Installation

```bash
pip install elevenlabs==0.3.0b0

Check out the [HTTP API documentation](https://elevenlabs.io/docs/api-reference).



## V3 Migrationsleitfaden
> Das SDK wurde in Version 3 neu geschrieben und wird jetzt programmatisch aus unserer OpenAPI-Spezifikation generiert. Mit dieser Veröffentlichung
> gibt es einige wesentliche Änderungen.

### Client-Instantiierung
Das SDK exportiert jetzt eine Client-Klasse, die du instanziieren musst, um verschiedene
Endpunkte in unserer API aufzurufen.

```python
from elevenlabs.client import ElevenLabs

client = ElevenLabs(api_key="...")


Hier ist der übersetzte Teil des Textes im Markdown-Format auf Deutsch:

```markdown
## V3 Migrationsleitfaden
> Das SDK wurde in Version 3 neu geschrieben und wird jetzt programmatisch aus unserer OpenAPI-Spezifikation generiert. Mit dieser Veröffentlichung
> gibt es einige wesentliche Änderungen.

### Client-Instantiierung
Das SDK exportiert jetzt eine Client-Klasse, die du instanziieren musst, um verschiedene
Endpunkte in unserer API aufzurufen.

```python
from elevenlabs.client import ElevenLabs

client = ElevenLabs(api_key="...")
```
Als Teil dieser Änderung gibt es nicht mehr die Methoden `set_api_key` und `get_api_key`.

### HTTPX
Das SDK verwendet jetzt unter der Haube HTTPX. Dies ermöglicht uns, neben einem synchronen Client auch einen asynchronen Client zu exportieren. Beachte, dass du auch deinen eigenen HTTPX-Client übergeben kannst.

```python
from elevenlabs.client import AsyncElevenLabs

client = AsyncElevenLabs(api_key="...", httpx=httpx.AsyncClient(...))
```

### Entfernen statischer Methoden
Es gibt keine statischen Methoden mehr, die direkt auf Objekten verfügbar sind. Zum Beispiel
anstatt `Models.from_api()` kannst du jetzt `client.models.get_all()` verwenden.

Die Umbenennungen sind unten aufgeführt:

  `User.from_api()` -> `client.users.get()`

  `Models.from_api()` -> `client.models.get_all()`

  `Voices.from_api()` -> `client.voices.get_all()`

  `History.from_api()` -> `client.history.get_all()`


### Beibehaltung von Hilfsmethoden
Das SDK exportiert weiterhin Methoden für `generate`, `play`, `clone` und
`voices`, die im README unten detailliert beschrieben werden.
```


## 🗣️ Voices

List all your available voices with `voices()`.
```py
from elevenlabs import voices, generate

voices = voices()
audio = generate(text="Hello there!", voice=voices[0])
print(voices)
```

<details> <summary> Show output </summary>

```py
Voices(
    voices=[
        Voice(
            voice_id='21m00Tcm4TlvDq8ikWAM',
            name='Rachel',
            category='premade',
            settings=None,
        ),
        Voice(
            voice_id='AZnzlk1XvdvUeBnXmlld',
            name='Domi',
            category='premade',
            settings=None,
        ),
        ...
    ]
)
```

</details>

Build a voice object with custom settings to personalize the voice style, or call 
`client.voices.get_settings("your-voice-id")` to get the default settings for the voice.

```py
from elevenlabs import Voice, VoiceSettings, generate

audio = generate(
    text="Hello! My name is Bella.",
    voice=Voice(
        voice_id='EXAVITQu4vr4xnSDxMaL',
        settings=VoiceSettings(stability=0.71, similarity_boost=0.5, style=0.0, use_speaker_boost=True)
    )
)

play(audio)
```

</details>

## Clone Voice

Clone your voice in an instant. Note that voice cloning requires an API key, see below.

```py
from elevenlabs import clone, generate, play

voice = clone(
    name="Alex",
    description="An old American male voice with a slight hoarseness in his throat. Perfect for news", # Optional
    files=["./sample_0.mp3", "./sample_1.mp3", "./sample_2.mp3"],
)

audio = generate(text="Hi! I'm a cloned voice!", voice=voice)

play(audio)
```

## 🚿 Streaming

Stream audio in real-time, as it's being generated.

```py
from elevenlabs import generate, stream

audio_stream = generate(
  text="This is a... streaming voice!!",
  stream=True
)

stream(audio_stream)
```

### Input streaming
Stream text chunks into audio as it's being generated, with <1s latency. Note: if chunks don't end with space or punctuation (" ", ".", "?", "!"), the stream will wait for more text.
```py
from elevenlabs import generate, stream

def text_stream():
    yield "Hi there, I'm Eleven "
    yield "I'm a text to speech API "

audio_stream = generate(
    text=text_stream(),
    voice="Nicole",
    model="eleven_monolingual_v1",
    stream=True
)

stream(audio_stream)
```

## HTTP Client
The SDK also exposes an HTTP client that you can use to query our 
various endpoints. 

```python
from elevenlabs.client import ElevenLabs

eleven = ElevenLabs(
  api_key="MY_API_KEY" # Defaults to ELEVEN_API_KEY
)
models = eleven.models.get_all()
```

## Async Client 
Use `AsyncElevenLabs` if you want to make API calls asynchronously. 

```python
import asyncio

from elevenlabs.client import AsyncElevenLabs

eleven = AsyncElevenLabs(
  api_key="MY_API_KEY" # Defaulsts to ELEVEN_API_KEY
)

async def print_models() -> None:
    models = eleven.models.get_all()
    print(models)

asyncio.run(print_models())
```

## Elevenlabs-Modul
Alle Modelle von ElevenLabs sind im `elevenlabs`-Modul eingebettet.

![Alt-Text](assets/module.png)

## Unterstützte Sprachen

Wir unterstützen 29 Sprachen und über 100 Akzente. Entdecke [alle Sprachen](https://elevenlabs.io/languages).

<img src="https://github.com/elevenlabs/elevenlabs-js/blob/main/assets/languages.png" width="900">

## Mitwirkung

Obwohl wir Open-Source-Beiträge zu diesem SDK schätzen, wird diese Bibliothek programmatisch generiert. Direkte Ergänzungen zu dieser Bibliothek müssten in unseren Generierungscode übertragen werden, sonst würden sie bei der nächsten generierten Veröffentlichung überschrieben. Fühle dich frei, einen Pull Request als Proof of Concept zu öffnen, aber sei dir bewusst, dass wir ihn nicht so, wie er ist, zusammenführen können. Wir empfehlen, zunächst ein Issue zu öffnen, um mit uns zu diskutieren!

Andererseits sind Beiträge zur README immer sehr willkommen!
