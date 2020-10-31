---
title: Jak používat sadu Speech SDK pro vyhodnocení výslovnosti
titleSuffix: Azure Cognitive Services
description: Sada Speech SDK podporuje vyhodnocení výslovnosti, které posuzuje kvalitu výslovnosti vstupu řeči s indikátory přesnosti, Fluency, úplností atd.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-set-nineteen
ms.openlocfilehash: 245a00acb07d1c0e769a243413fccdf64d544f5a
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133574"
---
# <a name="pronunciation-assessment"></a>Posouzení výslovnosti

Posouzení výslovnosti vyhodnocuje výslovnost řeči a dává mluvčí názory na přesnost a Fluency mluveného zvuku.
S hodnocením výslovnosti mohou jazyky naučit postupovat, získávat okamžitou zpětnou vazbu a zlepšovat jejich výslovnost, aby mohli mluvit a prezentovat s jistotou.
Pedagogé můžou využít schopnost vyhodnotit výslovnost více mluvčích v reálném čase.

V tomto článku se dozvíte, jak nastavit `PronunciationAssessmentConfig` a načíst `PronunciationAssessmentResult` pomocí sady Speech SDK.

> [!NOTE]
> Funkce hodnocení výslovnosti je aktuálně dostupná jenom v oblastech `westus` `eastasia` a `centralindia` podporuje jenom jazyk `en-US` .

## <a name="pronunciation-assessment-with-the-speech-sdk"></a>Posouzení výslovnosti pomocí sady Speech SDK

V níže uvedených ukázkách vytvoříte a `PronunciationAssessmentConfig` pak použijete na `SpeechRecognizer` .

Následující fragmenty kódu ukazují, jak používat automatické rozpoznávání jazyka ve vašich aplikacích:

::: zone pivot="programming-language-csharp"

```csharp
var pronunciationAssessmentConfig = new PronunciationAssessmentConfig(
    "reference text", GradingSystem.HundredMark, Granularity.Phoneme);

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig))
{
    // apply the pronunciation assessment configuration to the speech recognizer
    pronunciationAssessmentConfig.ApplyTo(recognizer);
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var pronunciationAssessmentResult =
        PronunciationAssessmentResult.FromResult(speechRecognitionResult);
    var pronunciationScore = pronunciationAssessmentResult.PronunciationScore;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig =
    PronunciationAssessmentConfig::Create("reference text",
        PronunciationAssessmentGradingSystem::HundredMark,
        PronunciationAssessmentGranularity::Phoneme);

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig->ApplyTo(recognizer);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto pronunciationAssessmentResult =
    PronunciationAssessmentResult::FromResult(speechRecognitionResult);
auto pronunciationScore = pronunciationAssessmentResult->PronunciationScore;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
PronunciationAssessmentConfig pronunciationAssessmentConfig =
    new PronunciationAssessmentConfig("reference text", 
        PronunciationAssessmentGradingSystem.HundredMark,
        PronunciationAssessmentGranularity.Phoneme);

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(recognizer);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
PronunciationAssessmentResult pronunciationAssessmentResult =
    PronunciationAssessmentResult.fromResult(result);
Double pronunciationScore = pronunciationAssessmentResult.getPronunciationScore();

recognizer.close();
speechConfig.close();
audioConfig.close();
pronunciationAssessmentConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = \
        speechsdk.PronunciationAssessmentConfig(reference_text='reference text',
                grading_system=msspeech.PronunciationAssessmentGradingSystem.HundredMark,
                granularity=msspeech.PronunciationAssessmentGranularity.Phoneme)
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, \
        audio_config=audio_config)

# apply the pronunciation assessment configuration to the speech recognizer
pronunciation_assessment_config.apply_to(speech_recognizer)
result = speech_recognizer.recognize_once()
pronunciation_assessment_result = speechsdk.PronunciationAssessmentResult(result)
pronunciation_score = pronunciation_assessment_result.pronunciation_score
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXPronunciationAssessmentConfiguration* pronunciationAssessmentConfig =
    [[SPXPronunciationAssessmentConfiguration alloc]init:@"reference text"
                                           gradingSystem:SPXPronunciationAssessmentGradingSystem_HundredMark
                                             granularity:SPXPronunciationAssessmentGranularity_Phoneme];

SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                              audioConfiguration:audioConfig];

// apply the pronunciation assessment configuration to the speech recognizer
[pronunciationAssessmentConfig applyToRecognizer:speechRecognizer];

SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXPronunciationAssessmentResult* pronunciationAssessmentResult = [[SPXPronunciationAssessmentResult alloc] init:result];
double pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
```

::: zone-end

### <a name="pronunciation-assessment-configuration-parameters"></a>Parametry konfigurace vyhodnocení výslovnosti

Tato tabulka uvádí parametry konfigurace pro posouzení výslovnosti.

| Parametr | Popis | Požadováno/volitelné |
|-----------|-------------|---------------------|
| ReferenceText | Text, proti kterému bude výslovnost vyhodnocena. | Vyžadováno |
| GradingSystem | Systém bodů pro kalibraci skóre. Přijaté hodnoty jsou `FivePoint` a `HundredMark` . Výchozí hodnota je `FivePoint`. | Volitelné |
| Členitost | Členitost vyhodnocení. Přípustné hodnoty jsou, což zobrazuje skóre pro úplný text, na úrovni aplikace Word a foném, ve kterém se zobrazuje skóre `Phoneme` `Word` pro úplný text a na úrovni slova, ve `FullText` kterém se zobrazuje skóre pouze na úrovni celého textu. Výchozí hodnota je `Phoneme`. | Volitelné |
| EnableMiscue | Povolí výpočet miscue. Když je tato možnost povolená, vyslovované slova se porovnají s referenčním textem a budou označená vynechání nebo vložení na základě porovnání. Přijaté hodnoty jsou `False` a `True` . Výchozí hodnota je `False`. | Volitelné |
| ScenarioId | Identifikátor GUID označující systém přizpůsobeného bodu. | Volitelné |

### <a name="pronunciation-assessment-result-parameters"></a>Parametry výsledku vyhodnocení výslovnosti

Tato tabulka obsahuje seznam výsledných parametrů pro vyhodnocení výslovnosti.

| Parametr | Popis |
|-----------|-------------|
| `AccuracyScore` | Přesnost řeči v výslovnosti. Přesnost určuje, jak úzce se fonémy shoduje s výslovností nativního mluvčího. Skóre přesnosti aplikace Word a úplného textu je agregované ze foném skóre úrovně. |
| `FluencyScore` | Fluency zadaného řeči. Fluency označuje, jak blízkoně řeč odpovídá použití tichého zalomení mezi slovy v nativním mluvčím. |
| `CompletenessScore` | Úplnost rozpoznávání řeči určené výpočtem poměru slov, která jsou vyhodnocena jako odkaz na textové zadání. |
| `PronunciationScore` | Celkové skóre označující kvalitu výslovnosti daného řeči. To je agregované z `AccuracyScore` `FluencyScore` a `CompletenessScore` s váhou. |
| `ErrorType` | Tato hodnota označuje, zda je slovo vynecháno, vloženo nebo špatně vyslovované, ve srovnání s `ReferenceText` . Možné hodnoty jsou `None` (to znamená, že toto slovo neobsahuje žádnou chybu), `Omission` `Insertion` a `Mispronunciation` . |

## <a name="next-steps"></a>Další kroky

<!-- TODO: update the sample links after release -->

<!-- ::: zone pivot="programming-language-csharp"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L741) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-cpp"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L507) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-java"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L521) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-python"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_synthesis_sample.py#L434) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-objectivec"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L494) on GitHub for automatic language detection
::: zone-end -->

* [Referenční dokumentace sady Speech SDK](speech-sdk.md)
