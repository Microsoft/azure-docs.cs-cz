---
title: Oblasti služby řeči
titlesuffix: Azure Cognitive Services
description: Referenční informace pro oblasti Speech Service.
services: cognitive-services
author: mahilleb-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mahilleb
ms.openlocfilehash: a5fce6f9547a96da3ce482ce388e5ba2093f2af4
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49468122"
---
# <a name="regions-of-the-speech-service"></a>Oblasti Speech Service

Speech Service je k dispozici v různých oblastech.
Když vytvoříte odběr, můžete vybrat dostupnou oblast na základě vašich potřeb.

Při použití předplatného máte účet pro oblast, kterou jste vybrali.

## <a name="rest-api"></a>REST API

Použití rozhraní REST API k výběru správné koncové body oblast.
Zobrazit [rozhraní REST API](rest-apis.md) podrobnosti.

## <a name="speech-sdk"></a>Speech SDK

V [sadou SDK pro řeč služby](speech-sdk.md), oblastí jsou určené jako řetězec (například jako parametr `SpeechConfig.FromSubscription` v sadou SDK pro řeč pro jazyk C#).

### <a name="regions-for-speech-recognition-and-translation"></a>Oblasti pro rozpoznávání řeči a překladu

V následující tabulce jsou uvedeny dostupné oblasti pro **rozpoznávání řeči** a **překlad**.

  Oblast | Parametr SDK řeči | Portál
 ------|-------|--------
 USA – západ | `westus` | https://westus.cris.ai
 Západní USA 2 | `westus2` | https://westus2.cris.ai 
 USA – východ | `eastus` | https://eastus.cris.ai
 USA – východ 2 | `eastus2` | https://eastus2.cris.ai
 Východní Asie | `eastasia` | https://eastasia.cris.ai
 Jihovýchodní Asie | `southeastasia` | https://southeastasia.cris.ai
 Severní Evropa | `northeurope` | https://northeurope.cris.ai
 Západní Evropa | `westeurope` | https://westeurope.cris.ai


### <a name="regions-for-intent-recognition"></a>Oblasti pro rozpoznání záměru

Dostupné oblasti pro **rozpoznání záměru** prostřednictvím sadou SDK pro řeč jsou uvedeny na [stránku oblasti služby Language Understanding](/azure/cognitive-services/luis/luis-reference-regions).
Pro každou oblast publikování uvedené příslušného parametru oblasti sadou SDK pro řeč určena jako první část názvu domény koncového bodu.
Například použít `westus` určit publikování oblast západní USA.
