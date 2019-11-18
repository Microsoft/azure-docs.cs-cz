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
ms.date: 10/26/2019
ms.author: qiohu
ms.openlocfilehash: a72f477e64c856c545801533c131c397de627c00
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74110174"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Automatické zjišování jazyka pro rozpoznávání řeči na text

Automatická detekce jazyka se používá k určení nejpravděpodobnější shody pro zvuk předaný do sady Speech SDK při porovnání se seznamem poskytovaných jazyků. Hodnota vrácená automatickým rozpoznáním jazyka se pak použije k výběru jazykového modelu pro rozpoznávání řeči textu a poskytuje vám přesnější přepis. Pokud chcete zjistit, které jazyky jsou k dispozici, přečtěte si téma [Podpora jazyků](language-support.md).

V tomto článku se naučíte, jak pomocí `AutoDetectSourceLanguageConfig` vytvořit objekt `SpeechRecognizer` a načíst zjištěný jazyk.

> [!IMPORTANT]
> Tato funkce je k dispozici pouze pro sadu Speech C++ SDK pro a sadu Speech SDK for Java.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Automatické rozpoznávání jazyka pomocí sady Speech SDK

Automatické rozpoznání jazyka aktuálně má limit na straně služeb pro každé zjištění. Pamatujte na toto omezení při vytváření objektu `AudoDetectSourceLanguageConfig`. V níže uvedených ukázkách vytvoříte `AutoDetectSourceLanguageConfig`a pak ho použijete k vytvoření `SpeechRecognizer`.

> [!TIP]
> Můžete také zadat vlastní model, který se použije při provádění řeči na text. Další informace najdete v tématu [použití vlastního modelu pro automatické rozpoznávání jazyka](#use-a-custom-model-for-automatic-language-detection).

Následující fragmenty kódu ukazují, jak používat automatické rozpoznávání jazyka ve vašich aplikacích:

```C++
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, autoDetectSourceLanguageConfig, audioConfig);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult = AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

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

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Použití vlastního modelu pro automatické zjišování jazyka

Kromě rozpoznávání jazyka pomocí modelů služby Speech můžete pro lepší rozpoznávání zadat vlastní model. Pokud není k dispozici vlastní model, služba použije výchozí jazykový model.

Níže uvedené fragmenty kódu ilustrují, jak zadat vlastní model ve volání služby pro rozpoznávání řeči. Pokud se zjištěný jazyk `en-US`, použije se výchozí model. Pokud se zjištěný jazyk `fr-FR`, použije se koncový bod pro vlastní model:

```C++
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
auto autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(sourceLanguageConfigs);
```

```Java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig = AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(sourceLanguageConfigs);
```

## <a name="next-steps"></a>Další kroky

- [Referenční dokumentace sady Speech SDK](speech-sdk.md)
