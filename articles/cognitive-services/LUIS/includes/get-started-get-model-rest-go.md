---
title: Získání modelu s voláním REST v go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 8d180eeffdbc41db6fa0e636daf7702faad47fcc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368449"
---
## <a name="prerequisites"></a>Požadavky

* Azure Language Understanding – vytváření klíče 32 znaků prostředku a vytváření adresy URL koncového bodu. Vytvořte pomocí [portálu Azure nebo](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) [příkazového příkazového příkazu Kontu Azure](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importujte aplikaci [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) z úložiště GitHub, které rozumí kognitivním službám.
* ID aplikace LUIS pro naimportovanou aplikaci TravelAgent ID aplikace je uvedené na řídicím panelu aplikace.
* ID verze aplikace, která přijímá promluvy. Výchozí ID je 0.1.
* Programovací jazyk [Go](https://golang.org/)
* [Kód visual studia](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Soubor JSON s ukázkovými promluvami

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Programová změna modelu

1. Vytvořte nový soubor s názvem `predict.go`. Přidejte následující kód:

    ```go
    // dependencies
    package main
    import (
        "fmt"
        "net/http"
        "io/ioutil"
        "log"
        "strings"
    )

    // main function
    func main() {

        // NOTE: change to your app ID
        var appID = "YOUR-APP-ID"

        // NOTE: change to your authoring key
        var authoringKey = "YOUR-KEY"

        // NOTE: change to your authoring key's endpoint, for example, your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"

        var version = "0.1"

        var exampleUtterances = `
        [
            {
              'text': 'go to Seattle today',
              'intentName': 'BookFlight',
              'entityLabels': [
                {
                  'entityName': 'Location::LocationTo',
                  'startCharIndex': 6,
                  'endCharIndex': 12
                }
              ]
            },
            {
                'text': 'a barking dog is annoying',
                'intentName': 'None',
                'entityLabels': []
            }
          ]
        `

        fmt.Println("add example utterances requested")
        addUtterance(authoringKey, appID, version, exampleUtterances, endpoint)

        fmt.Println("training selected")
        requestTraining(authoringKey, appID, version, endpoint)

        fmt.Println("training status selected")
        getTrainingStatus(authoringKey, appID, version, endpoint)
    }

    // get utterances from file and add to model
    func addUtterance(authoringKey string, appID string,  version string, labeledExampleUtterances string, endpoint string){

        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/examples", endpoint, appID, version)

        httpRequest("POST", authoringUrl, authoringKey, labeledExampleUtterances)
    }
    func requestTraining(authoringKey string, appID string,  version string, endpoint string){

        trainApp("POST", authoringKey, appID, version, endpoint)
    }
    func trainApp(httpVerb string, authoringKey string, appID string,  version string, endpoint string){

        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/train", endpoint, appID, version)

        httpRequest(httpVerb,authoringUrl, authoringKey, "")
    }
    func getTrainingStatus(authoringKey string, appID string, version string, endpoint string){

        trainApp("GET", authoringKey, appID, version, endpoint)
    }
    // generic HTTP request
    // includes setting header with authoring key
    func httpRequest(httpVerb string, url string, authoringKey string, body string){

        client := &http.Client{}

        request, err := http.NewRequest(httpVerb, url, strings.NewReader(body))
        request.Header.Add("Ocp-Apim-Subscription-Key", authoringKey)

        fmt.Println("body")
        fmt.Println(body)

        response, err := client.Do(request)
        if err != nil {
            log.Fatal(err)
        } else {
            defer response.Body.Close()
            contents, err := ioutil.ReadAll(response.Body)
            if err != nil {
                log.Fatal(err)
            }
            fmt.Println("   ", response.StatusCode)
            fmt.Println(string(contents))
        }
    }
    ```

1. Nahraďte hodnoty `YOUR-` začínající vlastními hodnotami.

    |Informace|Účel|
    |--|--|
    |`YOUR-KEY`|Váš 32 znak ový klíč.|
    |`YOUR-ENDPOINT`| Koncový bod adresy URL pro vytváření. Například, `replace-with-your-resource-name.api.cognitive.microsoft.com`. Při vytváření prostředku nastavíte název prostředku.|
    |`YOUR-APP-ID`| ID aplikace LUIS. |

    Přiřazené klíče a prostředky jsou viditelné na portálu LUIS v části Spravovat na stránce **prostředků Azure.** ID aplikace je k dispozici ve stejném oddílu Správa na stránce **Nastavení aplikace.**

1. Pomocí příkazového řádku ve stejném adresáři, ve kterém jste soubor vytvořili, zadejte následující příkaz pro kompilaci souboru Go:

    ```console
    go build model.go
    ```

1. Aplikaci Go spusťte z příkazového řádku zadáním následujícího textu do příkazového řádku:

    ```console
    go run model.go
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto rychlého startu odstraňte soubor ze systému souborů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Doporučené postupy pro aplikaci](../luis-concept-best-practices.md)