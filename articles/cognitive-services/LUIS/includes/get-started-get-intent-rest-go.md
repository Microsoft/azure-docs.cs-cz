---
title: Buďte intent s voláním REST v go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: d9af9f788e2309cdf687e0a13b77220c150a0e1e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733296"
---
## <a name="prerequisites"></a>Požadavky

* Programovací jazyk [Go](https://golang.org/)
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

Použijte Přejít na dotaz [koncového bodu předpověď](https://aka.ms/luis-apim-v3-prediction) a získat výsledek předpověď.

1. Vytvořte nový soubor s názvem `predict.go`. Přidejte následující kód:

    ```go
    package main

    /* Do dependencies */
    import (
        "fmt"
        "net/http"
        "net/url"
        "io/ioutil"
        "log"
    )
    func main() {

        // public app
        var appID = "df67dcdb-c37d-46af-88e1-8b97951ca1c2"

        // utterance for public app
        var utterance = "turn on all lights"

        // YOUR-KEY - your **Runtime** key
        var endpointKey = "YOUR-KEY"

        // YOUR-ENDPOINT - example is your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"

        endpointPrediction(appID, endpointKey, endpoint, utterance)
    }
    func endpointPrediction(appID string, endpointKey string, endpoint string, utterance string) {

        var endpointUrl = fmt.Sprintf("https://%s/luis/prediction/v3.0/apps/%s/slots/production/predict?subscription-key=%s&verbose=true&show-all-intents=true&query=%s", endpoint, appID, endpointKey, url.QueryEscape(utterance))

        response, err := http.Get(endpointUrl)

        if err!=nil {
            // handle error
            fmt.Println("error from Get")
            log.Fatal(err)
        }

        response2, err2 := ioutil.ReadAll(response.Body)

        if err2!=nil {
            // handle error
            fmt.Println("error from ReadAll")
            log.Fatal(err2)
        }

        fmt.Println("response")
        fmt.Println(string(response2))
    }
    ```

1. Nahraďte hodnoty `YOUR-KEY` a `YOUR-ENDPOINT` vlastním predikčním klíčem **runtime** a koncovým bodem.

    |Informace|Účel|
    |--|--|
    |`YOUR-KEY`|Váš klíč **runtime** predikce 32 znaků.|
    |`YOUR-ENDPOINT`| Koncový bod adresy URL předpovědi. Například, `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. Pomocí příkazového řádku ve stejném adresáři, ve kterém jste soubor vytvořili, zadejte následující příkaz pro kompilaci souboru Go:

    ```console
    go build predict.go
    ```

1. Aplikaci Go spusťte z příkazového řádku zadáním následujícího textu do příkazového řádku:

    ```console
    go run predict.go
    ```

    Odpověď příkazového řádku je:

    ```console
    appID has value df67dcdb-c37d-46af-88e1-8b97951ca1c2
    endpointKey has value a7b206911f714e71a1ddae36928a61cc
    endpoint has value westus2.api.cognitive.microsoft.com
    utterance has value turn on all lights
    response
    {"query":"turn on all lights","prediction":{"topIntent":"HomeAutomation.TurnOn","intents":{"HomeAutomation.TurnOn":{"score":0.5375382},"None":{"score":0.08687421},"HomeAutomation.TurnOff":{"score":0.0207554}},"entities":{"HomeAutomation.Operation":["on"],"$instance":{"HomeAutomation.Operation":[{"type":"HomeAutomation.Operation","text":"on","startIndex":5,"length":2,"score":0.724984169,"modelTypeId":-1,"modelType":"Unknown","recognitionSources":["model"]}]}}}}
    ```

    JSON formátován pro čitelnost:

    ```json
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