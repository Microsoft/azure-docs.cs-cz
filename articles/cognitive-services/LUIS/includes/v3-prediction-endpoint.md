---
title: Jak získat koncový bod předpovědi V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: 85fcd0308083350a02afd4eadac2b361337f3b33
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128125"
---
1. Na portálu LUIS v části **Spravovat** (nabídka v pravém horním rohu) na stránce **prostředky** předpovědi na kartě **prostředky předpovědi** zkopírujte **vzorový dotaz** ve spodní části stránky. Adresa URL má ID aplikace, klíč a název slotu. Adresa URL koncového bodu verze V3 má formu: `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

    :::image type="content" source="../media/prediction-resources-example-query.png" alt-text="Příklad dotazu v části předpověď prostředků" lightbox="../media/prediction-resources-example-query.png":::
    
    Vložte adresu URL do nové karty prohlížeče. Pokud adresu URL nevidíte, nemáte k dispozici předpověď prostředků a budete ji muset vytvořit. 

    

    

