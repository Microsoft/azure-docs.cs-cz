---
title: 'Získání modelu pomocí volání REST v jazyce C #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/18/2020
ms.author: diberry
ms.openlocfilehash: 19f72dbb62fc2084bf0c9609fb3782e083c911af
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655486"
---
## <a name="prerequisites"></a>Požadavky

* Azure Language Understanding – vytváření znaků a vytváření kódu URL koncového bodu prostředku 32. Vytvořte pomocí [Azure Portal](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) nebo [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importujte aplikaci [Pizza](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json) z `Azure-Samples/cognitive-services-sample-data-files` úložiště GitHub.
* ID aplikace LUIS pro importovanou aplikaci Pizza ID aplikace je uvedené na řídicím panelu aplikace.
* ID verze aplikace, která přijímá promluvy.
* Programovací jazyk [Node.js](https://nodejs.org/)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Soubor JSON s ukázkovými promluvami

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="create-the-nodejs-project"></a>Vytvoření projektu Node. js

1. Vytvořte novou složku pro uložení projektu Node. js, například `node-model-with-rest` .

1. Otevřete nový příkazový řádek, přejděte do složky, kterou jste vytvořili, a spusťte následující příkaz:

    ```console
    npm init
    ```

    Stisknutím klávesy ENTER u každé výzvy přijměte výchozí nastavení.

1. Pomocí následujícího příkazu nainstalujte Modul Request-příslib:

    ```console
    npm install --save request-promise
    ```

## <a name="change-model-programmatically"></a>Programové změny modelu

1. Vytvořte nový soubor s názvem `model.js`. Přidejte následující kód:

    ```javascript
    var request = require('request-promise');

    //////////
    // Values to modify.

    // YOUR-APP-ID: The App ID GUID found on the www.luis.ai Application Settings page.
    const LUIS_appId = "YOUR-APP-ID";

    // YOUR-AUTHORING-KEY: Your LUIS authoring key, 32 character value.
    const LUIS_authoringKey = "YOUR-AUTHORING-KEY";

    // YOUR-AUTHORING-ENDPOINT: Replace this with your authoring key endpoint.
    // For example, "https://your-resource-name.api.cognitive.microsoft.com/"
    const LUIS_endpoint = "YOUR-AUTHORING-ENDPOINT";

    // NOTE: Replace this your version number. The Pizza app uses a version number of "0.1".
    const LUIS_versionId = "0.1";
    //////////

    const addUtterancesURI = `${LUIS_endpoint}luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/examples`;
    const addTrainURI = `${LUIS_endpoint}luis/authoring/v3.0-preview/apps/${LUIS_appId}/versions/${LUIS_versionId}/train`;

    const utterances = [
        {
            'text': 'order a pizza',
            'intentName': 'ModifyOrder',
            'entityLabels': [
                {
                    'entityName': 'Order',
                    'startCharIndex': 6,
                    'endCharIndex': 12
                }
            ]
        },
        {
            'text': 'order a large pepperoni pizza',
            'intentName': 'ModifyOrder',
            'entityLabels': [
                {
                    'entityName': 'Order',
                    'startCharIndex': 6,
                    'endCharIndex': 28
                },
                {
                    'entityName': 'FullPizzaWithModifiers',
                    'startCharIndex': 6,
                    'endCharIndex': 28
                },
                {
                    'entityName': 'PizzaType',
                    'startCharIndex': 14,
                    'endCharIndex': 28
                },
                {
                    'entityName': 'Size',
                    'startCharIndex': 8,
                    'endCharIndex': 12
                }
            ]
        },
        {
            'text': 'I want two large pepperoni pizzas on thin crust',
            'intentName': 'ModifyOrder',
            'entityLabels': [
                {
                    'entityName': 'Order',
                    'startCharIndex': 7,
                    'endCharIndex': 46
                },
                {
                    'entityName': 'FullPizzaWithModifiers',
                    'startCharIndex': 7,
                    'endCharIndex': 46
                },
                {
                    'entityName': 'PizzaType',
                    'startCharIndex': 17,
                    'endCharIndex': 32
                },
                {
                    'entityName': 'Size',
                    'startCharIndex': 11,
                    'endCharIndex': 15
                },
                {
                    'entityName': 'Quantity',
                    'startCharIndex': 7,
                    'endCharIndex': 9
                },
                {
                    'entityName': 'Crust',
                    'startCharIndex': 37,
                    'endCharIndex': 46
                }
            ]
        }
    ];

    // Main function.
    const main = async() =>{

        await addUtterances(utterances);
        await train("POST");
        await train("GET");

    }

    // Adds the utterances to the model.
    const addUtterances = async (utterances) => {

        const options = {
            uri: addUtterancesURI,
            method: 'POST',
            headers: {
                'Ocp-Apim-Subscription-Key': LUIS_authoringKey
            },
            json: true,
            body: utterances
        };

        const response = await request(options)
        console.log("addUtterance:\n" + JSON.stringify(response, null, 2));
    }

    // With verb === "POST", sends a training request.
    // With verb === "GET", obtains the training status.
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

        const response = await request(options)
        console.log("train " + verb + ":\n" + JSON.stringify(response, null, 2));
    }

    // MAIN
    main().then(() => console.log("done")).catch((err)=> console.log(err));
    ```

1. Nahraďte hodnoty začínající `YOUR-` vlastními hodnotami.

    |Informace|Účel|
    |--|--|
    |`YOUR-APP-ID`| Vaše ID aplikace LUIS |
    |`YOUR-AUTHORING-KEY`|Klíč pro vytváření znaků 32.|
    |`YOUR-AUTHORING-ENDPOINT`| Váš koncový bod adresy URL pro vytváření Například, `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`. Název prostředku se nastaví při vytváření prostředku.|

    Přiřazené klíče a prostředky jsou zobrazené na portálu LUIS v části Správa na stránce **prostředky Azure** . ID aplikace je k dispozici ve stejné části pro správu na stránce **nastavení aplikace** .

1. Na příkazovém řádku zadejte následující příkaz pro spuštění projektu:

    ```console
    node model.js
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto rychlým startem hotovi, odstraňte složku projektu ze systému souborů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Osvědčené postupy pro aplikaci](../luis-concept-best-practices.md)