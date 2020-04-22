---
title: 'Úvodní příručka: Dotaz na předpověď pomocí prohlížeče – LUIS'
description: V tomto rychlém startu použijte dostupnou veřejnou aplikaci LUIS k určení záměru uživatele z konverzačního textu v prohlížeči.
ms.topic: quickstart
ms.date: 04/21/2020
ms.openlocfilehash: 5ba86882ebf3cb538ad6b865382342fcbd43d27c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769970"
---
# <a name="quickstart-query-prediction-runtime-with-user-text"></a>Úvodní příručka: Doba běhu předpovědi dotazu s textem uživatele

Abyste porozuměli výsledkům koncového bodu předpovědí služby LUIS, zobrazte si výsledek předpovědi ve webovém prohlížeči.

## <a name="prerequisites"></a>Požadavky

Chcete-li zadat dotaz na veřejnou aplikaci, potřebujete:

* Informace o prostředku pro jazykové porozumění (LUIS):
    * **Predikční klíč** - který lze získat z [luis portálu](https://www.luis.ai/). Pokud ještě nemáte předplatné na vytvoření klíče, můžete se zaregistrovat k [bezplatnému účtu](https://azure.microsoft.com/free/).
    * **Poddoména koncového bodu předpověď** – subdoména je také **název** prostředku LUIS.
* ID aplikace LUIS – použijte veřejné IOT `df67dcdb-c37d-46af-88e1-8b97951ca1c2`ID aplikace . Uživatelský dotaz použitý v kódu rychlého startu je specifický pro tuto aplikaci.

## <a name="use-the-browser-to-see-predictions"></a>Zobrazení předpovědí pomocí prohlížeče

1. Otevřete webový prohlížeč.
1. Použijte úplné adresy URL níže `YOUR-KEY` a nahraďte je vlastním klíčem Luis Prediction. Požadavky jsou požadavky GET a zahrnují autorizaci s klíčem LUIS Prediction jako parametr řetězce dotazu.

    #### <a name="v3-prediction-request"></a>[Požadavek na předpověď V3](#tab/V3-1-1)


    Formát adresy URL V3 pro požadavek **koncového** bodu GET (podle slotů) je:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY
    `

    #### <a name="v2-prediction-request"></a>[Požadavek na předpověď V2](#tab/V2-1-2)

    Formát adresy URL V2 pro požadavek koncového bodu **GET** je:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-LUIS-PREDICTION-KEY&q=turn on all lights
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
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&show-all-intents=true
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
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&verbose=true
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

## <a name="next-steps"></a>Další kroky

Další informace:
* [Koncový bod predikce V3](luis-migration-api-v3.md)
* [Vlastní subdomény](../cognitive-services-custom-subdomains.md)

> [!div class="nextstepaction"]
> [Vytvoření aplikace na portálu LUIS](get-started-portal-build-app.md)
