---
title: Jak určit zdrojový jazyk pro řeč na text
titleSuffix: Azure Cognitive Services
description: Sada Speech SDK umožňuje určit zdrojový jazyk při převodu řeči na text. Tento článek popisuje, jak používat FromConfig a SourceLanguageConfig metody, aby služba Speech znát zdrojový jazyk a poskytnout vlastní cíl modelu.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f0723534d9d2187593cb73f058ffea62473b80a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235972"
---
# <a name="specify-source-language-for-speech-to-text"></a>Určení zdrojového jazyka pro převod řeči na text

V tomto článku se dozvíte, jak určit zdrojový jazyk pro zvukový vstup předaný sadě Speech SDK pro rozpoznávání řeči. Kromě toho příklad kódu je k dispozici k určení vlastního modelu řeči pro lepší rozpoznávání.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Jak zadat zdrojový jazyk v Jazyce C #

V tomto příkladu je zdrojový jazyk k `SpeechRecognizer` dispozici explicitně jako parametr pomocí konstrukce.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

V tomto příkladu zdrojový jazyk `SourceLanguageConfig`je k dispozici pomocí . Potom `sourceLanguageConfig` je předán jako parametr `SpeechRecognizer` k vytvoření.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

V tomto příkladu zdrojový jazyk a vlastní `SourceLanguageConfig`koncový bod jsou k dispozici pomocí . Potom `sourceLanguageConfig` je předán jako parametr `SpeechRecognizer` k vytvoření.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage`a `EndpointId` metody set jsou zastaralé `SpeechConfig` z třídy v C#. Použití těchto metod se nedoporučuje a neměly by být `SpeechRecognizer`použity při vytváření .

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Jak zadat zdrojový jazyk v jazyce C++

V tomto příkladu je zdrojový jazyk k dispozici `FromConfig` explicitně jako parametr pomocí metody.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

V tomto příkladu zdrojový jazyk `SourceLanguageConfig`je k dispozici pomocí . Potom `sourceLanguageConfig` je předán jako parametr `FromConfig` při `recognizer`vytváření .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

V tomto příkladu zdrojový jazyk a vlastní `SourceLanguageConfig`koncový bod jsou k dispozici pomocí . Je `sourceLanguageConfig` předán jako parametr `FromConfig` při `recognizer`vytváření .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage`a `SetEndpointId` jsou zastaralé metody z `SpeechConfig` třídy v jazyce C++ a Java. Použití těchto metod se nedoporučuje a neměly by být `SpeechRecognizer`použity při vytváření .

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Jak zadat zdrojový jazyk v Jazyce Java

V tomto příkladu je zdrojový jazyk k `SpeechRecognizer`dispozici explicitně při vytváření nového .

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

V tomto příkladu zdrojový jazyk `SourceLanguageConfig`je k dispozici pomocí . Potom `sourceLanguageConfig` je předán jako parametr při `SpeechRecognizer`vytváření nového .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

V tomto příkladu zdrojový jazyk a vlastní `SourceLanguageConfig`koncový bod jsou k dispozici pomocí . Potom `sourceLanguageConfig` je předán jako parametr při `SpeechRecognizer`vytváření nového .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage`a `setEndpointId` jsou zastaralé metody z `SpeechConfig` třídy v jazyce C++ a Java. Použití těchto metod se nedoporučuje a neměly by být `SpeechRecognizer`použity při vytváření .

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Jak zadat zdrojový jazyk v Pythonu

V tomto příkladu je zdrojový jazyk k `SpeechRecognizer` dispozici explicitně jako parametr pomocí konstrukce.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

V tomto příkladu zdrojový jazyk `SourceLanguageConfig`je k dispozici pomocí . Potom `SourceLanguageConfig` je předán jako parametr `SpeechRecognizer` k vytvoření.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

V tomto příkladu zdrojový jazyk a vlastní `SourceLanguageConfig`koncový bod jsou k dispozici pomocí . Potom `SourceLanguageConfig` je předán jako parametr `SpeechRecognizer` k vytvoření.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language`a `endpoint_id` vlastnosti jsou zastaralé `SpeechConfig` z třídy v Pythonu. Použití těchto vlastností se nedoporučuje a neměly by být `SpeechRecognizer`použity při vytváření .

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Jak zadat zdrojový jazyk v Javascriptu

Prvním krokem je vytvoření `SpeechConfig`:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Dále zadejte zdrojový jazyk zvuku `speechRecognitionLanguage`pomocí aplikace :

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Pokud používáte vlastní model pro rozpoznávání, můžete zadat `endpointId`koncový bod s :

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Jak zadat zdrojový jazyk v Objective-C

Prvním krokem je vytvoření `speechConfig`:

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

Dále zadejte zdrojový jazyk zvuku `speechRecognitionLanguage`pomocí aplikace :

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Pokud používáte vlastní model pro rozpoznávání, můžete zadat `endpointId`koncový bod s :

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>Viz také

* Seznam podporovaných jazyků a národních prostředí pro převod řeči na text naleznete v [tématu Jazyková podpora](language-support.md).

## <a name="next-steps"></a>Další kroky

* [Referenční dokumentace sady Speech SDK](speech-sdk.md)
