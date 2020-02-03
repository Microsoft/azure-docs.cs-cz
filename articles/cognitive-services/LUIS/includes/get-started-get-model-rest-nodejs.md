---
title: Získat model s voláním RESTC#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: a262db04e51015edb760a8b04952dfa24b2ad63a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748961"
---
## <a name="prerequisites"></a>Předpoklady

* Počáteční klíč.
* Importujte aplikaci [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) z úložiště GitHub-Services-Language-porozumění.
* ID aplikace LUIS pro importovanou aplikaci TravelAgent ID aplikace je uvedené na řídicím panelu aplikace.
* ID verze v rámci aplikace, která přijímá projevy. Výchozí ID je 0.1.
* Programovací jazyk [Node.js](https://nodejs.org/) 
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Soubor JSON s ukázkovými promluvami

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="get-luis-key"></a>Získání klíče LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="change-model-programmatically"></a>Programové změny modelu

Pomocí možnosti přejít přidejte do aplikace [rozhraní API](https://aka.ms/luis-apim-v3-authoring) náročné na počítač. 

1. Vytvořte nový soubor s názvem `model.js`. Přidejte následující kód:

    ```javascript
    var request = require('request');
    var requestpromise = require('request-promise');
    
    const LUIS_authoringKey = "YOUR-KEY";
    const LUIS_endpoint = "YOUR-ENDPOINT";
    const LUIS_appId = "YOUR-APP-ID";
    const LUIS_versionId = "0.1";
    const addUtterancesURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/examples`;
    const addTrainURI = `https://${LUIS_endpoint}/luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/train`;
    
    const utterances = [
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
          ];
    
    const main = async() =>{
    
    
        await addUtterance();
        await train("POST");
        await trainStatus("GET");
    
    }
    const addUtterance = async () => {
    
        const options = {
            uri: addUtterancesURI,
            method: 'POST',
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: utterances
        };
    
        const response = await requestpromise(options)
        console.log(response.body);
    }
    const train = async (verb) => {
    
        const options = {
            uri: addTrainURI,
            method: verb, 
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: null // The body can be empty for a training request
        };
    
        const response = await requestpromise(options)
        console.log(response.body);
    }
    
    // MAIN
    main().then(() => console.log("done")).catch((err)=> console.log(err returned));
    ```
1. Nahraďte následující hodnoty:

    * `YOUR-KEY` pomocí počátečního klíče
    * `YOUR-ENDPOINT` s vaším koncovým bodem, například `westus2.api.cognitive.microsoft.com`
    * `YOUR-APP-ID` s ID vaší aplikace

1. Pomocí příkazového řádku ve stejném adresáři, ve kterém jste vytvořili soubor, zadejte následující příkaz, který soubor spustí:

    ```console
    node model.js
    ```  

## <a name="luis-keys"></a>Klíče služby LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto rychlým startem hotovi, odstraňte soubor ze systému souborů. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Osvědčené postupy pro aplikaci](../luis-concept-best-practices.md)