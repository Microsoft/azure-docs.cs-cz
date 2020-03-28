---
title: 'Úvodní příručka: Získejte záměr s prohlížečem - LUIS'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijte dostupnou veřejnou aplikaci LUIS k určení záměru uživatele z konverzačního textu v prohlížeči.
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987950"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Úvodní příručka: Získání záměru s prohlížečem

Abyste porozuměli výsledkům koncového bodu předpovědí služby LUIS, zobrazte si výsledek předpovědi ve webovém prohlížeči.

## <a name="prerequisites"></a>Požadavky

Chcete-li zadat dotaz na veřejnou aplikaci, potřebujete:

* Vlastní klíč pro vytváření nebo předpověď jazyka (LUIS), který lze získat z [portálu LUIS (Preview).](https://preview.luis.ai/) Pokud ještě nemáte předplatné na vytvoření klíče, můžete se zaregistrovat k [bezplatnému účtu](https://azure.microsoft.com/free/).
* ID veřejné aplikace: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.

## <a name="use-the-browser-to-see-predictions"></a>Zobrazení předpovědí pomocí prohlížeče

1. Otevřete webový prohlížeč.
1. Použijte úplné adresy URL níže `YOUR-KEY` a nahraďte je vlastním klíčem Luis Authoring nebo Prediction. Požadavky jsou požadavky GET a zahrnují autorizaci pomocí klíče pro vytváření nebo předpověď luis jako parametr řetězce dotazu.

    #### <a name="v3-prediction-request"></a>[Požadavek na předpověď V3](#tab/V3-1-1)


    Formát adresy URL V3 pro požadavek **koncového** bodu GET (podle slotů) je:

    `
    https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-KEY
    `

    #### <a name="v2-prediction-request"></a>[Požadavek na předpověď V2](#tab/V2-1-2)

    Formát adresy URL V2 pro požadavek koncového bodu **GET** je:

    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-KEY&q=turn on all lights
    `

1. Vložte adresu URL do okna prohlížeče a stiskněte Enter. V prohlížeči se zobrazí výsledek JSON, který značí, že služba LUIS rozpoznala záměr `HomeAutomation.TurnOn` jako hlavní záměr a entitu `HomeAutomation.Operation` s hodnotou `on`.

    #### <a name="v3-prediction-response"></a>[Predikční odpověď V3](#tab/V3-2-1)

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

    #### <a name="v2-prediction-response"></a>[Predikční odpověď V2](#tab/V2-2-2)

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

    #### <a name="v3-prediction-endpoint"></a>[Koncový bod predikce V3](#tab/V3-3-1)

    Chcete-li zobrazit všechny záměry , přidat `show-all-intents=true` na konec řetězce **dotazu**:

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

    #### <a name="v2-prediction-endpoint"></a>[Koncový bod predikce V2](#tab/V2)

    Chcete-li zobrazit všechny záměry , přidat `verbose=true` na konec řetězce **dotazu**:

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

Další informace o [koncovém bodu predikce V3](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Vytvoření aplikace na portálu LUIS](get-started-portal-build-app.md)
