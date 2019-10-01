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
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6282e768ebc51d0d4ec2b15f057727f207a7d81a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703135"
---
# <a name="quickstart-get-intent-with-a-browser"></a>Rychlý Start: získání záměru pomocí prohlížeče

Abyste porozuměli výsledkům koncového bodu předpovědí služby LUIS, zobrazte si výsledek předpovědi ve webovém prohlížeči. 

## <a name="prerequisites"></a>Předpoklady

K dotazování veřejné aplikace potřebujete:

* Vlastní klíč Language Understanding (LUIS). Pokud ještě nemáte předplatné k vytvoření klíče, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).
* ID veřejné aplikace: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. 

## <a name="use-the-browser-to-see-predictions"></a>Použití prohlížeče k zobrazení předpovědi

1. Otevřete webový prohlížeč. 
1. Použijte níže uvedené úplné adresy URL a nahraďte `{your-key}` vlastním klíčem LUIS. Požadavky jsou požadavky GET a zahrnují autorizaci s klíčem LUIS jako parametr řetězce dotazu.

    #### <a name="v2-prediction-endpoint-requesttabv2"></a>[Hodnota koncového bodu předpovědi v2](#tab/V2)
    
    Formát adresy URL v2 pro požadavek **Get** Endpoint je:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key={your-key}&q=turn on all lights
    `
    
    #### <a name="v3-prediction-endpoint-requesttabv3"></a>[Požadavek na koncový bod verze V3](#tab/V3)
    
    
    Formát adresy URL v3 pro požadavek **Get** Endpoint (podle slotů) je:
    
    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}
    `
    
    * * *

1. Vložte adresu URL do okna prohlížeče a stiskněte Enter. V prohlížeči se zobrazí výsledek JSON, který značí, že služba LUIS rozpoznala záměr `HomeAutomation.TurnOn` jako hlavní záměr a entitu `HomeAutomation.Operation` s hodnotou `on`.

    #### <a name="v2-prediction-endpoint-responsetabv2"></a>[Předpověď odezvy koncového bodu v2](#tab/V2)

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

    #### <a name="v3-prediction-endpoint-responsetabv3"></a>[Prediktivní odezva koncového bodu V3](#tab/V3)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
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

    * * *

1. Chcete-li zobrazit všechny záměry, přidejte příslušný parametr řetězce dotazu. 

    #### <a name="v2-prediction-endpointtabv2"></a>[Koncový bod pro předpověď v2](#tab/V2)

    Do konce řetězce dotazu přidejte `verbose=true`, aby se **zobrazily všechny záměry**:

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

    #### <a name="v3-prediction-endpointtabv3"></a>[Prediktivní koncový bod V3](#tab/V3)

    Do konce řetězce dotazu přidejte `show-all-intents=true`, aby se **zobrazily všechny záměry**:

    `
    https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key={your-key}&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "normalizedQuery": "turn on all lights",
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "HomeAutomation.TurnOff": {
                     "score": 0.0207554
                },
                "None": {
                     "score": 0.08687421
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

    * * * 

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [koncovém bodu předpovědi V3](luis-migration-api-v3.md).

> [!div class="nextstepaction"]
> [Vytvoření aplikace na portálu LUIS](get-started-portal-build-app.md)