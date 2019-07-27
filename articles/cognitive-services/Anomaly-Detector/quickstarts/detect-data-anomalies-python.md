---
title: 'Rychlý start: Zjištění anomálií jako dávky pomocí REST API detektoru anomálií a Pythonu'
titleSuffix: Azure Cognitive Services
description: Rozhraní API pro detekci anomálií použijte k detekci anomálií v datové řadě buď jako dávku, nebo na streamovaná data.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: aahi
ms.openlocfilehash: f40f1b94b3e7c2732fd8bed0bc6e503277b533c3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565817"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Rychlý start: Detekce anomálií v datech časových řad pomocí REST API detektoru anomálií a Pythonu

Tento rychlý Start vám umožní začít používat dva režimy zjišťování rozhraní API pro detekci anomálií ke zjištění anomálií v datech časových řad. Tato aplikace Python odesílá dvě požadavky rozhraní API obsahující data časových řad ve formátu JSON a získá odpovědi.

| Požadavek rozhraní API                                        | Výstup aplikace                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Zjištění anomálií jako dávky                        | Odpověď JSON obsahující stav anomálie (a další data) pro každý datový bod v datech časové řady a pozice všech zjištěných anomálií. |
| Zjistit stav anomálií nejnovějšího datového bodu | Odpověď JSON obsahující stav anomálie (a další data) pro poslední datový bod v datech časové řady.                                                                                                                                         |

 I když je tato aplikace napsaná v Pythonu, je rozhraní API webová služba RESTful kompatibilní s většinou programovacích jazyků.

## <a name="prerequisites"></a>Požadavky

- [Python 2. x nebo 3. x](https://www.python.org/downloads/)

- [Knihovna požadavků](http://docs.python-requests.org) pro Python

- Soubor JSON, který obsahuje datové body časové řady. Ukázková data pro tento rychlý Start najdete na [GitHubu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. V oblíbeném textovém editoru nebo integrovaném vývojovém prostředí vytvořte nový soubor Pythonu. Přidejte následující importy.

    ```python
    import requests
    import json
    ```

2. Vytvořte proměnné pro svůj klíč předplatného a koncový bod. Níže jsou uvedeny identifikátory URI, které lze použít pro detekci anomálií. Ty se připojí ke koncovému bodu služby později a vytvoří adresy URL žádostí o rozhraní API.

    |Metoda detekce  |Identifikátor URI  |
    |---------|---------|
    |Zjišťování dávky    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Zjišťování nejnovějšího datového bodu     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. V datovém souboru JSON si přečtěte jeho otevřením a `json.load()`použitím.

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce pro odesílání požadavků

1. Vytvořte novou funkci s názvem `send_request()` , která přijímá proměnné vytvořené výše. Pak proveďte následující kroky.

2. Vytvořte slovník pro hlavičky požadavku. Nastavte na a přidejte`Ocp-Apim-Subscription-Key` svůj klíč předplatného do záhlaví. `Content-Type` `application/json`

3. Odešlete požadavek pomocí `requests.post()`. Zkombinujte adresu URL koncového bodu a detekce anomálií pro úplnou adresu URL požadavku a zahrňte do nich vaše hlavičky a data požadavku JSON. A potom odpověď vraťte.

```python
def send_request(endpoint, url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json',
               'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(
        endpoint+url, data=json.dumps(request_data), headers=headers)
    return json.loads(response.content.decode("utf-8"))
```

## <a name="detect-anomalies-as-a-batch"></a>Zjištění anomálií jako dávky

1. Vytvořte metodu volanou `detect_batch()` pro detekci anomálií v rámci dat jako dávku. `send_request()` Zavolejte metodu vytvořenou výše pomocí vašeho koncového bodu, adresy URL, klíče předplatného a dat JSON.

2. Zavolejte `json.dumps()` na výsledek a naformátujte ho a vytiskněte ho do konzoly.

3. Pokud odpověď obsahuje `code` pole, vytiskněte kód chyby a chybovou zprávu.

4. V opačném případě najděte pozice anomálií v datové sadě. `isAnomaly` Pole odpovědi obsahuje logickou hodnotu, která se vztahuje na to, zda je daný datový bod anomálií. Iterujte v seznamu a vytiskněte index všech `True` hodnot. Tyto hodnoty odpovídají indexu datových bodů neobvyklé, pokud byly nalezeny.

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url,
                          subscription_key, request_data)
    print(json.dumps(result, indent=4))

    if result.get('code') != None:
        print("Detection failed. ErrorCode:{}, ErrorMessage:{}".format(
            result['code'], result['message']))
    else:
        # Find and display the positions of anomalies in the data set
        anomalies = result["isAnomaly"]
        print("Anomalies detected in the following data positions:")
        for x in range(len(anomalies)):
            if anomalies[x] == True:
                print(x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjistit stav anomálií nejnovějšího datového bodu

1. Vytvořte metodu s názvem `detect_latest()` k určení, zda je poslední datový bod v časové řadě anomálií. Zavolejte výše uvedenou metodu s vaším koncovým bodem, adresou URL, klíčem předplatného a data JSON. `send_request()` 

2. Zavolejte `json.dumps()` na výsledek a naformátujte ho a vytiskněte ho do konzoly.

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url,
                          subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Načtěte data časové řady a odešlete žádost.

1. Načtěte data časové řady JSON a otevřete obslužnou rutinu souboru a `json.load()` použijte ji. Pak zavolejte metody detekce anomálií vytvořené výše.

```python
file_handler = open(data_location)
json_data = json.load(file_handler)

detect_batch(json_data)
detect_latest(json_data)
```

### <a name="example-response"></a>Příklad odpovědi

Ve formátu JSON se vrátí úspěšná odpověď. Kliknutím na následující odkazy zobrazíte odpověď JSON na GitHubu:
* [Příklad odpovědi na zjišťování dávky](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Příklad odpovědi na nejnovější zjištění bodu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
