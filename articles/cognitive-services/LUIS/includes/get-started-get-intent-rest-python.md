---
title: Získání záměru pomocí volání REST v Pythonu
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 23ac98f91c989c9bedb6b91e6a7ce26dc164ac5a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987755"
---
## <a name="prerequisites"></a>Požadavky

* [Python 3.6](https://www.python.org/downloads/) nebo novější.
* [Visual Studio Code](https://code.visualstudio.com/)
* ID veřejné aplikace: `df67dcdb-c37d-46af-88e1-8b97951ca1c2`

## <a name="create-luis-runtime-key-for-predictions"></a>Vytvoření klíče LUIS runtime pro předpovědi

1. Přihlaste se k [Azure Portal](https://portal.azure.com)
1. Klikněte na [vytvořit **Language Understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Zadejte všechna požadovaná nastavení pro klíč **za běhu** :

    |Nastavení|Hodnota|
    |--|--|
    |Name (Název)|Požadovaný název (2-64 znaků)|
    |Předplatné|Vyberte odpovídající předplatné.|
    |Umístění|Výběr libovolného okolí a dostupného umístění|
    |Cenová úroveň|`F0` – minimální cenová úroveň|
    |Skupina prostředků|Vyberte dostupnou skupinu prostředků.|

1. Klikněte na **vytvořit** a počkejte na vytvoření prostředku. Po vytvoření přejděte na stránku prostředků.
1. Shromážděte nakonfigurované `endpoint` a `key`.

## <a name="get-intent-from-the-prediction-endpoint"></a>Získat záměr z koncového bodu předpovědi

Použijte Python k dotazování [koncového bodu předpovědi](https://aka.ms/luis-apim-v3-prediction) a získejte výsledek předpovědi.

1. Zkopírujte tento fragment kódu do souboru s názvem `predict.py`:

    ```python
    ########### Python 3.6 #############
    import requests

    try:

        key = 'YOUR-KEY' # your Runtime key
        endpoint = 'YOUR-ENDPOINT' # such as 'your-resource-name.api.cognitive.microsoft.com'
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

1. Hodnoty `YOUR-KEY` a `YOUR-ENDPOINT` nahraďte vlastním klíčovým a koncovým bodem **prostředí** pro předpověď.

    |Informace|Účel|
    |--|--|
    |`YOUR-KEY`|Klíč **běhu** pro předpověď znaků 32.|
    |`YOUR-ENDPOINT`| Koncový bod adresy URL předpovědi Například, `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s tímto rychlým startem hotovi, odstraňte soubor ze systému souborů.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidat projevy a vlak](../get-started-get-model-rest-apis.md)