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
ms.openlocfilehash: f4d180dd6ad99d5bc00e6970e22b756aa26275da
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76268298"
---
## <a name="prerequisites"></a>Požadavky

* Počáteční klíč.
* Importujte aplikaci [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) z úložiště GitHub-Services-Language-porozumění.
* ID aplikace LUIS pro importovanou aplikaci TravelAgent ID aplikace je uvedené na řídicím panelu aplikace.
* ID verze v rámci aplikace, která přijímá projevy. Výchozí ID je 0.1.
* [Python 3.6](https://www.python.org/downloads/) nebo novější.
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Soubor JSON s ukázkovými promluvami

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="get-luis-key"></a>Získání klíče LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]


## <a name="change-model-programmatically"></a>Programové změny modelu

Pomocí možnosti přejít přidejte do aplikace [rozhraní API](https://aka.ms/luis-apim-v3-authoring) náročné na počítač. 

1. Vytvořte nový soubor s názvem `model.py`. Přidejte následující kód:

    ```python
    ########### Python 3.6 #############
    import requests
    
    # Starter key
    LUIS_authoringKey  = "YOUR-KEY"
    
    LUIS_APP_ID = "YOUR-APP-ID"
    
    # Authoring endpoint, example: westus.api.cognitive.microsoft.com
    LUIS_ENDPOINT = "YOUR-ENDPOINT"

    # The version number of your LUIS app
    LUIS_APP_VERSION = "0.1"
    
    URI_AddUtterances = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_VERSION}/examples'
    URI_Train = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_VERSION}/train'
    
    HEADERS = {'Ocp-Apim-Subscription-Key': LUIS_authoringKey}
    
    def addUtterances():
        r = requests.post(URI_AddUtterances,headers=HEADERS)
        print(r.json())
    
    def train():
        r = requests.post(URI_Train,headers=HEADERS)
        print(r.json())
    
    def trainStatus():
        r = requests.get(URI_Train,headers=HEADERS)
        print(r.json())
    
    addUtterances()
    train()
    trainStatus()
    ```
1. Nahraďte následující hodnoty:

    * `YOUR-KEY` pomocí počátečního klíče
    * `YOUR-ENDPOINT` s vaším koncovým bodem, například `westus2.api.cognitive.microsoft.com`
    * `YOUR-APP-ID` s ID vaší aplikace

1. Pomocí příkazového řádku ve stejném adresáři, ve kterém jste vytvořili soubor, zadejte následující příkaz, který soubor spustí:

    ```console
    python model.py
    ```  

## <a name="luis-keys"></a>Klíče služby LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto rychlým startem hotovi, odstraňte soubor ze systému souborů. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Osvědčené postupy pro aplikaci](../luis-concept-best-practices.md)
