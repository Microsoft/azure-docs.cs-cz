---
title: Jak sledovat využití paměti sady Speech SDK – služba Řeč
titleSuffix: Azure Cognitive Services
description: Sada Speech Service SDK podporuje mnoho programovacích jazyků pro převod řeči na text a převod textu na řeč spolu s překladem řeči. Tento článek popisuje nástroje pro správu paměti integrované do sady SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: da5103317a2215aca68cec14ba8a0951258c9b89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456429"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Jak sledovat využití paměti sady Speech SDK

Sada Speech SDK je založena na nativním základu kódu, který je promítán do více programovacích jazyků prostřednictvím řady vrstev interoperability. Každá projekce specifická pro daný jazyk má idiomaticky správné funkce pro správu životního cyklu objektu. Sada Speech SDK navíc obsahuje nástroje pro správu paměti ke sledování využití prostředků pomocí protokolování objektů a omezení objektů. 

## <a name="how-to-read-object-logs"></a>Čtení protokolů objektů

Pokud [je povoleno protokolování sady Speech SDK](how-to-use-logging.md), jsou vyzařovány sledovací značky, které umožňují pozorování historických objektů. Mezi tyto značky patří: 

* `TrackHandle` nebo `StopTracking` 
* Typ objektu
* Aktuální počet objektů, které jsou sledovány typ objektu a aktuální číslo je sledováno.

Zde je ukázkový protokol: 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Nastavení prahové hodnoty pro upozornění

Máte možnost vytvořit prahovou hodnotu upozornění a pokud je tato prahová hodnota překročena (za předpokladu, že protokolování je povoleno), je zaznamenána varovná zpráva. Varovná zpráva obsahuje výpis všech objektů, které existují spolu s jejich počet. Tyto informace lze použít k lepšímu pochopení problémů. 

Chcete-li povolit prahovou hodnotu `SpeechConfig` upozornění, musí být zadána na objektu. Tento objekt je kontrolován při vytvoření nového nástroje pro rozpoznávání. V následujících příkladech předpokládejme, že jste vytvořili `SpeechConfig` `config`instanci s názvem :

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> Výchozí hodnota pro tuto vlastnost je 10 000.

## <a name="set-an-error-threshold"></a>Nastavení prahové hodnoty chyby 

Pomocí sady Speech SDK můžete nastavit maximální počet objektů povolených v daném čase. Pokud je toto nastavení povoleno, při dosažení maximálního počtu se pokusy o vytvoření nových objektů pro rozpoznávání nezdaří. Existující objekty budou nadále fungovat.

Zde je ukázková chyba:

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

Chcete-li povolit prahovou hodnotu `SpeechConfig` chyby, musí být zadána na objektu. Tento objekt je kontrolován při vytvoření nového nástroje pro rozpoznávání. V následujících příkladech předpokládejme, že jste vytvořili `SpeechConfig` `config`instanci s názvem :

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> Výchozí hodnota pro tuto vlastnost je maximální hodnota `size_t` pro datový typ specifická pro platformu. Typické rozpoznávání bude spotřebovávat mezi 7 a 10 vnitřní objekty.

## <a name="next-steps"></a>Další kroky

* [Získejte zkušební předplatné služby Speech Service](get-started.md)
* [Informace o rozpoznání řeči pomocí mikrofonu](quickstarts/speech-to-text-from-microphone.md)