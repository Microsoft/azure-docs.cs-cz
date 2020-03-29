---
title: Regiony - Řečová služba
titleSuffix: Azure Cognitive Services
description: Seznam dostupných oblastí a koncových bodů pro službu Řeč, včetně převodu řeči na text, převodu textu na řeč a překladu řeči.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 560575ca7f51218e472abecb4319f4a3db69b1ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220465"
---
# <a name="speech-service-supported-regions"></a>Oblasti podporované službou Speech Service

Služba Řeč umožňuje aplikaci převádět zvuk na text, provádět překlad řeči a skrytý text na řeč. Služba je k dispozici ve více oblastech s jedinečnými koncovými body pro rozhraní API sady Speech SDK a REST.

Portál Řeči k provádění vlastních konfigurací pro vaše prostředí řeči pro všechny oblasti je k dispozici zde:https://speech.microsoft.com

U vyvolání služby Řeč se ujistěte, že volání odpovídá oblasti pro vaše předplatné.

## <a name="speech-sdk"></a>Speech SDK

V [sadě Speech SDK](speech-sdk.md)jsou oblasti určeny jako řetězec `SpeechConfig.FromSubscription` (například jako parametr v sadě Speech SDK pro c#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Převod řeči na text, převod textu na řeč a překlad

Portál pro přizpůsobení řeči je k dispozici zde:https://speech.microsoft.com

Služba Řeč je k dispozici v těchto oblastech pro **rozpoznávání řeči**, **převod textu na řeč**a **překlad**:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Pokud používáte [sadu Speech SDK](speech-sdk.md), oblasti jsou určeny **identifikátorem oblasti** (například jako parametr). `SpeechConfig.FromSubscription` Ujistěte se, že oblast odpovídá oblasti vašeho předplatného.

### <a name="intent-recognition"></a>Rozpoznávání záměru

Dostupné oblasti pro **rozpoznávání záměru** prostřednictvím sady Speech SDK jsou následující:

| Globální oblast | Region (Oblast)           | Identifikátor oblasti |
| ------------- | ---------------- | -------------------- |
| Asie          | Východní Asie        | `eastasia`           |
| Asie          | Jihovýchodní Asie   | `southeastasia`      |
| Austrálie     | Austrálie – východ   | `australiaeast`      |
| Evropa        | Severní Evropa     | `northeurope`        |
| Evropa        | Západní Evropa      | `westeurope`         |
| Severní Amerika | USA – východ          | `eastus`             |
| Severní Amerika | USA – východ 2        | `eastus2`            |
| Severní Amerika | USA – středojih | `southcentralus`     |
| Severní Amerika | USA – středozápad  | `westcentralus`      |
| Severní Amerika | USA – západ          | `westus`             |
| Severní Amerika | USA – západ 2        | `westus2`            |
| Jižní Amerika | Brazílie – jih     | `brazilsouth`        |

Toto je podmnožina oblastí publikování podporovaných [službou Language Understanding (LUIS).](/azure/cognitive-services/luis/luis-reference-regions)

### <a name="voice-assistants"></a>Hlasoví asistenti

Sada [Speech SDK](speech-sdk.md) podporuje funkce **hlasového asistenta** v těchto oblastech:

| Region (Oblast)         | Identifikátor oblasti |
| -------------- | -------------------- |
| USA – západ        | `westus`             |
| USA – západ 2      | `westus2`            |
| USA – východ        | `eastus`             |
| USA – východ 2      | `eastus2`            |
| Západní Evropa    | `westeurope`         |
| Severní Evropa   | `northeurope`        |
| Jihovýchodní Asie | `southeastasia`      |

## <a name="rest-apis"></a>Rozhraní REST API

Služba Řeč také zveřejňuje koncové body REST pro požadavky na řeč a převod textu na řeč.

### <a name="speech-to-text"></a>Převod řeči na text

Referenční dokumentace převodu na text naleznete v [tématu Rozhraní REST API pro převod řeči na text](rest-speech-to-text.md).

Koncový bod pro rozhraní REST API má tento formát:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Nahraďte `<REGION_IDENTIFIER>` identifikátorem, který odpovídá oblasti předplatného z této tabulky:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Parametr jazyka musí být připojen k adrese URL, aby se zabránilo přijetí chyby 4xx HTTP. Například jazyk nastavený na americkou angličtinu pomocí `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`koncového bodu –USA je: .

### <a name="text-to-speech"></a>Převod textu na řeč

Referenční dokumentace pro převod textu na řeč naleznete v [tématu Rozhraní REST API pro převod textu na řeč](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
