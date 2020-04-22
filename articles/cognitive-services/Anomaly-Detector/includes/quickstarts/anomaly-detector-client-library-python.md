---
title: Rychlý start klientské knihovny Pythonu detektoru anomálií
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2020
ms.author: aahi
ms.openlocfilehash: a51025245e5b02b89126afd886bc89fb1cefdef4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759990"
---
Začínáme s klientskou knihovnou Detektoranolií pro Python. Následujícím postupem nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly. Služba Detektor anomálií umožňuje najít abnormality v datech časových řad automaticky pomocí nejvhodnějších modelů, bez ohledu na odvětví, scénář nebo objem dat.

Pomocí klientské knihovny Detektoranolií pro Python:

* Detekce anomálií v celé datové sadě časových řad jako dávkový požadavek
* Zjištění stavu anomálií nejnovějšího datového bodu v časových řadách

[Referenční dokumentace](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [knihovny Balíček zdrojového kódu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [knihovny (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | Najít[ukázkový kód na GitHubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/python-sdk-sample.py)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Klíč a koncový bod detektoru anomálií
* [Python 3.x](https://www.python.org/)
* [Knihovna analýzy dat Pandy](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Nastavení

### <a name="create-an-anomaly-detector-resource"></a>Vytvoření prostředku detektoru anomálií

[!INCLUDE [anomaly-detector-resource-creation](../../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace pythonu

 Vytvořte nový soubor Pythonu a importujte následující knihovny.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Vytvořte proměnné pro klíč jako proměnnou prostředí, cestu k datovému souboru časové řady a umístění azure vašeho předplatného. Například, `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Po instalaci Pythonu můžete nainstalovat klientskou knihovnu pomocí:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Objektový model

Zákazník detektoru anomálií je objekt [AnomalyDetectorClient,](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) který se ověřuje v Azure pomocí vašeho klíče. Klient poskytuje dvě metody detekce anomálií: Na celou datovou sadu pomocí [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)a na nejnovější datový bod pomocí [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Data časových řad jsou odesílána jako řada [bodů](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) v objektu [Požadavku.](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) Objekt `Request` obsahuje vlastnosti popisující data (například[rozlišovací schopnost)](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) a parametry pro detekci anomálií. 

Odpověď detektoru anomálií je [lastdetectresponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) nebo [wholedetectresponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) objekt v závislosti na použité metodě. 

## <a name="code-examples"></a>Příklady kódu 

Tyto fragmenty kódu ukazují, jak provést následující akce s klientskou knihovnou Detektoranolií pro Python:

* [Ověření klienta](#authenticate-the-client)
* [Načtení datové sady časových řad ze souboru](#load-time-series-data-from-a-file)
* [Detekce anomálií v celé sadě dat](#detect-anomalies-in-the-entire-data-set) 
* [Zjištění stavu anomálií nejnovějšího datového bodu](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Ověření klienta

Přidejte proměnnou umístění azure do koncového bodu a ověřte klienta pomocí klíče.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Načtení dat časových řad ze souboru

Stáhněte si ukázková data pro tento rychlý start z [GitHubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. V prohlížeči klepněte pravým tlačítkem myši na **položku Nerejzovní**.
2. Klepněte na **tlačítko Uložit odkaz jako**.
3. Uložte soubor do adresáře aplikace jako soubor .csv.

Tato data časové řady jsou formátována jako soubor .csv a budou odeslána do rozhraní API detektoru anomálií.

Načtěte datový soubor `read_csv()` metodou knihovny Pandas a vytvořte prázdnou proměnnou seznamu pro uložení datové řady. Iterate prostřednictvím souboru a připojit data jako [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) objektu. Tento objekt bude obsahovat časové razítko a číselnou hodnotu z řádků datového souboru .csv. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Vytvořte [request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) objekt s časové řady a [rozlišovací schopnost](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (nebo periodicita) jeho datových bodů. Například, `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Detekce anomálií v celé sadě dat 

Volání rozhraní API pro detekci anomálií prostřednictvím dat celé časové řady pomocí metody [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) klienta. Uložte vrácený objekt [WholeDetectResponse.](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) Iterate prostřednictvím `is_anomaly` seznamu odpovědí a vytisknout `true` index všech hodnot. Tyto hodnoty odpovídají indexu anomálních datových bodů, pokud byly nalezeny.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjištění stavu anomálií nejnovějšího datového bodu

Volání rozhraní API detektoru anomálií k určení, pokud váš nejnovější datový bod je anomálie pomocí metody [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) klienta a uložit vrácený [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) objektu. `is_anomaly` Hodnota odpovědi je logická hodnota, která určuje stav anomálií tohoto bodu.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `python` a názvu souboru.
 
[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
