---
title: Jak získat události pozice obličeje pro sadu LIP – synchronizace
titleSuffix: Azure Cognitive Services
description: Sada Speech SDK podporuje událost viseme v syntézě řeči, která se používá k reprezentaci klíčových funkcí v pozorovaném řeči, jako je například pozice sad LIP, vidlice a jazyka při vytváření konkrétního foném.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: e97c48d4e42627d0fc2caaa4f66e81b9a0cafa86
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643889"
---
# <a name="get-facial-pose-events"></a>Získat události pozice obličeje

> [!NOTE]
> Viseme funguje jenom pro `en-US-AriaNeural` hlas.

Viseme je vizuální popis foném v mluveném jazyce.
Definuje polohu obličeje a úst při mluvení slova.
Každý viseme znázorňuje klíčovou obličeje pro konkrétní sadu fonémy.
Mezi visemes a fonémy neexistuje žádná souvislost 1:1.
Často několik fonémy odpovídá jednomu viseme, protože několik fonémy se při vytvoření, jako je například a, vyhledá na tváři `s` `z` .
Viz [Tabulka mapování mezi visemes a fonémy](#map-phonemes-to-visemes).

Pomocí visemes můžete vytvořit více možností pomocníka pro vysílat přirozeného a inteligentního vysílání, další interaktivní hry a animované znaky a intuitivnější výuková videa pro jazyky. Lidé s postižením sluchu můžou také vizuálně vylepšit zvuk a obsah mluveného slova "se čtením ze sady LIP", který zobrazuje visemes na animovanou plochu.

## <a name="get-viseme-events-with-the-speech-sdk"></a>Získání událostí viseme pomocí sady Speech SDK

Pro provádění událostí viseme převádíme vstupní text na sadu foném sekvencí a jejich odpovídající sekvence viseme. Odhadneme čas zahájení každého viseme ve zvukovém zvuku. Události viseme obsahují sekvenci viseme ID, z nichž každý má posun na zvuk, kde se objeví viseme. Tyto události mohou řídit animace pro ústa, které simulují osobu, která mluví se vstupním textem.

| Parametr | Popis |
|-----------|-------------|
| ID viseme | Celé číslo, které určuje viseme. V angličtině (USA) nabízíme 22 různých visemes, které poobrazují tvary úst pro konkrétní sadu fonémy. Viz [Tabulka mapování mezi ID viseme a fonémy](#map-phonemes-to-visemes).  |
| Posun zvuku | Čas zahájení každého viseme, v taktech (100 nanosekund). |

Pokud chcete získat události viseme, přihlaste se k odběru `VisemeReceived` události v sadě Speech SDK.
Následující fragmenty kódu ukazují, jak se přihlásit k odběru události viseme.

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig))
{
    // Subscribes to viseme received event
    synthesizer.VisemeReceived += (s, e) =>
    {
        Console.WriteLine($"Viseme event received. Audio offset: " +
            $"{e.AudioOffset / 10000}ms, viseme id: {e.VisemeId}.");
    };

    var result = await synthesizer.SpeakSsmlAsync(ssml));
}

```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer->VisemeReceived += [](const SpeechSynthesisVisemeEventArgs& e)
{
    cout << "viseme event received. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "viseme id: " << e.VisemeId << "." << endl;
};

auto result = synthesizer->SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.VisemeReceived.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Viseme event received. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("viseme id: " + e.getVisemeId() + ".");
});

SpeechSynthesisResult result = synthesizer.SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

# Subscribes to viseme received event
speech_synthesizer.viseme_received.connect(lambda evt: print(
    "Viseme event received: audio offset: {}ms, viseme id: {}.".format(evt.audio_offset / 10000, evt.viseme_id)))

result = speech_synthesizer.speak_ssml_async(ssml).get()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var synthesizer = new SpeechSDK.SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.visemeReceived = function (s, e) {
    window.console.log("(Viseme), Audio offset: " + e.audioOffset / 10000 + "ms. Viseme ID: " + e.visemeId);
}

synthesizer.speakSsmlAsync(ssml);
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer *synthesizer =
    [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig
                                           audioConfiguration:audioConfig];

// Subscribes to viseme received event
[synthesizer addVisemeReceivedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisVisemeEventArgs *eventArgs) {
    NSLog(@"Viseme event received. Audio offset: %fms, viseme id: %lu.", eventArgs.audioOffset/10000., eventArgs.visemeId);
}];

[synthesizer speakSsml:ssml];
```

::: zone-end

## <a name="map-phonemes-to-visemes"></a>Mapování fonémy na visemes

Visemes se liší podle jazyka. Každý jazyk obsahuje sadu visemes, která odpovídá jeho konkrétnímu fonémy. V následující tabulce je uvedena shoda mezi mezinárodními fonetickými abecedami (IPA) fonémy a identifikátory viseme pro angličtinu (USA).

| IPA | Příklad | ID viseme |
|-----|---------|-----------|
| Mohu   | **EA** t   | 6 |
| ɪ   | **i** f | 6 |
| eɪ  | **te** | 4 |
| ɛ | **e** | 4 |
|æ  |   **ktivní**        |1|
|ɑ  |   **o** bstinate     |2|
|ɔ  |   c **au** se         |3|
|ʊ  |   b **Nún** k          |4|
|oʊ |   **o** ld           |8|
|u  |   **U** ber          |7|
|ʌ  |   **u** ncle         |1|
|aɪ |   **i** CE           |11|
|aʊ |   **organizační jednotka** t           |9|
|ɔɪ |   **Oi** l           |10|
|ju |   **Yu** mA          |[6, 7]|
|ə  |   **a** přejít           |1|
|ɪɹ |   **ušní** prvky          |[6, 13]|
|ɛɹ |   **vzduchová** rovina      |[4, 13]|
|ʊɹ |   c **vaše** e          |[4, 13]|
|aɪ(ə)ɹ |   **Vyžadovat** půda   |[11, 13]|
|aʊ(ə)ɹ |   **hodina** s     |[9, 13]|
|ɔɹ |   **nebo** AD        |[3, 13]|
|ɑɹ |   tist **ar**        |[2, 13]|
|ɝ  |   **ušní** th         |[5; 13]|
|ɚ  |   všechny Gy **ER**       |[1, 13]|
|w  |   **w t**, s **UE** de   |7|
|j  |   **y** ARD, f **e** w     |6|
|p  |   **p** UT           |21|
|b  |   IG **b**           |21|
|t  |   **t** ALK          |19|
|d  |   **d** IG           |19|
|k  |   **c** UT           |20|
|g  |   **g** o            |20|
|m  |   **hmotnost** popel v, s **m m**    |21|
|n  |   **n** o, s **n** oddálení      |19|
|ŋ  |   li **n** k          |20|
|f  |   síť **f**          |18|
|v  |   **v** alue         |18|
|θ  |   **tou** v          |17|
|ð  |   **th** EN          |17|
|s  |   **s** IT           |15|
|z  |   bod **z**           |15|
|ʃ  |   **SH** e           |16|
|ʒ  |   **J** acques       |16|
|h  |   **h** ELP          |12|
|tʃ |   **ch** v          |16|
|dʒ |   **j** Oy           |16|
|l  |   **l** ID, g **l** AD     |14|
|ɹ  |   **r** Ed, b **r** z    |13|


## <a name="next-steps"></a>Další kroky

* [Referenční dokumentace sady Speech SDK](speech-sdk.md)
