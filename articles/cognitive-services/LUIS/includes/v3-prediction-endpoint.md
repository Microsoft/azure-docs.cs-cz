---
title: Jak získat koncový bod předpovědi V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/05/2020
ms.author: diberry
ms.openlocfilehash: 18f5422202cf972d49349cc04b845c623cabffa3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589116"
---
1. Na portálu LUIS v části **Spravovat** (nabídka v pravém horním rohu) na stránce **prostředky** předpovědi na kartě **prostředky předpovědi** zkopírujte **vzorový dotaz** ve spodní části stránky.

    Vložte adresu URL do nové karty prohlížeče.

    Adresa URL má ID aplikace, klíč a název slotu. Adresa URL koncového bodu verze V3 vypadá takto:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

