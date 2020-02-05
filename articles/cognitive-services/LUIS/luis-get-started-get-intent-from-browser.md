---
title: 'Rychlý Start: získání záměru pomocí prohlížeče – LUIS'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu pomocí dostupné veřejné aplikace LUIS určíte záměr uživatele z konverzačního textu v prohlížeči.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: diberry
ms.openlocfilehash: e06bb4c09b3ebab25c0c0ef8ac5c51f6842f34cd
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987950"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Rychlý Start: získání záměru pomocí prohlížeče

Abyste porozuměli výsledkům koncového bodu předpovědí služby LUIS, zobrazte si výsledek předpovědi ve webovém prohlížeči.

## <a name="prerequisites"></a>Požadavky

K dotazování veřejné aplikace potřebujete:

* Vlastní klíč pro vytváření nebo předpověď Language Understanding (LUIS), který se dá získat z [portálu Luis (Preview)](https://preview.luis.ai/). Pokud ještě nemáte předplatné k vytvoření klíče, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).
* ID veřejné aplikace: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.

## <a name="use-the-browser-to-see-predictions"></a>Použití prohlížeče k zobrazení předpovědi

1. Otevřete webový prohlížeč.
1. Použijte níže uvedené úplné adresy URL, které nahradí `YOUR-KEY` vlastním klíčem pro vytváření obsahu LUIS nebo předpovědi. Požadavky jsou požadavky GET a zahrnují autorizaci s klíčovým slovem LUIS Authoring nebo předpovědi jako parametr řetězce dotazu.

    #### <a name="v3-prediction-requesttabv3-1-1"></a>[Požadavek na předpověď V3](#tab/V3-1-1)


    Formát adresy URL v3 pro požadavek **Get** Endpoint (podle slotů) je:

    `
    https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY
    `

    #### <a name="v2-prediction-requesttabv2-1-2"></a>[V2 – žádost o předpověď](#tab/V2-1-2)

    Formát adresy URL v2 pro požadavek **Get** Endpoint je:

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-KEY&q=turn on all lights
    `

1. Vložte adresu URL do okna prohlížeče a stiskněte Enter. V prohlížeči se zobrazí výsledek JSON, který značí, že služba LUIS rozpoznala záměr `HomeAutomation.TurnOn` jako hlavní záměr a entitu `HomeAutomation.Operation` s hodnotou `on`.

    #### <a name="v3-prediction-responsetabv3-2-1"></a>[Odpověď předpovědi V3](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-responsetabv2-2-2"></a>[V2 – odpověď předpovědi](#tab/V2-2-2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    * * *

1. Chcete-li zobrazit všechny záměry, přidejte příslušný parametr řetězce dotazu.

    #### <a name="v3-prediction-endpointtabv3-3-1"></a>[Prediktivní koncový bod V3](#tab/V3-3-1)

    Přidejte `show-all-intents=true` na konec řetězce dotazu, aby se **zobrazily všechny záměry**:

    `
    https://westus.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-endpointtabv2"></a>[Koncový bod pro předpověď v2](#tab/V2)

    Přidejte `verbose=true` na konec řetězce dotazu, aby se **zobrazily všechny záměry**:

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key={your-key}&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```


<!-- FIX - is the public app getting updated for the new prebuilt domain with entities? -->

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Vytvoření aplikace na portálu LUIS](get-started-portal-build-app.md)
