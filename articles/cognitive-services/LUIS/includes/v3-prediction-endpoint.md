---
title: Jak získat koncový bod predikce V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 23e7f88b23ac7af7470183877eeb1be89e79779c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80287743"
---
1. Na portálu LUIS v části **Spravovat** (vpravo nahoře) na stránce **Prostředky Azure** (vlevo) na kartě **Prostředky předpovědi** zkopírujte **ukázkový dotaz** v dolní části stránky.

    Vložte adresu URL do nové karty prohlížeče.

    Adresa URL má ID aplikace, klíč a název slotu. Adresa URL koncového bodu predikce V3 vypadá takto:

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

