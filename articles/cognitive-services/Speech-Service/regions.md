---
title: Oblasti – služba řeči
titleSuffix: Azure Cognitive Services
description: Seznam dostupných oblastí a koncových bodů pro službu rozpoznávání řeči, včetně převodu řeči na text, převod textu na řeč a překladu řeči.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.custom: seodec18,references_regions
ms.openlocfilehash: 646d29e72b91cd6afcde8e70ad8fd8715442b88e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98786787"
---
# <a name="speech-service-supported-regions"></a>Oblasti podporované službou Speech

Služba rozpoznávání řeči umožňuje aplikaci převést zvuk na text, provést překlad řeči a převést text na řeč. Služba je dostupná ve více oblastech s jedinečnými koncovými body pro sadu Speech SDK a rozhraní REST API.

Portál pro rozpoznávání řeči pro vlastní konfigurace prostředí Speech pro všechny oblasti je k dispozici zde: https://speech.microsoft.com

Při zvažování oblastí Pamatujte na tyto body:

* Pokud vaše aplikace používá [sadu Speech SDK](speech-sdk.md), při vytváření konfigurace řeči zadáte identifikátor oblasti, například `westus` .
* Pokud vaše aplikace používá jedno z [rozhraní REST API](./overview.md#reference-docs)služby pro rozpoznávání řeči, je tato oblast součástí identifikátoru URI koncového bodu, který používáte při provádění požadavků.
* Klíče vytvořené pro oblast jsou platné pouze v této oblasti. Při pokusu o jejich použití s ostatními oblastmi dojde k chybám ověřování.

## <a name="speech-sdk"></a>Speech SDK

V [sadě Speech SDK](speech-sdk.md)jsou oblasti určeny jako řetězec (například jako parametr `SpeechConfig.FromSubscription` v sadě Speech SDK pro jazyk C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Převod řeči na text, převod textu na řeč a překlad

Portál pro přizpůsobení řeči je k dispozici zde: https://speech.microsoft.com

Služba Speech je v těchto oblastech dostupná pro **rozpoznávání řeči**, převod **textu na řeč** a **překlady**:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

Použijete-li [sadu Speech SDK](speech-sdk.md), oblasti jsou určeny **identifikátorem oblasti** (například jako parametr `SpeechConfig.FromSubscription` ). Ujistěte se, že je oblast shodná s oblastí vašeho předplatného.

Pokud máte v úmyslu vytvořit vlastní model se zvukovými daty, použijte k rychlejšímu školení jednu z [oblastí s vyhrazeným hardwarem](custom-speech-overview.md#set-up-your-azure-account) . Pomocí [REST API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) můžete zkopírovat plně vycvičený model do jiné oblasti později.

### <a name="intent-recognition"></a>Rozpoznávání záměru

Dostupné oblasti pro **rozpoznávání záměrů** prostřednictvím sady Speech SDK jsou následující:

| Globální oblast | Oblast           | Identifikátor oblasti |
| ------------- | ---------------- | -------------------- |
| Asie          | Východní Asie        | `eastasia`           |
| Asie          | Southeast Asia   | `southeastasia`      |
| Austrálie     | Austrálie – východ   | `australiaeast`      |
| Evropa        | Severní Evropa     | `northeurope`        |
| Evropa        | West Europe      | `westeurope`         |
| Severní Amerika | East US          | `eastus`             |
| Severní Amerika | USA – východ 2        | `eastus2`            |
| Severní Amerika | Středojižní USA | `southcentralus`     |
| Severní Amerika | USA – středozápad  | `westcentralus`      |
| Severní Amerika | USA – západ          | `westus`             |
| Severní Amerika | Západní USA 2        | `westus2`            |
| Jižní Amerika | Brazílie – jih     | `brazilsouth`        |

Toto je podmnožina oblastí publikování, které podporuje [služba Language Understanding (Luis)](../luis/luis-reference-regions.md).

### <a name="voice-assistants"></a>Hlasoví asistenti

[Sada Speech SDK](speech-sdk.md) podporuje funkce **hlasového asistenta** prostřednictvím [přímého lineového rozpoznávání řeči](./direct-line-speech.md) v těchto oblastech:

| Globální oblast | Oblast           | Identifikátor oblasti    |
| ------------- | ---------------- | -------------------- |
| Severní Amerika | USA – západ          | `westus`             |
| Severní Amerika | Západní USA 2        | `westus2`            |
| Severní Amerika | East US          | `eastus`             |
| Severní Amerika | USA – východ 2        | `eastus2`            |
| Severní Amerika | USA – středozápad  | `westcentralus`      |
| Severní Amerika | Středojižní USA | `southcentralus`     |
| Evropa        | West Europe      | `westeurope`         |
| Evropa        | Severní Evropa     | `northeurope`        |
| Asie          | Východní Asie        | `eastasia`           |
| Asie          | Southeast Asia   | `southeastasia`      |
| Indie         | Indie – střed    | `centralindia`       |

### <a name="speaker-recognition"></a>Rozpoznávání mluvčího

Rozpoznávání mluvčího je aktuálně k dispozici pouze v `westus` oblasti.

## <a name="rest-apis"></a>Rozhraní REST API

Služba Speech také zpřístupňuje koncové body REST pro požadavky řeči na text a převod textu na řeč.

### <a name="speech-to-text"></a>Převod řeči na text

Referenční dokumentaci k textu pro převod řeči na text najdete v tématu [REST API řeči](rest-speech-to-text.md).

Koncový bod pro REST API má tento formát:

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Nahraďte `<REGION_IDENTIFIER>` identifikátorem, který odpovídá oblasti vašeho předplatného z této tabulky:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Parametr Language se musí připojit k adrese URL, aby nedošlo k 4xx chybě HTTP. Například jazyk nastavený na AMERICKou angličtinu pomocí Západní USAho koncového bodu je: `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

### <a name="text-to-speech"></a>Převod textu na řeč

Referenční dokumentaci pro převod textu na řeč najdete v tématu [REST API převodu textu na řeč](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]