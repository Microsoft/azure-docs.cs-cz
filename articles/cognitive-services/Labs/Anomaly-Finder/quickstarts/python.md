---
title: Jak používat rozhraní API pro vyhledávač anomálií s využitím Pythonu – Microsoft Cognitive Services | Dokumentace Microsoftu
description: Získat informace a ukázky kódu můžete rychle začít používat vyhledávač anomálií na Pythonu ve službě Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: eb64bb4daece44d33775dca7417c51b216e04614
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49455432"
---
# <a name="use-the-anomaly-finder-api-with-python"></a>Vyhledávač anomálií rozhraní API pomocí Pythonu

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Tento článek obsahuje informace a ukázky kódu, které vám pomohou rychle začít používat rozhraní API pro vyhledávač anomálií na Python k provedení úlohy získání výsledku anomálií dat časové řady.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-python"></a>Získání bodů anomálií s využitím Python API vyhledávač anomálií 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Příklad dat časových řad

Následuje příklad času datových bodů řady.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-python-example"></a>Analýza dat a získat příklad v Pythonu body anomálií

Ujistěte se, že máte nainstalovanou pythonu3 a pak vytvořte spustitelný soubor pythonu s názvem detect.py. V detect.py měli byste zahrnout kód uvedený níže. Před spuštěním kódu, nezapomeňte nahradit `[YOUR_SUBSCRIPTION_KEY]` hodnotu klíčem platné předplatné.
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

Úspěšná odpověď se vrátí ve formátu JSON. Ukázková odpověď vypadá takto.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Aplikace v Pythonu](../tutorials/python-tutorial.md)
