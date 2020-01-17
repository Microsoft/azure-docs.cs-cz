---
title: Určení zdrojového jazyka pro převod řeči na text
titleSuffix: Azure Cognitive Services
description: Sada Speech SDK umožňuje určit zdrojový jazyk při převodu řeči na text. Tento článek popisuje, jak pomocí metod FromConfig a SourceLanguageConfig, aby služba rozpoznávání řeči znala zdrojový jazyk a poskytovala vlastní cíl modelu.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: e4f4dd3c1e23855a8a1a69dac72c232779206f1d
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76121705"
---
# <a name="specify-source-language-for-speech-to-text"></a>Určení zdrojového jazyka pro převod řeči na text

V tomto článku se dozvíte, jak určit zdrojový jazyk pro zvukový vstup předaný do sady Speech SDK pro rozpoznávání řeči. Kromě toho je k dispozici vzorový kód pro určení vlastního modelu řeči pro lepší rozpoznávání.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Jak určit zdrojový jazyk vC#

V tomto příkladu je zdrojový jazyk poskytován explicitně jako parametr pomocí `SpeechRecognizer` konstrukce.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

V tomto příkladu je zdrojový jazyk k dispozici pomocí `SourceLanguageConfig`. Pak je `sourceLanguageConfig` předán jako parametr pro `SpeechRecognizer` konstrukce.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

V tomto příkladu jsou k dispozici zdrojový jazyk a vlastní koncový bod pomocí `SourceLanguageConfig`. Pak je `sourceLanguageConfig` předán jako parametr pro `SpeechRecognizer` konstrukce.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> metody `SpeechRecognitionLanguage` a `EndpointId` set jsou zastaralé z třídy `SpeechConfig` v C#. Použití těchto metod se nedoporučuje a nemělo by se používat při sestavování `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Jak určit zdrojový jazyk vC++

V tomto příkladu je zdrojový jazyk poskytován explicitně jako parametr pomocí metody `FromConfig`.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

V tomto příkladu je zdrojový jazyk k dispozici pomocí `SourceLanguageConfig`. Pak se `sourceLanguageConfig` předává jako parametr, který se `FromConfig` při vytváření `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

V tomto příkladu jsou k dispozici zdrojový jazyk a vlastní koncový bod pomocí `SourceLanguageConfig`. `sourceLanguageConfig` se předává jako parametr, který se `FromConfig` při vytváření `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` a `SetEndpointId` jsou zastaralé metody z třídy `SpeechConfig` v C++ jazyce a Java. Použití těchto metod se nedoporučuje a nemělo by se používat při sestavování `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Určení zdrojového jazyka v jazyce Java

V tomto příkladu je zdrojový jazyk k dispozici explicitně při vytváření nového `SpeechRecognizer`.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

V tomto příkladu je zdrojový jazyk k dispozici pomocí `SourceLanguageConfig`. Pak se `sourceLanguageConfig` při vytváření nového `SpeechRecognizer`předává jako parametr.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

V tomto příkladu jsou k dispozici zdrojový jazyk a vlastní koncový bod pomocí `SourceLanguageConfig`. Pak se `sourceLanguageConfig` při vytváření nového `SpeechRecognizer`předává jako parametr.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` a `setEndpointId` jsou zastaralé metody z třídy `SpeechConfig` v C++ jazyce a Java. Použití těchto metod se nedoporučuje a nemělo by se používat při sestavování `SpeechRecognizer`.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Určení zdrojového jazyka v Pythonu

Prvním krokem je vytvoření `speech_config`:

```Python
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

V dalším kroku zadejte zdrojový jazyk vašeho zvuku s `speech_recognition_language`:

```Python
speech_config.speech_recognition_language="de-DE"
```

Pokud používáte vlastní model pro rozpoznávání, můžete zadat koncový bod s `endpoint_id`:

```Python
speech_config.endpoint_id = "The Endpoint ID for your custom model."
```

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Jak určit zdrojový jazyk v JavaScriptu

Prvním krokem je vytvoření `SpeechConfig`:

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

V dalším kroku zadejte zdrojový jazyk vašeho zvuku s `speechRecognitionLanguage`:

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Pokud používáte vlastní model pro rozpoznávání, můžete zadat koncový bod s `endpointId`:

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Určení zdrojového jazyka v cíli – C

Prvním krokem je vytvoření `speechConfig`:

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

V dalším kroku zadejte zdrojový jazyk vašeho zvuku s `speechRecognitionLanguage`:

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

Pokud používáte vlastní model pro rozpoznávání, můžete zadat koncový bod s `endpointId`:

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>Další informace najdete v tématech

* Seznam podporovaných jazyků a národních prostředí pro převod řeči na text najdete v tématu [Podpora jazyků](language-support.md).

## <a name="next-steps"></a>Další kroky

* [Referenční dokumentace sady Speech SDK](speech-sdk.md)
