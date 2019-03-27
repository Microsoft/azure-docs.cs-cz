---
title: 'Rychlý start: Detekuje anomálie jako batch pomocí rozhraní REST API služby detekce anomálií a Python | Dokumentace Microsoftu'
description: Zjištění anomálií v datových řad v dávce nebo na streamovaných datech pomocí rozhraní API detekce anomálií.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 60307d51439b4474c8be4f040792c03a6f83b0fd
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58473159"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Rychlý start: Detekovat anomálie ve vašich datech časových řad pomocí rozhraní REST API služby detekce anomálií a Pythonu

V tomto rychlém startu můžete začít používat rozhraní API detekce anomálií dva režimy zjišťování zjišťovat anomálie ve vašich datech časových řad. Tuto aplikaci v Pythonu odešle dva požadavky rozhraní API, obsahující data ve formátu JSON časových řad a získá odpovědi.

| Žádosti na rozhraní API                                        | Výstup aplikace                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Zjišťovat anomálie v dávce                        | Odpověď JSON, který obsahuje stav anomálií (a další data) pro každý datový bod v datech časových řad a pozice jakékoli detekované anomálie. |
| Zjištění anomálií stavu nejnovější datového bodu | Odpověď JSON, který obsahuje stav anomálií (a další data) pro poslední datový bod v datech časové řady.                                                                                                                                         |

 Zatímco tato aplikace je napsaný v Pythonu, je rozhraní API RESTful webová služba, která je kompatibilní s Většina programovacích jazyků.

## <a name="prerequisites"></a>Požadavky

- [Python 2.x nebo 3.x](https://www.python.org/downloads/)

- [Požadavky knihovny](http://docs.python-requests.org) pro python

- Odkazuje JSON soubor obsahující data časových řad. Ukázková data pro účely tohoto rychlého startu můžete najít na [Githubu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]


## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. V oblíbeném textovém editoru nebo prostředí IDE vytvořte nový soubor pythonu. Přidejte následující importy.

    ```python
    import requests
    import json
    ```

2. Vytváření proměnných pro váš klíč předplatného a váš koncový bod. V následující tabulce jsou identifikátory URI, můžete použít k detekci anomálií. Ty se připojí k vašeho koncového bodu služby později k vytvoření rozhraní API pro adresy URL požadavků.

    |Metoda detekce  |Identifikátor URI  |
    |---------|---------|
    |Zjišťování služby batch    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Zjišťování na nejnovější datového bodu     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    ```python
    batch_detection_url = "/anomalydetector/v1.0/timeseries/entire/detect"
    latest_point_detection_url = "/anomalydetector/v1.0/timeseries/last/detect"

    endpoint = "[YOUR_ENDPOINT_URL]"
    subscription_key = "[YOUR_SUBSCRIPTION_KEY]"
    data_location = "[PATH_TO_TIME_SERIES_DATA]"
    ```

3. Přečtěte si v datovém souboru JSON, otevřením a použitím `json.load()`. 

    ```python
    file_handler = open(data_location)
    json_data = json.load(file_handler)
    ```

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce k odesílání požadavků

1. Vytvořit novou funkci s názvem `send_request()` , která přebírá proměnných vytvořené výše. Pak proveďte následující kroky.

2. Vytvořte slovník pro záhlaví požadavku. Nastavte `Content-Type` k `application/json`a přidat váš klíč předplatného na `Ocp-Apim-Subscription-Key` záhlaví.

3. Odeslat žádost o prostřednictvím `requests.post()`. Kombinace váš koncový bod a adresa URL zjišťování anomálií úplný adresa URL požadavku a zahrnout záhlaví a data žádosti json. 

4. Pokud je žádost úspěšná, vrátí odpověď.  
    
    ```python
    def send_request(endpoint, url, subscription_key, request_data):
        headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
        response = requests.post(endpoint+url, data=json.dumps(request_data), headers=headers)
        if response.status_code == 200:
            return json.loads(response.content.decode("utf-8"))
        else:
            print(response.status_code)
            raise Exception(response.text)
    ```

## <a name="detect-anomalies-as-a-batch"></a>Zjišťovat anomálie v dávce

1. Vytvořit metodu nazvanou `detect_batch()` zjišťovat anomálie v celém data v dávce. Volání `send_request()` metoda vytvořené výše s koncovým bodem, adresa url, klíč předplatného a dat json. 

2. Volání `json.dumps()` na výsledek, který má naformátování a vypíše do konzoly.

3. Vyhledání pozice anomálie v datové sadě. Odpověď na `isAnomaly` pole obsahuje hodnotu typu boolean týkající se určuje, zda je daný datový bod anomálie. Iteraci v rámci seznamu a vytisknout index žádné `True` hodnoty. Tyto hodnoty odpovídat indexu neobvyklé datových bodů, pokud některá.

```python
def detect_batch(request_data):
    print("Detecting anomalies as a batch")
    result = send_request(endpoint, batch_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))

    # Find and display the positions of anomalies in the data set
    anomalies = result["isAnomaly"]
    print("Anomalies detected in the following data positions:")
    for x in range(len(anomalies)):
        if anomalies[x] == True:
            print (x)
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjištění anomálií stavu nejnovější datového bodu

1. Vytvořit metodu nazvanou `detect_latest()` k určení, zda je poslední datový bod v časové řadě anomálie. Volání `send_request()` metoda nad s koncovým bodem, adresa url, klíč předplatného a dat json. 

2. Volání `json.dumps()` na výsledek, který má naformátování a vypíše do konzoly.

```python
def detect_latest(request_data):
    print("Determining if latest data point is an anomaly")
    # send the request, and print the JSON result
    result = send_request(endpoint, latest_point_detection_url, subscription_key, request_data)
    print(json.dumps(result, indent=4))
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Načtení dat časových řad a odeslat žádost

1. Načíst data JSON časových řad otevřete popisovač souboru a pomocí `json.load()` na něj. Potom anomálii volání metody detekce vytvořili výše.
    
    ```python
    file_handler = open (data_location)
    json_data = json.load(file_handler)
    
    detect_batch(json_data)
    detect_latest(json_data)
    ```

### <a name="example-response"></a>Příklad odpovědi

Úspěšná odpověď se vrátí ve formátu JSON. Kliknutím na odkazy níže zobrazíte odpověď JSON na Githubu:
* [Příklad odpovědi zjišťování služby batch](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Příklad nejnovější bod zjišťování odpovědi](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)