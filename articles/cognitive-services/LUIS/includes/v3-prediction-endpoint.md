---
title: Jak získat koncový bod předpovědi V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: a607ad71915606d6046b4c71291a49b8641bdcc9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91544896"
---
1. Na portálu LUIS v části **Spravovat** (nabídka v pravém horním rohu) na stránce **prostředky** předpovědi na kartě **prostředky předpovědi** zkopírujte **vzorový dotaz** ve spodní části stránky.

    Vložte adresu URL do nové karty prohlížeče.

    Adresa URL má ID aplikace, klíč a název slotu. Adresa URL koncového bodu verze V3 vypadá takto:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

