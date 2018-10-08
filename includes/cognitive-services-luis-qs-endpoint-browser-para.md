---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: e507a7c45e286473abe9b9e4365e80fb29eba2a4
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47043995"
---
Abyste porozuměli výsledkům koncového bodu předpovědí služby LUIS, zobrazte si výsledek předpovědi ve webovém prohlížeči. Pokud chcete zadat dotaz na veřejnou aplikaci, potřebujete vlastní klíč a ID aplikace. ID veřejné aplikace IoT (`df67dcdb-c37d-46af-88e1-8b97951ca1c2`) najdete v rámci adresy URL v kroku 1.

Formát adresy URL pro požadavek koncového bodu **GET** je následující:

```JSON
https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<YOUR-KEY>&q=<user-utterance>
```

1. Koncový bod veřejné aplikace IoT má tento formát: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_KEY>&q=turn on the bedroom light`.

    Zkopírujte adresu URL a nahraďte hodnotu `<YOUR_KEY>` klíčem.

2. Vložte adresu URL do okna prohlížeče a stiskněte Enter. V prohlížeči se zobrazí výsledek JSON, který značí, že služba LUIS rozpoznala záměr `HomeAutomation.TurnOn` jako hlavní záměr a entitu `HomeAutomation.Room` s hodnotou `bedroom`.

    ```JSON
    {
      "query": "turn on the bedroom light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOn",
        "score": 0.809439957
      },
      "entities": [
        {
          "entity": "bedroom",
          "type": "HomeAutomation.Room",
          "startIndex": 12,
          "endIndex": 18,
          "score": 0.8065475
        }
      ]
    }
    ```

3. Změňte hodnotu parametru `q=` v adrese URL na `turn off the living room light` a stiskněte Enter. Výsledek teď značí, že služba LUIS rozpoznala záměr `HomeAutomation.TurnOff` jako hlavní záměr a entitu `HomeAutomation.Room` s hodnotou `living room`. 

    ```JSON
    {
      "query": "turn off the living room light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.984057844
      },
      "entities": [
        {
          "entity": "living room",
          "type": "HomeAutomation.Room",
          "startIndex": 13,
          "endIndex": 23,
          "score": 0.9619945
        }
      ]
    }
    ```
