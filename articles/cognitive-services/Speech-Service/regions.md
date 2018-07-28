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
ms.openlocfilehash: 082002b25b02e1e496221f4686d0e636630dd438
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324387"
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

Oblast| Hodnota parametru oblast v sadou SDK pro řeč
-|-
USA – západ| `westus`
Východní Asie| `eastasia`
Severní Evropa| `northeurope`

### <a name="regions-for-intent-recognition"></a>Oblasti pro rozpoznání záměru

Dostupné oblasti pro **rozpoznání záměru** prostřednictvím sadou SDK pro řeč jsou uvedeny v [stránku oblasti služby Language Understanding](/azure/cognitive-services/luis/luis-reference-regions).
Pro každou oblast publikování uvedené příslušného parametru oblasti sadou SDK pro řeč určena jako první část názvu domény koncového bodu.
Například použít `westus` určit publikování oblast západní USA.
