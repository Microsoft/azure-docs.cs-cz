---
title: Jak používat automatickou detekci jazyka pro řeč na text
titleSuffix: Azure Cognitive Services
description: Sada Speech SDK podporuje automatickou detekci jazyka pro převod řeči na text. Při použití této funkce je poskytnutý zvuk porovnán s poskytnutým seznamem jazyků a je určena nejpravděpodobnější shoda. Vrácenou hodnotu lze poté použít k výběru jazykového modelu používaného pro převod řeči na text.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 5592fc3e50db892c6abb09fc2516b8e1c03f0f03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239608"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Automatická detekce jazyka pro převod řeči na text

Automatická detekce jazyka se používá k určení nejpravděpodobnější shody zvuku předávaného sady Speech SDK ve srovnání se seznamem poskytnutých jazyků. Hodnota vrácená automatickou detekcí jazyka se pak používá k výběru jazykového modelu pro převod řeči na text, což vám poskytuje přesnější přepis. Informace o tom, které jazyky jsou k dispozici, naleznete [v tématu Jazyková podpora](language-support.md).

V tomto článku se dozvíte, `AutoDetectSourceLanguageConfig` jak `SpeechRecognizer` použít k vytvoření objektu a načtení zjištěného jazyka.

> [!IMPORTANT]
> Tato funkce je k dispozici pouze pro sadu Speech SDK pro C#, C++, Java a Python.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Automatická detekce jazyka pomocí sady Speech SDK

Automatická detekce jazyka má v současné době limit na straně služeb dva jazyky na detekci. Mějte toto omezení `AudoDetectSourceLanguageConfig` na paměti při konstrukci objektu. V následujících ukázkách vytvoříte `AutoDetectSourceLanguageConfig`a pak ji `SpeechRecognizer`použijete k vytvoření .

> [!TIP]
> Můžete také určit vlastní model, který se má použít při provádění řeči na text. Další informace naleznete [v tématu Použití vlastního modelu pro automatické zjišťování jazyka](#use-a-custom-model-for-automatic-language-detection).

Následující úryvky ilustrují, jak používat automatickou detekci jazyka ve vašich aplikacích:

::: zone pivot="programming-language-csharp"

```csharp
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE" });

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult =
        AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
auto_detect_source_language_config = \
        speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["en-US", "de-DE"])
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, 
        auto_detect_source_language_config=auto_detect_source_language_config, 
        audio_config=audio_config)
result = speech_recognizer.recognize_once()
auto_detect_source_language_result = speechsdk.AutoDetectSourceLanguageResult(result)
detected_language = auto_detect_source_language_result.language
```

::: zone-end

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Použití vlastního modelu pro automatickou detekci jazyka

Kromě detekce jazyka pomocí modelů služby Rozpoznávání řeči můžete zadat vlastní model pro rozšířené rozpoznávání. Pokud vlastní model není k dispozici, služba bude používat výchozí jazykový model.

Výstřižky níže ilustrují, jak zadat vlastní model ve volání služby Řeči. Pokud je `en-US`zjištěný jazyk , použije se výchozí model. Pokud je `fr-FR`zjištěný jazyk , použije se koncový bod pro vlastní model:

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
 en_language_config = speechsdk.languageconfig.SourceLanguageConfig("en-US")
 fr_language_config = speechsdk.languageconfig.SourceLanguageConfig("fr-FR", "The Endpoint Id for custom model of fr-FR")
 auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(
        sourceLanguageConfigs=[en_language_config, fr_language_config])
```

::: zone-end

## <a name="next-steps"></a>Další kroky

- [Referenční dokumentace sady Speech SDK](speech-sdk.md)
