---
title: Jak používat automatické zjišování jazyka pro rozpoznávání řeči na text
titleSuffix: Azure Cognitive Services
description: Sada Speech SDK podporuje automatické rozpoznávání jazyka pro rozpoznávání řeči na text. Při použití této funkce se zadaný zvuk porovná se zadaným seznamem jazyků a je určena nejpravděpodobnější shoda. Vrácená hodnota se pak může použít k výběru jazykového modelu použitého pro převod řeči na text.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-seven
ms.openlocfilehash: bc438c3e606fefc10e9ffbb64c79f7167d9af062
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122045"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Automatické zjišování jazyka pro rozpoznávání řeči na text

Automatická detekce jazyka se používá k určení nejpravděpodobnější shody pro zvuk předaný do sady Speech SDK při porovnání se seznamem poskytovaných jazyků. Hodnota vrácená automatickým rozpoznáním jazyka se pak použije k výběru jazykového modelu pro rozpoznávání řeči textu a poskytuje vám přesnější přepis. Pokud chcete zjistit, které jazyky jsou k dispozici, přečtěte si téma [Podpora jazyků](language-support.md).

V tomto článku se naučíte, jak pomocí `AutoDetectSourceLanguageConfig` vytvořit objekt `SpeechRecognizer` a načíst zjištěný jazyk.

> [!IMPORTANT]
> Tato funkce je dostupná jenom pro sadu Speech SDK pro C# C++ a Java.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Automatické rozpoznávání jazyka pomocí sady Speech SDK

Automatické rozpoznání jazyka aktuálně má limit na straně služeb pro každé zjištění. Pamatujte na toto omezení při vytváření objektu `AudoDetectSourceLanguageConfig`. V níže uvedených ukázkách vytvoříte `AutoDetectSourceLanguageConfig`a pak ho použijete k vytvoření `SpeechRecognizer`.

> [!TIP]
> Můžete také zadat vlastní model, který se použije při provádění řeči na text. Další informace najdete v tématu [použití vlastního modelu pro automatické rozpoznávání jazyka](#use-a-custom-model-for-automatic-language-detection).

Následující fragmenty kódu ukazují, jak používat automatické rozpoznávání jazyka ve vašich aplikacích:

::: zone pivot="programming-language-csharp"

```csharp
var autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.FromLanguages(new string[] { "en-US", "de-DE" });
using (var recognizer = new SpeechRecognizer(speechConfig, autoDetectSourceLanguageConfig, audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Použití vlastního modelu pro automatické zjišování jazyka

Kromě rozpoznávání jazyka pomocí modelů služby Speech můžete pro lepší rozpoznávání zadat vlastní model. Pokud není k dispozici vlastní model, služba použije výchozí jazykový model.

Níže uvedené fragmenty kódu ilustrují, jak zadat vlastní model ve volání služby pro rozpoznávání řeči. Pokud se zjištěný jazyk `en-US`, použije se výchozí model. Pokud se zjištěný jazyk `fr-FR`, použije se koncový bod pro vlastní model:

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(sourceLanguageConfigs);
```

::: zone-end

## <a name="next-steps"></a>Další kroky

- [Referenční dokumentace sady Speech SDK](speech-sdk.md)
