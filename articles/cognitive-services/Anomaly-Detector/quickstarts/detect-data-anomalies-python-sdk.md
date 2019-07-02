---
title: 'Rychlý start: Detekovat anomálie dat pomocí knihovny detekce anomálií a Python | Dokumentace Microsoftu'
titleSuffix: Azure Cognitive Services
description: Zjištění anomálií v datových řad v dávce nebo na streamovaných datech pomocí rozhraní API detekce anomálií.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 07/01/2019
ms.author: aahi
ms.openlocfilehash: 1d89ed8f40547142d41af9c587fc8fc000fa4dd9
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503672"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>Rychlý start: Anomálií detektor klientské knihovny pro Python

Začínáme s detekce anomálií Klientská knihovna pro .NET. Postupujte podle následujících kroků instalace balíčku a vyzkoušejte si ukázkový kód pro základní úlohy. Služba detekce anomálií vám umožňuje automaticky pomocí nejlepšího těsně modelů, bez ohledu na odvětví, scénáři nebo objem dat vyhledat anomálie v daty časových řad.

## <a name="key-concepts"></a>Klíčové koncepty

Použití detekce anomálií klientské knihovny pro Python k:

* Detekuje anomálie v celé vaší datové sadě řady čas jako dávkového požadavku
* Zjištění anomálií stav poslední datový bod v časové řadě

[Referenční dokumentace knihovny](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [balíčku (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [ukázky](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořit zdarma](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* [Knihovny analýz dat Pandas](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Nastavení

### <a name="create-an-anomaly-detector-resource"></a>Vytvořte prostředek detekce anomálií

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="install-the-client-library"></a>Nainstalujte klientské knihovny

Po instalaci jazyka Python, můžete nainstalovat klientské knihovny s:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Objektový model

Je klient detekce anomálií [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) objekt, který ověřuje do Azure pomocí klíče. Klient poskytuje dvě metody pro detekci anomálií: Na objekt pro celou datovou sadu pomocí [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-)a na nejnovější data bodu pomocí [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Data časových řad se odešle jako řadu [body](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) v [žádosti](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python) objektu. `Request` Objekt obsahuje vlastností, které popisují data ([Členitosti](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) například) a parametrů pro detekci anomálií. 

Odpověď detekce anomálií [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) nebo [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) objekt v závislosti na metodě použít. 

## <a name="getting-started"></a>Začínáme

Vytvoření nové aplikace v Pythonu v preferovaného editoru nebo prostředí IDE. Pak přidejte následující deklarace importu do souboru. 

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

> [!NOTE]
> V tomto rychlém startu se předpokládá jste [vytvoří proměnnou prostředí](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) klíč detekce anomálií s názvem `ANOMALY_DETECTOR_KEY`.

Vytváření proměnných pro váš klíč jako proměnnou prostředí, cestu k času řady datový soubor a umístění vašeho předplatného azure. Například, `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

## <a name="code-examples"></a>Příklady kódu 

Tyto fragmenty kódu ukazují, jak provést následující pomocí detekce anomálií klientské knihovny pro .NET:

* [Ověření klienta](#authenticate-the-client)
* [Načtení času řady datové sady ze souboru](#load-time-series-data-from-a-file)
* [Zjišťovat anomálie v celé datové sady](#detect-anomalies-in-the-entire-data-set) 
* [Zjištění anomálií stavu nejnovější datového bodu](#detect-the-anomaly-status-of-the-latest-data-point)

### <a name="authenticate-the-client"></a>Ověření klienta

Přidat proměnnou vaše umístění azure do koncového bodu a ověřit váš klíč klienta.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

### <a name="load-time-series-data-from-a-file"></a>Načtení dat časových řad ze souboru

Stáhněte si ukázková data pro účely tohoto rychlého startu z [Githubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. V prohlížeči, klikněte pravým tlačítkem na **Raw**
2. Klikněte na tlačítko **uložit odkaz jako**
3. Uložte soubor do adresáře aplikace jako soubor .csv.

Tato data časových řad formátována jako soubor .csv a se odešlou do rozhraní API detekce anomálií.

Načíst soubor dat pomocí knihovny Pandas `read_csv()` metoda a ujistěte se, prázdným seznamem proměnnou pro uložení datové řady. Iterovat přes soubor a připojovat data jako [bodu](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python) objektu. Tento objekt bude obsahovat časové razítko a číselnou hodnotu z řádků souboru CSV s daty. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Vytvoření [žádosti](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) objekt s časové řady a [členitosti](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (nebo periodicita) jeho datových bodů. Například, `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

### <a name="detect-anomalies-in-the-entire-data-set"></a>Zjišťovat anomálie v celé datové sady 

Volání rozhraní API pro detekci anomálií prostřednictvím dat celého časových řad pomocí klienta [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) metody. Store vráceného [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) objektu. Iterovat přes odpovědi `is_anomaly` seznamu a vytisknout index žádné `true` hodnoty. Tyto hodnoty odpovídat indexu neobvyklé datových bodů, pokud některá.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

### <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjištění anomálií stavu nejnovější datového bodu

Volání rozhraní API detekce anomálií k určení, zda je poslední datový bod anomálií pomocí klienta [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) metoda a úložiště vráceného [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) objektu. Odpověď na `is_anomaly` je hodnota logická hodnota, která určuje stav anomálií tohoto bodu.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci v prostředí (IDE) nebo na příkazovém řádku se `python` příkazu a názvem souboru.
 
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků se odstraní také všechny další prostředky spojené s vybranou skupinou prostředků.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Můžete také spustit cloud shell příkaz k odebrání skupiny prostředků a její přidružené prostředky. To může trvat několik minut. 

```azurecli-interactive
az group delete --name example-anomaly-detector-resource-group
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
>[Streamování detekce anomálií pomocí služby Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* Co je [detekce anomálií rozhraní API?](../overview.md)
* [Osvědčené postupy](../concepts/anomaly-detection-best-practices.md) při použití rozhraní API detekce anomálií.
* Zdrojový kód pro tuto ukázku můžete najít na [Githubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
