---
title: Použití rozhraní API vyhledávací anomálií s Python - kognitivní služby Microsoft | Microsoft Docs
description: Get informace a ukázky kódu můžete rychle začít používat anomálií vyhledávací s Pythonem v kognitivní služby.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: c14916b0644edab613b298d6e71f8bbb9a6bb804
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343007"
---
# <a name="use-the-anomaly-finder-api-with-python"></a>Použití anomálií vyhledávací rozhraní API s Pythonem

Tento článek obsahuje informace a ukázky kódu, které vám pomohou rychle začít, pomocí rozhraní API vyhledávací anomálií Python k provedení úlohy získání anomálií výsledek pro data časové řady.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-python"></a>Získávání anomálií body s rozhraním API vyhledávací anomálií, které používá Python 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Příklad data časové řady

Následuje příklad času řadu datových bodů.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-python-example"></a>Analýza dat a získat příklad anomálií body Python

Zkontrolujte, zda jste nainstalovali python3 potom vytvořte python spustitelný soubor s názvem detect.py. V detect.py by měla obsahovat kód níže. Před provedením kód, nezapomeňte nahradit `[YOUR_SUBSCRIPTION_KEY]` hodnotu s klíč platné předplatné.
Nahraďte `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` s datovými body.

```python
import requests
import json


def detect(url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(url, data=json.dumps(request_data), headers=headers)
    if response.status_code == 200:
        return json.loads(response.content.decode("utf-8"))
    else:
        print(response.status_code)
        raise Exception(response.text)


sample_data = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]"
endpont = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
subscription_key = "[YOUR_SUBSCRIPTION_KEY]"

result = detect(endpont, subscription_key, sample_data)
print(result)

```

### <a name="example-response"></a>Příklad odpovědi

Úspěšná odpověď se vrátí ve formátu JSON. Ukázková odpověď je následující.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Aplikace Python](../tutorials/python-tutorial.md)
