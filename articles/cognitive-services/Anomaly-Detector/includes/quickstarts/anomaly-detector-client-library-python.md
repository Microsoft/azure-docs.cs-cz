---
title: Rychlý Start knihovny pro klienta Python detektoru anomálií
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 98f68af11cf21cb795e7741585e55c195c066995
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "91024972"
---
Začněte s klientskou knihovnou anomálií pro Python. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. Služba detektoru anomálií umožňuje v datech časových řad najít neobvyklé typy, a to díky tomu, že automaticky používá nejvhodnější modely, bez ohledu na obor, scénář nebo objem dat.

Použijte klientskou knihovnu detektoru anomálií pro Python:

* Detekovat anomálie v celé sadě dat časových řad jako dávkový požadavek
* Zjistit stav anomálií z nejnovějšího datového bodu v časové řadě
* Zjištění bodů změny trendu v datové sadě.

[Referenční dokumentace ke knihovně](https://go.microsoft.com/fwlink/?linkid=2090370)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector)  |  [Balíček (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/)  |  [Vyhledání ukázkového kódu na GitHubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>Požadavky

* [Python 3.x](https://www.python.org/)
* [Knihovna analýzy dat PANDAS](https://pandas.pydata.org/)
* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" vytvořte prostředek pro detekci anomálií "  target="_blank"> vytvořením prostředku detektoru anomálií <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Počkejte na nasazení a klikněte na tlačítko **Přejít k prostředku** .
    * K připojení aplikace k rozhraní API detektoru anomálií budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.


## <a name="setting-up"></a>Nastavení

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

 Vytvořte nový soubor Pythonu a importujte následující knihovny.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Vytvořte proměnné pro klíč jako proměnnou prostředí, cestu k datovému souboru časové řady a umístění Azure vašeho předplatného. Například, `westus2`.

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Po instalaci Pythonu můžete nainstalovat klientskou knihovnu pomocí nástroje:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="object-model"></a>Objektový model

Klient detektoru anomálií je objekt [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) , který se ověřuje v Azure pomocí vašeho klíče. Klient může provést detekci anomálií celou datovou sadu pomocí [entire_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)nebo na nejnovějším datovém bodu pomocí [Last_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). Funkce [ChangePointDetectAsync](https://go.microsoft.com/fwlink/?linkid=2090370) detekuje body, které označují změny trendu.

Data časové řady se odesílají jako řada [bodů](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point?view=azure-python) v objektu [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request?view=azure-python) . `Request`Objekt obsahuje vlastnosti pro popis dat (například[členitosti](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) ) a parametrů pro detekci anomálií.

Odezva detektoru anomálií je objekt [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python), [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python)nebo [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090370) v závislosti na použité metodě.

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny pro detekci anomálií pro Python:

* [Ověření klienta](#authenticate-the-client)
* [Načtení datové sady časových řad ze souboru](#load-time-series-data-from-a-file)
* [Detekovat anomálie v celé sadě dat](#detect-anomalies-in-the-entire-data-set)
* [Zjistit stav anomálií nejnovějšího datového bodu](#detect-the-anomaly-status-of-the-latest-data-point)
* [Zjistit body změny v sadě dat](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Ověření klienta

Přidejte do koncového bodu proměnnou umístění Azure a ověřte klienta s klíčem.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Načtení dat časových řad ze souboru

Stáhněte si ukázková data pro tento rychlý Start z [GitHubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. V prohlížeči klikněte pravým tlačítkem na **raw**.
2. Klikněte na **Uložit odkaz jako**.
3. Uložte soubor do adresáře aplikace jako soubor. csv.

Tato data časové řady jsou formátována jako soubor. csv a budou odeslána do rozhraní API detektoru anomálií.

Načtěte datový soubor pomocí metody knihovny PANDAS `read_csv()` a vytvořte prázdnou proměnnou seznamu pro uložení datové řady. Iterujte soubor a připojovat data jako objekt [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point?view=azure-python) . Tento objekt bude obsahovat časové razítko a číselnou hodnotu z řádků datového souboru. csv.

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Vytvořte objekt [žádosti](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request?view=azure-python) s časovou řadou a [členitost](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (nebo periodicita) svých datových bodů. Například, `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Detekovat anomálie v celé sadě dat

Zavolejte rozhraní API pro detekci anomálií prostřednictvím celých dat časových řad pomocí metody [entire_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) klienta. Uložte vrácený objekt [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) . Iterujte v seznamu odpovědí `is_anomaly` a vytiskněte index všech `true` hodnot. Tyto hodnoty odpovídají indexu datových bodů neobvyklé, pokud byly nalezeny.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjistit stav anomálií nejnovějšího datového bodu

Zavolejte rozhraní API pro detekci anomálií, abyste zjistili, jestli je váš nejnovější datový bod anomálií pomocí metody [last_detect ()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) klienta a uložte vrácený objekt [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) . `is_anomaly`Hodnota odpovědi je logická hodnota, která určuje stav anomálie tohoto bodu.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="detect-change-points-in-the-data-set"></a>Zjištění bodů změny v sadě dat

Zavolejte rozhraní API pro detekci změn bodů v datech časové řady pomocí metody [detect_change_point ()](https://go.microsoft.com/fwlink/?linkid=2090370) klienta. Uložte vrácený objekt [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090370) . Iterujte v seznamu odpovědí `is_change_point` a vytiskněte index všech `true` hodnot. Tyto hodnoty odpovídají indexům bodů změny trendů, pokud byly nalezeny.

[!code-python[detect change points](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=changePointDetection)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `python` příkazu a názvu souboru.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
