---
title: Získání záměru s voláním REST v souboru Node.js
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 3bef3cbb321465893b3a05242f0d72d77d091d6b
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733342"
---
## <a name="prerequisites"></a>Požadavky

* Programovací jazyk [Node.js](https://nodejs.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* ID aplikace LUIS – použijte veřejné IOT `df67dcdb-c37d-46af-88e1-8b97951ca1c2`ID aplikace . Uživatelský dotaz použitý v kódu rychlého startu je specifický pro tuto aplikaci.

## <a name="create-luis-runtime-key-for-predictions"></a>Vytvořit klíč runtime LUIS pro předpovědi

1. Přihlášení k [portálu Azure](https://portal.azure.com)
1. Klikněte na [Vytvořit **jazykové znalosti.** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Zadejte všechna požadovaná nastavení pro klíč **Runtime:**

    |Nastavení|Hodnota|
    |--|--|
    |Název|Požadovaný název (2-64 znaků)|
    |Předplatné|Vybrat příslušné předplatné|
    |Umístění|Vyberte libovolné blízké a dostupné místo|
    |Cenová úroveň|`F0`- minimální cenová úroveň|
    |Skupina prostředků|Výběr dostupné skupiny prostředků|

1. Klikněte na **Vytvořit** a počkejte na vytvoření prostředku. Po vytvoření přejděte na stránku prostředků.
1. Sbírat `endpoint` nakonfigurované a `key`.

## <a name="get-intent-programmatically"></a>Získání záměru prostřednictvím kódu programu

Pomocí souboru Node.js můžete zadat dotaz na [koncový bod předpověď](https://aka.ms/luis-apim-v3-prediction) a získat výsledek předpovědi.

1. Zkopírujte následující fragment kódu do souboru s názvem `predict.js`:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');
    var querystring = require('querystring');

    // Analyze text
    //
    getPrediction = async () => {

        // YOUR-KEY - Language Understanding runtime key
        var endpointKey = "YOUR-KEY";

        // YOUR-ENDPOINT Language Understanding endpoint URL, an example is your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT";

        // Set the LUIS_APP_ID environment variable
        // to df67dcdb-c37d-46af-88e1-8b97951ca1c2, which is the ID
        // of a public sample application.
        var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

        var utterance = "turn on all lights";

        // Create query string
        var queryParams = {
            "show-all-intents": true,
            "verbose":  true,
            "query": utterance,
            "subscription-key": endpointKey
        }

        // append query string to endpoint URL
        var URI = `https://${endpoint}/luis/prediction/v3.0/apps/${appId}/slots/production/predict?${querystring.stringify(queryParams)}`

        // HTTP Request
        const response = await requestpromise(URI);

        // HTTP Response
        console.log(response);

    }

    // Pass an utterance to the sample LUIS app
    getPrediction().then(()=>console.log("done")).catch((err)=>console.log(err));
    ```

1. Nahraďte hodnoty `YOUR-KEY` a `YOUR-ENDPOINT` vlastním predikčním klíčem **runtime** a koncovým bodem.

    |Informace|Účel|
    |--|--|
    |`YOUR-KEY`|Váš klíč **runtime** predikce 32 znaků.|
    |`YOUR-ENDPOINT`| Koncový bod adresy URL předpovědi. Například, `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Nainstalujte `request` `request-promise`, `querystring` a závislosti pomocí tohoto příkazu:

    ```console
    npm install request request-promise querystring
    ```

1. Spusťte aplikaci pomocí tohoto příkazu:

    ```console
    node predict.js
    ```

 1. Zkontrolujte předpověď odpověď, která je vrácena jako JSON:

    ```console
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
    ```

    Odpověď JSON formátovaná pro čitelnost:

    ```JSON
    {
        "query": "turn on all lights",
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
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto rychlého startu odstraňte soubor ze systému souborů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání promluv a vytáčení](../get-started-get-model-rest-apis.md)