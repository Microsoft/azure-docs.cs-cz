---
title: Jak získat koncový bod předpovědi V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.author: diberry
ms.openlocfilehash: d73508e8734883f5ffef205b6c1f03905f349f91
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316476"
---
1. Na portálu LUIS v části **Spravovat** (nabídka v pravém horním rohu) na stránce **prostředky** předpovědi na kartě **prostředky předpovědi** zkopírujte **vzorový dotaz** ve spodní části stránky.

    Vložte adresu URL do nové karty prohlížeče.

    Adresa URL má ID aplikace, klíč a název slotu. Adresa URL koncového bodu verze V3 vypadá takto:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

