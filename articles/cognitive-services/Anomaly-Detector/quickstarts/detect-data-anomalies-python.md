---
title: 'Úvodní příručka: Detekce anomálií jako dávky pomocí rozhraní REST API a Pythonu detektoru anomálií'
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API detektoru anomálií zjišťujte abnormality v datových řadách jako dávku nebo na streamovaných datech v tomto rychlém startu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 684aba561dc50b64dd7cc564cff8e55229ce1429
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239031"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Úvodní příručka: Detekce anomálií v datech časových řad pomocí rozhraní REST API a Pythonu detektoru anomálií

Pomocí tohoto rychlého startu můžete začít používat dva režimy detekce rozhraní API detektoru anomálií k detekci anomálií v datech časových řad. Tato aplikace Pythonu odešle dva požadavky rozhraní API obsahující data časových řad ve formátu JSON a získá odpovědi.

| Požadavek na rozhraní API                                        | Výstup aplikace                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detekce anomálií jako dávky                        | Odpověď JSON obsahující stav anomálie (a další data) pro každý datový bod v datech časových řad a pozice všech zjištěných anomálií. |
| Zjištění stavu anomálií nejnovějšího datového bodu | Odpověď JSON obsahující stav anomálie (a další data) pro nejnovější datový bod v datech časové řady.                                                                                                                                         |

 Zatímco tato aplikace je napsána v Pythonu, API je restful webová služba kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tento rychlý start najdete na [GitHubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/python-detect-anomalies.py).

## <a name="prerequisites"></a>Požadavky

- [Python 2.x nebo 3.x](https://www.python.org/downloads/)
- Klíč a koncový bod detektoru anomálií
- [Knihovna Požadavků](https://pypi.org/project/requests/) pro python

- Soubor JSON obsahující datové body časových řad. Ukázková data pro tento rychlý start lze nalézt na [GitHubu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Vytvoření prostředku detektoru anomálií

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]


## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. Vytvořte nový soubor pythonu a přidejte následující importy.

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. Vytvořte proměnné pro klíč předplatného a koncový bod. Níže jsou identifikátory URI, které můžete použít pro detekci anomálií. Ty budou později připojeny ke koncovému bodu služby, aby se vytvořily adresy URL požadavků rozhraní API.

    |Metoda detekce  |Identifikátor URI  |
    |---------|---------|
    |Detekce dávky    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Detekce na nejnovějším datovém bodu     | `/anomalydetector/v1.0/timeseries/last/detect`        |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. Přečtěte si v datovém souboru JSON jeho otevřením a použitím `json.load()`.

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce pro odesílání požadavků

1. Vytvořte novou `send_request()` funkci s názvem, která přebírá proměnné vytvořené výše. Poté proveďte následující kroky.

2. Vytvořte slovník pro hlavičky požadavku. Nastavte `Content-Type` na `application/json`a přidejte klíč `Ocp-Apim-Subscription-Key` předplatného do záhlaví.

3. Odešlete `requests.post()`požadavek pomocí aplikace . Zkombinujte adresu URL detekce koncových bodů a anomálií pro úplnou adresu URL požadavku a zahrňte záhlaví a data požadavků json. A pak vrátit odpověď.

    [!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Detekce anomálií jako dávky

1. Vytvořte metodu volanou `detect_batch()` ke zjištění anomálií v rámci dat jako dávka. Zavolejte `send_request()` výše vytvořenou metodu pomocí koncového bodu, adresy URL, klíče předplatného a dat json.

2. Volání `json.dumps()` na výsledek naformátovat a vytisknout do konzoly.

3. Pokud odpověď `code` obsahuje pole, vytiskněte kód chyby a chybovou zprávu.

4. V opačném případě najděte pozice anomálií v datové sadě. `isAnomaly` Pole odpovědi obsahuje logickou hodnotu vztahující se k tomu, zda je daný datový bod anomálií. Iterate prostřednictvím seznamu a vytisknout `True` index všech hodnot. Tyto hodnoty odpovídají indexu anomálních datových bodů, pokud byly nalezeny.

    [!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjištění stavu anomálií nejnovějšího datového bodu

1. Vytvořte metodu volanou `detect_latest()` k určení, zda nejnovější datový bod v časové řadě je anomálie. Zavolejte `send_request()` výše uvedenou metodu s koncovým bodem, adresou URL, klíčem předplatného a daty json. 

2. Volání `json.dumps()` na výsledek naformátovat a vytisknout do konzoly.

    [!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="send-the-request"></a>Odeslat žádost

Zavolejte výše vytvořené metody detekce anomálií.

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>Příklad odpovědi

Úspěšná odpověď je vrácena ve formátu JSON. Kliknutím na níže uvedené odkazy zobrazíte odpověď JSON na GitHubu:
* [Příklad odpovědi detekce dávky](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Příklad nejnovější odpovědi na detekci bodů](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
