---
title: Jak sledovat využití paměti sady Speech SDK – Speech Service
titleSuffix: Azure Cognitive Services
description: Sada Speech Service SDK podporuje řadu programovacích jazyků pro konverzi řeči na text a převod textu na řeč spolu s překladem řeči. Tento článek popisuje nástroje pro správu paměti integrované do sady SDK.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "75456429"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Postup sledování využití paměti sady Speech SDK

Sada Speech SDK je založená na nativním základu kódu, který je provedený na více programovacích jazyků prostřednictvím řady vrstev interoperability. Každá projekce konkrétního jazyka má idiomatically správné funkce pro správu životního cyklu objektu. Kromě toho sada Speech SDK zahrnuje nástroje pro správu paměti, které sledují využití prostředků pomocí omezení objektů a protokolování. 

## <a name="how-to-read-object-logs"></a>Postup čtení protokolů objektů

Pokud [je povoleno protokolování sady Speech SDK](how-to-use-logging.md), jsou vygenerovány sledovací značky, aby bylo možné sledovat historické objekty. Mezi tyto značky patří: 

* `TrackHandle` nebo `StopTracking` 
* Typ objektu
* Aktuální počet objektů, které jsou sledovány typem objektu a aktuální číslo, které je sledováno.

Tady je ukázkový protokol: 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Nastavit prahovou hodnotu pro upozornění

Máte možnost vytvořit prahovou hodnotu pro upozornění, a pokud dojde k překročení této prahové hodnoty (za předpokladu, že je povolené protokolování), zaprotokoluje se zpráva s upozorněním. Zpráva upozornění obsahuje výpis všech objektů, které existují, spolu s jejich počtem. Tyto informace se dají použít k lepšímu pochopení problémů. 

Chcete-li povolit prahovou hodnotu pro upozornění, je nutné `SpeechConfig` ji zadat u objektu. Tento objekt je zkontrolován při vytvoření nového nástroje pro rozpoznávání. V následujících příkladech Předpokládejme, že jste vytvořili instanci s `SpeechConfig` názvem: `config`

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
> Výchozí hodnota této vlastnosti je 10 000.

## <a name="set-an-error-threshold"></a>Nastavit prahovou hodnotu chyby 

Pomocí sady Speech SDK můžete nastavit maximální počet objektů povolených v daném okamžiku. Pokud je toto nastavení povoleno, pokud je dosaženo maximálního počtu, pokusy o vytvoření nových objektů pro rozpoznávání nebudou úspěšné. Stávající objekty budou fungovat i nadále.

Tady je ukázková Chyba:

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

Chcete-li povolit prahovou hodnotu chyby, je nutné ji `SpeechConfig` zadat u objektu. Tento objekt je zkontrolován při vytvoření nového nástroje pro rozpoznávání. V následujících příkladech Předpokládejme, že jste vytvořili instanci s `SpeechConfig` názvem: `config`

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
> Výchozí hodnota této vlastnosti je maximální hodnota specifická pro danou platformu pro `size_t` datový typ. Typické rozpoznávání bude využívat 7 až 10 interních objektů.

## <a name="next-steps"></a>Další kroky

* [Získání zkušebního předplatného služby Speech](get-started.md)
* [Naučte se rozpoznávat řeč pomocí mikrofonu.](quickstarts/speech-to-text-from-microphone.md)