---
title: Oblasti služby řeči
description: Referenční informace pro oblasti Speech service.
services: cognitive-services
author: mahilleb-msft
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 1cb00035dc8f1cdeabd1beb22ca69f47bf4bd89e
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379566"
---
# <a name="regions-of-the-speech-service"></a>Oblasti Speech service

Speech service je k dispozici v různých oblastech.
Když vytvoříte odběr můžete vybrat dostupnou oblast, v závislosti na potřebách.

Při použití předplatného máte účet pro oblast, kterou jste vybrali.

## <a name="rest-api"></a>REST API

Pomocí rozhraní REST API, můžete si vyberte koncové body přímo specifický pro oblast.
Zobrazit [rozhraní REST API](rest-apis.md) podrobnosti.

## <a name="speech-sdk"></a>Speech SDK

V [sadou SDK pro řeč](speech-sdk.md), oblastí jsou určené jako řetězec (například jako parametr [SpeechFactory.FromSubscription](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechfactory.fromsubscription) v sadou SDK pro řeč pro jazyk C#).

### <a name="regions-for-speech-recognition-and-translation"></a>Oblasti pro rozpoznávání řeči a překladu

Následující tabulka uvádí dostupné oblasti pro **rozpoznávání řeči** a **překlad**:

Oblast| Hodnota parametru oblast v sadou SDK pro řeč| Portál
-|-
USA – západ| `westus`| https://westus.cris.ai
Západní USA 2| `westus2`| https://westus2.cris.ai
USA – východ| `eastus`| https://eastus.cris.ai
USA – východ 2| `eastus2`| https://eastus2.cris.ai
Východní Asie| `eastasia`| https://eastasia.cris.ai
Jihovýchodní Asie| `southeastasia`| https://southeastasia.cris.ai
Severní Evropa| `northeurope`| https://northeurope.cris.ai
Západní Evropa|  `westeurope`| https://westeurope.cris.ai

### <a name="regions-for-intent-recognition"></a>Oblasti pro rozpoznání záměru

Dostupné oblasti pro **rozpoznání záměru** prostřednictvím sadou SDK pro řeč jsou uvedeny v [stránku oblasti služby Language Understanding](/azure/cognitive-services/luis/luis-reference-regions).
Pro každou oblast publikování uvedené příslušného parametru oblasti sadou SDK pro řeč určena jako první část názvu domény koncového bodu.
Například použít `westus` určit publikování oblast západní USA.
