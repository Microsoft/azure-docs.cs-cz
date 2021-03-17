---
title: Cloudy svrchované – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Naučte se používat cloudy v svrchovaném provozu.
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.custom: references_regions
ms.date: 01/07/2021
ms.author: alexeyo
ms.openlocfilehash: f30b1f0f14bba54b8b4fcd7c5190f3c533f199a6
ms.sourcegitcommit: 63caac7871df9d999ca5a5f1b8c036ae7014231c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98061748"
---
# <a name="speech-services-in-sovereign-clouds"></a>Služby Speech v cloudech svrchovan

## <a name="azure-government-united-states"></a>Azure Government (USA)

K dispozici pouze institucím státní správy USA a jejich partnerům. Další informace o Azure Government najdete [tady](../../azure-government/documentation-government-welcome.md) [.](../../azure-government/compare-azure-government-global-azure.md)

- **Azure Portal:**
  - [https://portal.azure.us/](https://portal.azure.us/)
- **Regionu**
  - USA (Gov) – Arizona
  - USA (Gov) – Virginia
- **Dostupné cenové úrovně:**
  - Free (F0) a Standard (S0). Další podrobnosti najdete [tady](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .
- **Podporované funkce:**
  - Převod řeči na text
    - Vlastní převod řeči (akustický Model AM) a přizpůsobení jazykového modelu (LM)
      - [Speech Studio](https://speech.azure.us/)
  - Převod textu na řeč
    - Standardní hlas
    - Neuronové hlas
  - Překladatel řeči
- **Nepodporované funkce:**
  - Vlastní hlas
- **Podporované jazyky:**
  - [Tady](language-support.md) najdete seznam podporovaných jazyků.

### <a name="endpoint-information"></a>Informace o koncovém bodu

Tato část obsahuje informace o koncovém bodu služby Speech Services pro použití s [funkcí Speech SDK](speech-sdk.md), [Speech-to-text REST API](rest-speech-to-text.md)a [Převod textu na řeč REST API](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>REST API služby Speech

Služba Speech Services REST API koncové body v Azure Government mají následující formát:

|  Typ/operace REST API | Formát koncového bodu |
|--|--|
| Přístupový token | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/sts/v1.0/issueToken`
| [Převod řeči na text REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/<URL_PATH>` |
| [REST API řeči na text pro krátký zvuk](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.us/<URL_PATH>` |
| [Rozhraní REST API pro převod textu na řeč](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.us/<URL_PATH>` |

Nahraďte `<REGION_IDENTIFIER>` identifikátorem, který odpovídá oblasti vašeho předplatného z této tabulky:

|                     | Identifikátor oblasti |
|--|--|
| **USA (Gov) – Arizona**  | `usgovarizona` |
| **USA (Gov) – Virginia** | `usgovvirginia` |

#### <a name="speech-sdk"></a>Speech SDK

Pro sadu Speech SDK v cloudech z svrchovaného systému je potřeba použít instanci z hostitele `SpeechConfig` třídy nebo `--host` Možnosti rozhraní příkazového [řádku pro rozpoznávání řeči](spx-overview.md). (Můžete také použít instanci "z koncového bodu" a `--endpoint` Speech CLI – možnost

`SpeechConfig` pro třídu by měla být vytvořena instance takto:
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
Mělo by se používat rozpoznávání řeči (Poznamenejte si `--host` možnost):
```dos
spx recognize --host "usGovHost" --file myaudio.wav
```
Nahraďte `subscriptionKey` klíčem prostředku řeči. Nahraďte `usGovHost` výrazem, který odpovídá požadované nabídce služeb a oblasti vašeho předplatného z této tabulky:

|  Nabídka oblast/služba | Výraz hostitele |
|--|--|
| **USA (Gov) – Arizona** | |
| Převod řeči na text | `wss://usgovarizona.stt.speech.azure.us` |
| Převod textu na řeč | `https://usgovarizona.tts.speech.azure.us` |
| **USA (Gov) – Virginia** | |
| Převod řeči na text | `wss://usgovvirginia.stt.speech.azure.us` |
| Převod textu na řeč | `https://usgovvirginia.tts.speech.azure.us` |


## <a name="azure-china"></a>Azure (Čína)

K dispozici organizacím, které mají podnikovou přítomnost v Číně. Další informace o Azure Číně najdete [tady.](/azure/china/overview-operations) 


- **Azure Portal:**
  - [https://portal.azure.cn/](https://portal.azure.cn/)
- **Regionu**
  - Čína – východ 2
- **Dostupné cenové úrovně:**
  - Free (F0) a Standard (S0). Další podrobnosti najdete [tady](https://www.azure.cn/pricing/details/cognitive-services/index.html) .
- **Podporované funkce:**
  - Převod řeči na text
    - Vlastní převod řeči (akustický Model AM) a přizpůsobení jazykového modelu (LM)
      - [Speech Studio](https://speech.azure.cn/)
  - Převod textu na řeč
    - Standardní hlas
    - Neuronové hlas
  - Překladatel řeči
- **Nepodporované funkce:**
  - Vlastní hlas
- **Podporované jazyky:**
  - [Tady](language-support.md) najdete seznam podporovaných jazyků.

### <a name="endpoint-information"></a>Informace o koncovém bodu

Tato část obsahuje informace o koncovém bodu služby Speech Services pro použití s [funkcí Speech SDK](speech-sdk.md), [Speech-to-text REST API](rest-speech-to-text.md)a [Převod textu na řeč REST API](rest-text-to-speech.md).

#### <a name="speech-services-rest-api"></a>REST API služby Speech

Služby Speech Services REST API koncové body v Azure Čína mají následující formát:

|  Typ/operace REST API | Formát koncového bodu |
|--|--|
| Přístupový token | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/sts/v1.0/issueToken`
| [Převod řeči na text REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/<URL_PATH>` |
| [REST API řeči na text pro krátký zvuk](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.cn/<URL_PATH>` |
| [Rozhraní REST API pro převod textu na řeč](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.cn/<URL_PATH>` |

Nahraďte `<REGION_IDENTIFIER>` identifikátorem, který odpovídá oblasti vašeho předplatného z této tabulky:

|                     | Identifikátor oblasti |
|--|--|
| **Čína – východ 2**  | `chinaeast2` |

#### <a name="speech-sdk"></a>Speech SDK

Pro sadu Speech SDK v cloudech z svrchovaného systému je potřeba použít instanci z hostitele `SpeechConfig` třídy nebo `--host` Možnosti rozhraní příkazového [řádku pro rozpoznávání řeči](spx-overview.md). (Můžete také použít instanci "z koncového bodu" a `--endpoint` Speech CLI – možnost

`SpeechConfig` pro třídu by měla být vytvořena instance takto:
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
Mělo by se používat rozpoznávání řeči (Poznamenejte si `--host` možnost):
```dos
spx recognize --host "azCnHost" --file myaudio.wav
```
Nahraďte `subscriptionKey` klíčem prostředku řeči. Nahraďte `azCnHost` výrazem, který odpovídá požadované nabídce služeb a oblasti vašeho předplatného z této tabulky:

|  Nabídka oblast/služba | Výraz hostitele |
|--|--|
| **Čína – východ 2** | |
| Převod řeči na text | `wss://chinaeast2.stt.speech.azure.cn` |
| Převod textu na řeč | `https://chinaeast2.tts.speech.azure.cn` |