---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 17d8c0157fcd478d01452167d240fb67daeeda5b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399616"
---
## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte účet Azure a předplatné služby Rozpoznávání řeči. Pokud nemáte účet a předplatné, [vyzkoušejte bezplatnou službu Řeč](../../../get-started.md).

## <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Než budete moci něco udělat, budete muset nainstalovat sadu Speech SDK. V závislosti na platformě postupujte podle pokynů v části <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">Získat řeč <span class="docon docon-navigate-external x-hidden-focus"></span> SDK</a> řeči článku sady Speech SDK.

## <a name="import-dependencies"></a>Importovat závislosti

Chcete-li spustit příklady v tomto `import` článku, uveďte následující příkazy v horní části souboru kódu pythonu.

```python
import os
import azure.cognitiveservices.speech as speechsdk
```

## <a name="sensitive-data-and-environment-variables"></a>Citlivá data a proměnné prostředí

Příklad zdrojového kódu v tomto článku závisí na proměnných prostředí pro ukládání citlivých dat, jako je například klíč a oblast odběru prostředků řeči. Soubor kódu pythonu obsahuje dvě hodnoty, které jsou přiřazeny `SPEECH__SUBSCRIPTION__KEY` `SPEECH__SERVICE__REGION`z proměnných prostředí hostitelských počítačů, a to a . Obě tyto proměnné jsou v globálním oboru, takže jsou přístupné v rámci definice funkce souboru kódu. Další informace o proměnných prostředí naleznete v [tématu proměnné prostředí a konfigurace aplikace](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```python
speech_key, service_region = os.environ['SPEECH__SUBSCRIPTION__KEY'], os.environ['SPEECH__SERVICE__REGION']
```

## <a name="create-a-speech-translation-configuration"></a>Vytvoření konfigurace překladu řeči

Chcete-li volat službu Řeč pomocí sady Speech [`SpeechTranslationConfig`][config]SDK, je třeba vytvořit . Tato třída obsahuje informace o vašem předplatném, jako je klíč a přidružená oblast, koncový bod, hostitel nebo autorizační token.

> [!TIP]
> Bez ohledu na to, zda provádíte rozpoznávání řeči, syntézu řeči, překlad nebo záměr rozpoznávání, budete vždy vytvořit konfiguraci.

Existuje několik způsobů, jak můžete inicializovat [`SpeechTranslationConfig`][config]:

* S předplatným: předat klíč a přidružené oblasti.
* S koncovým bodem: předají v koncovém bodě služby Řeči. Klíč nebo autorizační token je volitelný.
* S hostitelem: předat adresu hostitele. Klíč nebo autorizační token je volitelný.
* S tokenem autorizace: předavte autorizační token a přidruženou oblast.

Podívejme se na to, [`SpeechTranslationConfig`][config] jak se vytvoří pomocí klíče a oblasti. Na stránce [podpory oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) najdete identifikátor oblasti.

```python
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)
```

## <a name="change-source-language"></a>Změna zdrojového jazyka

Jednou z běžných úloh překladu řeči je určení vstupního (nebo zdrojového) jazyka. Podívejme se na to, jak byste změnili vstupní jazyk na italštinu. V kódu, interakci [`SpeechTranslationConfig`][config] s instancí, `speech_recognition_language` přiřazení k vlastnosti.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    # Source (input) language
    translation_config.speech_recognition_language = from_language
```

Vlastnost [`speech_recognition_language`][recognitionlang] očekává řetězec formátu jazykového národního prostředí. Libovolnou hodnotu můžete zadat ve **sloupci Národní prostředí** v seznamu podporovaných [národních prostředí/jazyků](../../../language-support.md).

## <a name="add-translation-language"></a>Přidání jazyka překladu

Dalším běžným úkolem překladu řeči je určení cílových jazyků překladu, je vyžadovánalespoň jeden, ale násobky jsou podporovány. V následujícím fragmentu kódu, francouzština a němčina jako cíle překladového jazyka.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = "it-IT"

    # Translate to languages. See, https://aka.ms/speech/sttt-languages
    translation_config.add_target_language("fr")
    translation_config.add_target_language("de")
```

Při každém [`add_target_language`][addlang]volání je určen nový cílový jazyk překladu. Jinými slovy, když je rozpoznávání řeči ze zdrojového jazyka, každý cílový překlad je k dispozici jako součást výsledné operace překladu.

## <a name="initialize-a-translation-recognizer"></a>Inicializovat nástroj pro rozpoznávání překladů

Po vytvoření [`SpeechTranslationConfig`][config]je dalším krokem inicializaci . [`TranslationRecognizer`][recognizer] Když inicializujete [`TranslationRecognizer`][recognizer], budete ji muset `translation_config`předat . Objekt konfigurace poskytuje pověření, která služba rozpoznávání řeči vyžaduje k ověření vašeho požadavku.

Pokud rozpoznávání řeči rozpoznáte pomocí výchozího mikrofonu zařízení, [`TranslationRecognizer`][recognizer] mělo by vypadat takto:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
```

Pokud chcete zadat vstupní zvukové zařízení, budete muset vytvořit [`AudioConfig`][audioconfig] a `audio_config` zadat parametr při [`TranslationRecognizer`][recognizer]inicializaci aplikace .

> [!TIP]
> [Přečtěte si, jak získat ID zařízení pro vstupní zvukové zařízení](../../../how-to-select-audio-input-devices.md).

Nejprve budete odkazovat `AudioConfig` na objekt takto:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

Pokud chcete místo mikrofonu zadat zvukový soubor, budete muset zadat `audioConfig`soubor . Však při vytváření [`AudioConfig`][audioconfig], namísto `use_default_microphone=True`volání s , `filename="path-to-file.wav"` budete `filename` volat s a zadat parametr.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(filename="path-to-file.wav")
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

## <a name="translate-speech"></a>Překlad řeči

Chcete-li přeložit řeč, sada Speech SDK spoléhá na mikrofon nebo vstup zvukového souboru. Rozpoznávání řeči dochází před překladem řeči. Po inicializování všech objektů zavolejte funkci rozpoznat jednou a získejte výsledek.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'RECOGNIZED "{from_language}": {result.text}\n' +
            f'TRANSLATED into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

Další informace o převodu řeči na text naleznete [v základech rozpoznávání řeči](../../../speech-to-text-basics.md).

## <a name="synthesize-translations"></a>Syntetizovat překlady

Po úspěšném rozpoznávání řeči a překladu výsledek obsahuje všechny překlady ve slovníku. Klíč [`translations`][translations] slovníku je cílový překladový jazyk a hodnota je přeložený text. Rozpoznaná řeč může být přeložena a poté syntetizována v jiném jazyce (řeč na řeč).

### <a name="event-based-synthesis"></a>Syntéza založená na událostech

Objekt `TranslationRecognizer` zpřístupňuje `Synthesizing` událost. Událost je několikrát vyvolána a poskytuje mechanismus pro načtení syntetizovaného zvuku z výsledku rozpoznávání překladu. Pokud překládáte do více jazyků, přečtěte si [informace o ruční syntéze](#manual-synthesis). Zadejte hlas syntézy [`voice_name`][voicename] přiřazením a poskytnout `Synthesizing` obslužnou rutinu události pro událost, získat zvuk. Následující příklad uloží přeložený zvuk jako soubor *WAV.*

> [!IMPORTANT]
> Syntéza založená na událostech funguje pouze s jedním překladem, **nepřidávejte** více cílových jazyků překladu. Kromě toho [`voice_name`][voicename] by měl být stejný jazyk jako cílový jazyk překladu, například; `"de"` mohl mapovat na `"de-DE-Hedda"`.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    # See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translation_config.voice_name = "de-DE-Hedda"

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)

    def synthesis_callback(evt):
        size = len(evt.result.audio)
        print(f'Audio synthesized: {size} byte(s) {"(COMPLETED)" if size == 0 else ""}')

        if size > 0:
            file = open('translation.wav', 'wb+')
            file.write(evt.result.audio)
            file.close()

    recognizer.synthesizing.connect(synthesis_callback)

    print(f'Say something in "{from_language}" and we\'ll translate into "{to_language}".')

    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'Recognized "{from_language}": {result.text}\n' +
            f'Translated into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

### <a name="manual-synthesis"></a>Ruční syntéza

Slovník [`translations`][translations] lze použít k syntéze zvuku z překladového textu. Iterovat prostřednictvím každého překladu a syntetizovat překlad. Při vytváření `SpeechSynthesizer` instance `SpeechConfig` musí mít objekt [`speech_synthesis_voice_name`][speechsynthesisvoicename] svou vlastnost nastavenou na požadovaný hlas. Následující příklad překládá do pěti jazyků a každý překlad je pak syntetizován do zvukového souboru v odpovídajícím neurálním jazyce.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', [ 'de', 'en', 'it', 'pt', 'zh-Hans' ]

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    synthesize_translations(result=result)

def synthesize_translations(result):
    language_to_voice_map = {
        "de": "de-DE-KatjaNeural",
        "en": "en-US-AriaNeural",
        "it": "it-IT-ElsaNeural",
        "pt": "pt-BR-FranciscaNeural",
        "zh-Hans": "zh-CN-XiaoxiaoNeural"
    }
    print(f'Recognized: "{result.text}"')

    for language in result.translations:
        translation = result.translations[language]
        print(f'Translated into "{language}": {translation}')

        speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
        speech_config.speech_synthesis_voice_name = language_to_voice_map.get(language)
        
        audio_config = speechsdk.audio.AudioOutputConfig(filename=f'{language}-translation.wav')
        speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
        speech_synthesizer.speak_text_async(translation).get()

translate_speech_to_text()
```

Další informace o syntéze řeči naleznete [v základech syntézy řeči](../../../text-to-speech-basics.md).

[config]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python
[audioconfig]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig?view=azure-python
[recognizer]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognizer?view=azure-python
[recognitionlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python
[addlang]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#add-target-language-language--str-
[translations]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognitionresult?view=azure-python#translations
[voicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig?view=azure-python#voice-name
[speechsynthesisvoicename]: https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#speech-synthesis-voice-name
