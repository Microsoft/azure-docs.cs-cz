---
title: 'Rychlý Start: dotaz na předpověď s prohlížečem – LUIS'
description: V tomto rychlém startu pomocí dostupné veřejné aplikace LUIS určíte záměr uživatele z konverzačního textu v prohlížeči.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 04/21/2020
ms.openlocfilehash: ce7544bee32586aa4cdeeb7f47a84744194db5da
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91316269"
---
# <a name="quickstart-query-prediction-runtime-with-user-text"></a>Rychlý Start: modul runtime předpovědi dotazu s uživatelským textem

Abyste porozuměli výsledkům koncového bodu předpovědí služby LUIS, zobrazte si výsledek předpovědi ve webovém prohlížeči.

## <a name="prerequisites"></a>Předpoklady

K dotazování veřejné aplikace potřebujete:

* Informace o prostředcích Language Understanding (LUIS):
    * **Klíč předpovědi** – který se dá získat z [portálu Luis](https://www.luis.ai/). Pokud ještě nemáte předplatné k vytvoření klíče, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/cognitive-services).
    * Dílčí doména **koncového bodu předpovědi** – subdoména je také **název** vašeho prostředku Luis.
* ID aplikace LUIS – používá veřejné ID aplikace IoT `df67dcdb-c37d-46af-88e1-8b97951ca1c2` . Dotaz uživatele použitý v kódu pro rychlý Start je specifický pro danou aplikaci.

## <a name="use-the-browser-to-see-predictions"></a>Použití prohlížeče k zobrazení předpovědi

1. Otevřete webový prohlížeč.
1. Pomocí níže uvedených úplných adres URL nahraďte `YOUR-KEY` vlastní klíč předpovědi Luis. Požadavky jsou požadavky GET a zahrnují autorizaci s klíčem předpovědi LUIS jako parametr řetězce dotazu.

    #### <a name="v3-prediction-request"></a>[Požadavek na předpověď V3](#tab/V3-1-1)


    Formát adresy URL v3 pro požadavek **Get** Endpoint (podle slotů) je:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY
    `

    #### <a name="v2-prediction-request"></a>[V2 – žádost o předpověď](#tab/V2-1-2)

    Formát adresy URL v2 pro požadavek **Get** Endpoint je:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-LUIS-PREDICTION-KEY&q=turn on all lights
    `

1. Vložte adresu URL do okna prohlížeče a stiskněte Enter. V prohlížeči se zobrazí výsledek JSON, který značí, že služba LUIS rozpoznala záměr `HomeAutomation.TurnOn` jako hlavní záměr a entitu `HomeAutomation.Operation` s hodnotou `on`.

    #### <a name="v3-prediction-response"></a>[Odpověď předpovědi V3](#tab/V3-2-1)

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

    #### <a name="v2-prediction-response"></a>[V2 – odpověď předpovědi](#tab/V2-2-2)

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

    #### <a name="v3-prediction-endpoint"></a>[Prediktivní koncový bod V3](#tab/V3-3-1)

    Přidejte `show-all-intents=true` na konec řetězce dotazu, aby se **zobrazily všechny záměry**:

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

    #### <a name="v2-prediction-endpoint"></a>[Koncový bod pro předpověď v2](#tab/V2)

    Přidejte `verbose=true` na konec řetězce dotazu, aby se **zobrazily všechny záměry**:

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

Přečtěte si další informace:
* [Prediktivní koncový bod V3](luis-migration-api-v3.md)
* [Vlastní subdomény](../cognitive-services-custom-subdomains.md)

> [!div class="nextstepaction"]
> [Vytvoření aplikace na portálu LUIS](get-started-portal-build-app.md)
