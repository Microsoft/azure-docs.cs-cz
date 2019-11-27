---
title: Získání záměru pomocí volání REST v Pythonu
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 37249cc560d4493c34dd4be6139de03f9c152a08
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414624"
---
## <a name="prerequisites"></a>Požadavky

* [Python 3.6](https://www.python.org/downloads/) nebo novější.
* [Visual Studio Code](https://code.visualstudio.com/)
* ID veřejné aplikace: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="get-luis-key"></a>Získání klíče LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="get-intent-from-the-prediction-endpoint"></a>Získat záměr z koncového bodu předpovědi

Použijte Python k dotazování [koncového bodu předpovědi](https://aka.ms/luis-apim-v3-prediction) a získejte výsledek předpovědi.

1. Zkopírujte tento fragment kódu do souboru s názvem `predict.py`:

    ```python
    ########### Python 3.6 #############
    import requests
    
    try:
    
        key = 'YOUR-KEY'
        endpoint = 'YOUR-ENDPOINT' # such as 'westus2.api.cognitive.microsoft.com' 
        appId = 'df67dcdb-c37d-46af-88e1-8b97951ca1c2'
        utterance = 'turn on all lights'
    
        headers = {
        }
    
        params ={
            'query': utterance,
            'timezoneOffset': '0',
            'verbose': 'true',
            'show-all-intents': 'true',
            'spellCheck': 'false',
            'staging': 'false',
            'subscription-key': key
        }
    
        r = requests.get(f'https://{endpoint}/luis/prediction/v3.0/apps/{appId}/slots/production/predict',headers=headers, params=params)
        print(r.json())
    
    except Exception as e:
        print(f'{e}')
    ```

1. Nahraďte následující hodnoty:

    * `YOUR-KEY` pomocí počátečního klíče.
    * `YOUR-ENDPOINT` s vaším koncovým bodem. Například, `westus2.api.cognitive.microsoft.com`.

1. Nainstalujte závislost `requests`. Slouží k provádění požadavků HTTP:

    ```console
    pip install requests
    ```

1. Spusťte skript pomocí tohoto příkazu konzoly:

    ```console
    python predict.py
    ``` 

1. Zkontrolujte odpověď předpovědi, která se vrátí jako JSON:

    ```console
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    Tady je odpověď JSON formátovanou pro čitelnost: 

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

## <a name="luis-keys"></a>Klíče služby LUIS

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto rychlým startem hotovi, odstraňte soubor ze systému souborů. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidat projevy a vlak](../get-started-get-model-rest-apis.md)