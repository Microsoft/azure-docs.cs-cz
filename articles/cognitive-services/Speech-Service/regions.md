---
title: Oblastí – hlasové služby
titlesuffix: Azure Cognitive Services
description: Referenční informace pro oblasti Speech Service.
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mahilleb
ms.custom: seodec18
ms.openlocfilehash: d41213d72d40555d8dc5aeab76040fc556dae774
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091641"
---
# <a name="speech-service-supported-regions"></a>Speech Service podporované oblasti

Služba Speech umožňuje vaší aplikaci převod zvuku na text, překlad řeči a převod textu na řeč. Služba je k dispozici v několika oblastech pomocí jedinečných koncových bodů pro rozhraní API REST a sadou SDK pro řeč.

Ujistěte se, že používáte koncového bodu, který se shoduje s oblastí pro vaše předplatné.

## <a name="speech-sdk"></a>Speech SDK

V [sadou SDK pro řeč služby](speech-sdk.md), oblastí jsou určené jako řetězec (například jako parametr `SpeechConfig.FromSubscription` v sadou SDK pro řeč pro jazyk C#).

### <a name="speech-recognition-and-translation"></a>Rozpoznávání řeči a překladu

Sadou SDK pro řeč je k dispozici v těchto oblastech pro **rozpoznávání řeči** a **překlad**:

  Oblast | Parametr SDK řeči | Přizpůsobení portálu řeči
 ------|-------|--------
 USA – západ | `westus` | https://westus.cris.ai
 Západní USA 2 | `westus2` | https://westus2.cris.ai
 USA – východ | `eastus` | https://eastus.cris.ai
 USA – východ 2 | `eastus2` | https://eastus2.cris.ai
 Východní Asie | `eastasia` | https://eastasia.cris.ai
 Jihovýchodní Asie | `southeastasia` | https://southeastasia.cris.ai
 Severní Evropa | `northeurope` | https://northeurope.cris.ai
 Západní Evropa | `westeurope` | https://westeurope.cris.ai


### <a name="intent-recognition"></a>Rozpoznání záměru

**Rozpoznání záměru** složek sadou SDK pro řeč oblasti podporují služba luis. Úplný seznam dostupných oblastí najdete v tématu [publikování oblasti a koncové body – LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions)

Dostupné oblasti pro **rozpoznání záměru** prostřednictvím sadou SDK pro řeč jsou uvedeny na [stránku oblasti služby Language Understanding](/azure/cognitive-services/luis/luis-reference-regions).

Pro každou oblast publikování uvedené, pomocí zadaných **název oblasti rozhraní API**. Například použít `westus` pro oblast západní USA.

## <a name="rest-apis"></a>Rozhraní REST API

Speech service také poskytuje koncové body REST pro požadavky na převod řeči na text a převod textu na řeč.

### <a name="speech-to-text"></a>Převod řeči na text

Převod řeči na text referenční dokumentaci, naleznete v tématu [rozhraní REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Převod textu na řeč

Převod textu na řeč referenční dokumentaci najdete v tématu [rozhraní REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#speech-to-text).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
