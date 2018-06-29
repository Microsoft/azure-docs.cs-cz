---
title: Oblasti služby řeči | Microsoft Docs
description: Referenční informace pro oblasti řeči služby.
services: cognitive-services
author: mahilleb-msft
manager: wolmfa61
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 1eb3768f5a5c5a27a45dde3f62f862f36fa3e8ac
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098494"
---
# <a name="regions-of-the-speech-service"></a>Oblasti služby řeči

Služba rozpoznávání řeči je k dispozici v různých oblastech.
Při vytvoření odběru můžete podle potřeby vyberte oblast dostupná.

Při používání vašeho předplatného, budete muset účet pro oblast, kterou jste vybrali.

## <a name="rest-api"></a>REST API

Pomocí rozhraní REST API, vyberte práva specifické pro oblast koncových bodů.
V tématu [rozhraní REST API](rest-apis.md) podrobnosti.



## <a name="speech-sdk"></a>Speech SDK

V [řeči SDK](speech-sdk.md), oblasti jsou určené jako řetězec (například jako parametr pro [SpeechFactory.FromSubscription](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechfactory.fromsubscription) v řeči SDK pro jazyk C#).

Následující tabulka uvádí dostupné oblasti pro rozpoznávání řeči a překlad:

Oblast| Hodnota parametru oblast v sadě SDK řeči
-|-
Západní USA| `westus`
Východní Asie| `eastasia`
Severní Evropa| `northeurope`

Dostupné oblasti pro záměrné rozpoznávání pomocí sady SDK řeči jsou uvedeny v [stránku oblasti služby znalosti jazyka](/azure/cognitive-services/luis/luis-reference-regions).
Pro každou oblast publikování uvedené je určen parametr odpovídající oblast řeči SDK jako první část názvu domény koncového bodu.
Například použít `westus` k určení publikování oblast západní USA.
