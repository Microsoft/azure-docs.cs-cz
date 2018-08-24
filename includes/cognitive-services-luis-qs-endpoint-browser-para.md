---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: dae56e05f01e83f05e75fdf378c0c50679d18728
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42820080"
---
Vysvětlení, co vrátí koncový bod služby LUIS predikcí, zobrazení výsledků predikce ve webovém prohlížeči. Aby bylo možné dotazovat veřejné aplikace, musíte si vlastní klíč a ID aplikace. Veřejné ID aplikace IoT, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, je k dispozici jako část adresy URL v kroku jedna.

Formát adresy URL pro **získat** požadavku koncového bodu je:

```JSON
https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<YOUR-KEY>&q=<user-utterance>
```

1. Koncový bod veřejné aplikace IoT je v tomto formátu: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_KEY>&q=turn on the bedroom light`

    Zkopírujte adresu URL a náhradní klíč pro hodnotu vlastnosti `<YOUR_KEY>`.

2. Vložte adresu URL do okna prohlížeče a stiskněte Enter. Prohlížeč zobrazí výsledek JSON, která označuje, že zjistí služba LUIS `HomeAutomation.TurnOn` záměru jako hlavní záměr a `HomeAutomation.Room` entity s hodnotou `bedroom`.

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

3. Změňte hodnotu parametru `q=` v adrese URL na `turn off the living room light` a stiskněte Enter. Výsledek teď znamená, že služba LUIS nalezen `HomeAutomation.TurnOff` záměru jako hlavní záměr a `HomeAutomation.Room` entity s hodnotou `living room`. 

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
