---
title: Oblasti – služba řeči
titleSuffix: Azure Cognitive Services
description: Referenční informace pro oblasti služby Speech.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 469dab093ed7a62171d232695af3258cc874b5f3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481306"
---
# <a name="speech-service-supported-regions"></a>Oblasti podporované službou Speech

Služba rozpoznávání řeči umožňuje aplikaci převést zvuk na text, provést překlad řeči a převést text na řeč. Služba je dostupná ve více oblastech s jedinečnými koncovými body pro sadu Speech SDK a rozhraní REST API.

Ujistěte se, že používáte koncový bod, který odpovídá oblasti vašeho předplatného.

## <a name="speech-sdk"></a>Speech SDK

V [sadě Speech SDK](speech-sdk.md)jsou oblasti určeny jako řetězec (například jako parametr `SpeechConfig.FromSubscription` v sadě Speech SDK pro C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Převod řeči na text, převod textu na řeč a překlad

Sada Speech SDK je k dispozici v těchto oblastech pro **rozpoznávání řeči**, převod **textu na řeč**a **překlady**:

  Oblast | Parametr sady Speech SDK | Portál pro přizpůsobení řeči
 ------|-------|--------
 Západní USA | `westus` | https://westus.cris.ai
 Západní USA 2 | `westus2` | https://westus2.cris.ai
 USA – východ | `eastus` | https://eastus.cris.ai
 Východ USA 2 | `eastus2` | https://eastus2.cris.ai
 Střední USA | `centralus` | https://centralus.cris.ai
 Středoseverní USA | `northcentralus` | https://northcentralus.cris.ai
 Středojižní USA | `southcentralus` | https://southcentralus.cris.ai
 Střední Indie | `centralindia` | https://centralindia.cris.ai
 Východní Asie | `eastasia` | https://eastasia.cris.ai
 Jihovýchodní Asie | `southeastasia` | https://southeastasia.cris.ai
 Japonsko – východ | `japaneast` | https://japaneast.cris.ai
 Korea – střed | `koreacentral` | https://koreacentral.cris.ai
 Austrálie – východ | `australiaeast` | https://australiaeast.cris.ai
 Kanada – střed | `canadacentral` | https://canadacentral.cris.ai
 Severní Evropa | `northeurope` | https://northeurope.cris.ai
 Západní Evropa | `westeurope` | https://westeurope.cris.ai
 Velká Británie – jih | `uksouth` | https://uksouth.cris.ai
 Francie – střed | `francecentral` | https://francecentral.cris.ai

### <a name="intent-recognition"></a>Rozpoznávání záměru

Dostupné oblasti pro **rozpoznávání záměrů** prostřednictvím sady Speech SDK jsou následující:

 Globální oblast | Oblast | Parametr sady Speech SDK
 ------|-------|--------
 Asie | Východní Asie | `eastasia`
 Asie | Jihovýchodní Asie | `southeastasia`
 Austrálie | Austrálie – východ | `australiaeast`
 Evropa | Severní Evropa | `northeurope`
 Evropa | Západní Evropa | `westeurope`
 Severní Amerika | USA – východ | `eastus`
 Severní Amerika | Východ USA 2 | `eastus2`
 Severní Amerika | Středojižní USA | `southcentralus`
 Severní Amerika | Středozápadní USA | `westcentralus`
 Severní Amerika | Západní USA | `westus`
 Severní Amerika | Západní USA 2 | `westus2`
 Jižní Amerika | Brazílie – jih | `brazilsouth`

Toto je podmnožina oblastí publikování, které podporuje [služba Language Understanding (Luis)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-assistants"></a>Asistenti hlasu

[Sada Speech SDK](speech-sdk.md) podporuje možnosti **hlasového asistenta** v těchto oblastech:

Oblast | Parametr sady Speech SDK
-------|---------------------
Západní USA | `westus`
Západní USA 2 | `westus2`
USA – východ | `eastus`
Východ USA 2 | `eastus2`
Západní Evropa | `westeurope`
Severní Evropa | `northeurope`
Jihovýchodní Asie | `southeastasia`

## <a name="rest-apis"></a>Rozhraní REST API

Služba Speech také zpřístupňuje koncové body REST pro požadavky řeči na text a převod textu na řeč.

### <a name="speech-to-text"></a>Převod řeči na text

Referenční dokumentaci k textu pro převod řeči na text najdete v tématu [REST API řeči](rest-speech-to-text.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Převod textu na řeč

Referenční dokumentaci pro převod textu na řeč najdete v tématu [REST API převodu textu na řeč](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]