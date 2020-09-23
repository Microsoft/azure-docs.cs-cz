---
title: 'Rychlý Start: zjištění anomálií jako dávky pomocí REST API detektoru anomálií a Pythonu'
titleSuffix: Azure Cognitive Services
description: Rozhraní API pro detekci anomálií vám umožní detekovat anomálie v datové řadě buď jako dávku, nebo na streamovaná data v tomto rychlém startu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 7bfe10ea5e0e95bcabf02243bb8b7172a5aec08d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906751"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-python"></a>Rychlý Start: zjištění anomálií v datech časových řad pomocí REST API detektoru anomálií a Pythonu

Tento rychlý Start vám umožní začít používat dva režimy zjišťování rozhraní API pro detekci anomálií ke zjištění anomálií v datech časových řad. Tato aplikace Python odesílá požadavky rozhraní API obsahující data časových řad ve formátu JSON a získá odpovědi.

| Požadavek rozhraní API                                        | Výstup aplikace                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Zjištění anomálií jako dávky                        | Odpověď JSON obsahující stav anomálie (a další data) pro každý datový bod v datech časové řady a pozice všech zjištěných anomálií. |
| Zjistit stav anomálií nejnovějšího datového bodu | Odpověď JSON obsahující stav anomálie (a další data) pro poslední datový bod v datech časové řady.|
| Zjištění bodů změn, které označují nové trendy dat | Odpověď JSON obsahující zjištěné body změny v datech časové řady |

 I když je tato aplikace napsaná v Pythonu, je rozhraní API webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tento rychlý Start najdete na [GitHubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/python-detect-anomalies.py).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
- Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" vytvořte prostředek pro detekci anomálií "  target="_blank"> vytvořením prostředku detektoru anomálií <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Počkejte na nasazení a klikněte na tlačítko **Přejít k prostředku** .
    - K připojení aplikace k rozhraní API detektoru anomálií budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
- [Python 2. x nebo 3. x](https://www.python.org/downloads/)
- [Knihovna požadavků](https://pypi.org/project/requests/) pro Python

- Soubor JSON, který obsahuje datové body časové řady. Ukázková data pro tento rychlý Start najdete na [GitHubu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. Vytvořte nový soubor Python a přidejte následující importy.

    [!code-python[import statements](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=imports)]

2. Vytvořte proměnné pro svůj klíč předplatného a koncový bod. Níže jsou uvedeny identifikátory URI, které lze použít pro detekci anomálií. Ty se připojí ke koncovému bodu služby později a vytvoří adresy URL žádostí o rozhraní API.

    |Metoda detekce  |Identifikátor URI  |
    |---------|---------|
    |Zjišťování dávky    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Zjišťování nejnovějšího datového bodu     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    | Detekce bodu změny | `/anomalydetector/v1.0/timeseries/changepoint/detect`   |

    [!code-python[initial endpoint and key variables](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=vars)]

3. V datovém souboru JSON si přečtěte jeho otevřením a použitím `json.load()` .

    [!code-python[Open JSON file and read in the data](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=fileLoad)]

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce pro odesílání požadavků

1. Vytvořte novou funkci s názvem `send_request()` , která přijímá proměnné vytvořené výše. Pak proveďte následující kroky.

2. Vytvořte slovník pro hlavičky požadavku. Nastavte na `Content-Type` a `application/json` přidejte svůj klíč předplatného do `Ocp-Apim-Subscription-Key` záhlaví.

3. Odešlete požadavek pomocí `requests.post()` . Zkombinujte adresu URL koncového bodu a detekce anomálií pro úplnou adresu URL požadavku a zahrňte do nich vaše hlavičky a data požadavku JSON. A potom odpověď vraťte.

    [!code-python[request method](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=request)]

## <a name="detect-anomalies-as-a-batch"></a>Zjištění anomálií jako dávky

1. Vytvořte metodu volanou `detect_batch()` pro detekci anomálií v rámci dat jako dávku. Zavolejte `send_request()` metodu vytvořenou výše pomocí vašeho koncového bodu, adresy URL, klíče předplatného a dat JSON.

2. Zavolejte `json.dumps()` na výsledek a naformátujte ho a vytiskněte ho do konzoly.

3. Pokud odpověď obsahuje `code` pole, vytiskněte kód chyby a chybovou zprávu.

4. V opačném případě najděte pozice anomálií v datové sadě. `isAnomaly`Pole odpovědi obsahuje logickou hodnotu, která se vztahuje na to, zda je daný datový bod anomálií. Iterujte v seznamu a vytiskněte index všech `True` hodnot. Tyto hodnoty odpovídají indexu datových bodů neobvyklé, pokud byly nalezeny.

    [!code-python[detection as a batch](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjistit stav anomálií nejnovějšího datového bodu

1. Vytvořte metodu s názvem `detect_latest()` k určení, zda je poslední datový bod v časové řadě anomálií. Zavolejte `send_request()` výše uvedenou metodu s vaším koncovým bodem, adresou URL, klíčem předplatného a data JSON.

2. Zavolejte `json.dumps()` na výsledek a naformátujte ho a vytiskněte ho do konzoly.

    [!code-python[Latest point detection](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectLatest)]

## <a name="detect-change-points-in-the-data"></a>Zjištění bodů změny v datech

1. Vytvořte metodu volanou `detect_change_point()` pro detekci anomálií v rámci dat jako dávku. Zavolejte `send_request()` metodu vytvořenou výše pomocí vašeho koncového bodu, adresy URL, klíče předplatného a dat JSON.

2. Zavolejte `json.dumps()` na výsledek a naformátujte ho a vytiskněte ho do konzoly.

3. Pokud odpověď obsahuje `code` pole, vytiskněte kód chyby a chybovou zprávu.

4. V opačném případě najděte pozice anomálií v datové sadě. `isChangePoint`Pole odpovědi obsahuje logickou hodnotu označující, zda je daný datový bod anomálií. Iterujte v seznamu a vytiskněte index všech `True` hodnot. Tyto hodnoty odpovídají indexům bodů změny trendu, pokud byly nalezeny.

    [!code-python[detect change points](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=detectChangePoint)]

## <a name="send-the-request"></a>Odeslat žádost

Zavolejte metody detekce anomálií vytvořené výše.

[!code-python[Method calls](~/samples-anomaly-detector/quickstarts/python-detect-anomalies.py?name=methodCalls)]

### <a name="example-response"></a>Příklad odpovědi

Ve formátu JSON se vrátí úspěšná odpověď. Kliknutím na následující odkazy zobrazíte odpověď JSON na GitHubu:
* [Příklad odpovědi na zjišťování dávky](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Příklad odpovědi na nejnovější zjištění bodu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)
* [Ukázka odpovědi na detekci bodu změny](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/change-point-sample.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
