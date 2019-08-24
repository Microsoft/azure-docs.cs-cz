---
title: Oblasti – služba řeči
titleSuffix: Azure Cognitive Services
description: Referenční informace pro oblasti Speech Service.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: c0414277b4851891911908ba4f42e92abedc86e4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553217"
---
# <a name="speech-service-supported-regions"></a>Speech Service podporované oblasti

Služba Speech umožňuje vaší aplikaci převod zvuku na text, překlad řeči a převod textu na řeč. Služba je k dispozici v několika oblastech pomocí jedinečných koncových bodů pro rozhraní API REST a sadou SDK pro řeč.

Ujistěte se, že používáte koncového bodu, který se shoduje s oblastí pro vaše předplatné.

## <a name="speech-sdk"></a>Speech SDK

V [sadě Speech SDK](speech-sdk.md)jsou oblasti určeny jako řetězec (například jako parametr `SpeechConfig.FromSubscription` v sadě Speech SDK pro C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Převod řeči na text, převod textu na řeč a překlad

Sada Speech SDK je k dispozici v těchto oblastech pro **rozpoznávání řeči**, převod **textu na řeč**a **překlady**:

  Oblast | Parametr SDK řeči | Přizpůsobení portálu řeči
 ------|-------|--------
 USA – západ | `westus` | https://westus.cris.ai
 USA – západ 2 | `westus2` | https://westus2.cris.ai
 East US | `eastus` | https://eastus.cris.ai
 Východní USA 2 | `eastus2` | https://eastus2.cris.ai
 Střední USA | `centralus` | https://centralus.cris.ai
 Středoseverní USA | `northcentralus` | https://northcentralus.cris.ai
 Středojižní USA | `southcentralus` | https://southcentralus.cris.ai
 Střed Indie | `centralindia` | https://centralindia.cris.ai
 Východní Asie | `eastasia` | https://eastasia.cris.ai
 Jihovýchodní Asie | `southeastasia` | https://southeastasia.cris.ai
 Japonsko – východ | `japaneast` | https://japaneast.cris.ai
 Jižní Korea – střed | `koreacentral` | https://koreacentral.cris.ai
 Austrálie – východ | `australiaeast` | https://australiaeast.cris.ai
 Kanada – střed | `canadacentral` | https://canadacentral.cris.ai
 Severní Evropa | `northeurope` | https://northeurope.cris.ai
 Západní Evropa | `westeurope` | https://westeurope.cris.ai
 Velká Británie – jih | `uksouth` | https://uksouth.cris.ai
 Francie – střed | `francecentral` | https://francecentral.cris.ai

### <a name="intent-recognition"></a>Rozpoznání záměru

Dostupné oblasti pro **rozpoznávání záměrů** prostřednictvím sady Speech SDK jsou následující:

 Globální oblast | Oblast | Parametr SDK řeči
 ------|-------|--------
 Asie | Východní Asie | `eastasia`
 Asie | Jihovýchodní Asie | `southeastasia`
 Austrálie | Austrálie – východ | `australiaeast`
 Evropa | Severní Evropa | `northeurope`
 Evropa | Západní Evropa | `westeurope`
 Severní Amerika | East US | `eastus`
 Severní Amerika | Východní USA 2 | `eastus2`
 Severní Amerika | Středojižní USA | `southcentralus`
 Severní Amerika | Středozápadní USA | `westcentralus`
 Severní Amerika | USA – západ | `westus`
 Severní Amerika | USA – západ 2 | `westus2`
 Jižní Amerika | Brazílie – jih | `brazilsouth`

Toto je podmnožina oblastí publikování, které podporuje [služba Language Understanding (Luis)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-first-virtual-assistants"></a>Virtuální asistenti prvního hlasu

[Sada Speech SDK](speech-sdk.md) podporuje funkce pro **virtuální asistenty prvního hlasu** v těchto oblastech:

Oblast | Parametr SDK řeči
-------|---------------------
USA – západ | `westus`
USA – západ 2 | `westus2`
East US | `eastus`
Východní USA 2 | `eastus2`
Západní Evropa | `westeurope`
Severní Evropa | `northeurope`
Jihovýchodní Asie | `southeastasia`

## <a name="rest-apis"></a>Rozhraní REST API

Speech service také poskytuje koncové body REST pro požadavky na převod řeči na text a převod textu na řeč.

### <a name="speech-to-text"></a>Převod řeči na text

Referenční dokumentaci k textu pro převod řeči na text najdete v tématu [REST API řeči](rest-speech-to-text.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Převod textu na řeč

Referenční dokumentaci pro převod textu na řeč najdete v tématu [REST API převodu textu na řeč](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]