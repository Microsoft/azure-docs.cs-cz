---
title: 'Rychlý Start: zjištění anomálií dat pomocí klientské knihovny detekce anomálií pro Python'
titleSuffix: Azure Cognitive Services
description: Rozhraní API pro detekci anomálií vám umožní detekovat anomálie v datové řadě buď jako dávku, nebo na streamovaná data v tomto rychlém startu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: b8cbf69aa0788b3b8e0130396f192cb5bc045f3d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448892"
---
# <a name="quickstart-anomaly-detector-client-library-for-nodejs"></a>Rychlý Start: Klientská knihovna detektoru anomálií pro Node. js

Začněte s klientskou knihovnou anomálií pro Node. js. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. Služba detektoru anomálií umožňuje v datech časových řad najít neobvyklé typy, a to díky tomu, že automaticky používá nejvhodnější modely, bez ohledu na obor, scénář nebo objem dat.

Pomocí klientské knihovny anomálií detektoru pro Node. js:

* Detekovat anomálie v celé datové sadě časových řad jako požadavek dávky
* Zjistit stav anomálií z nejnovějšího datového bodu v časové řadě

[Referenční dokumentace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/?view=azure-node-latest) |  | balíčku [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) [(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-anomalydetector) | [Najít kód na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/AnomalyDetector)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Aktuální verze [Node. js](https://nodejs.org/)
* Klíč a koncový bod detektoru anomálií

## <a name="setting-up"></a>Nastavení

### <a name="create-an-anomaly-detector-azure-resource"></a>Vytvoření prostředku Azure detektoru anomálií

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spusťte příkaz `npm init` pro vytvoření aplikace uzlu se souborem `package.json`. 

```console
npm init
```

Vytvořte soubor s názvem `index.js` a importujte následující knihovny:

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Vytvořte proměnné koncového bodu a klíče Azure prostředku. Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, budete muset zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k této proměnné. Vytvořte další proměnnou pro ukázkový datový soubor, který budete stahovat v pozdějším kroku, a prázdný seznam pro datové body. Pak vytvořte objekt `ApiKeyCredentials`, který bude obsahovat klíč.

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Nainstalujte balíčky `ms-rest-azure` a `azure-cognitiveservices-anomalydetector` NPM. Knihovna CSV-Parse se používá také v tomto rychlém startu:

```console
npm install  @azure/cognitiveservices-anomalydetector ms-rest-azure csv-parse
```

Soubor `package.json` vaší aplikace bude aktualizován pomocí závislostí.

## <a name="object-model"></a>Objektový model

Klient detektoru anomálií je objekt [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) , který se ověřuje v Azure pomocí vašeho klíče. Klient nabízí dvě metody detekce anomálií: na celé datové sadě pomocí [entireDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--)a na nejnovějším datovém bodu pomocí [LastDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-). 

Data časové řady se odesílají jako řada [bodů](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) v objektu [Request](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest) . `Request` Objekt obsahuje vlastnosti pro popis dat a parametrů pro detekci anomálií.[(například členitosti)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity) 

Odezva detektoru anomálií je objekt [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) nebo [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) v závislosti na použité metodě. 

## <a name="code-examples"></a>Příklady kódu 

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny pro detekci anomálií pro Node. js:

* [Ověření klienta](#authenticate-the-client)
* [Načtení datové sady časových řad ze souboru](#load-time-series-data-from-a-file)
* [Detekovat anomálie v celé sadě dat](#detect-anomalies-in-the-entire-data-set) 
* [Zjistit stav anomálií nejnovějšího datového bodu](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci objektu [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) s vaším koncovým bodem a přihlašovacími údaji.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Načtení dat časových řad ze souboru

Stáhněte si ukázková data pro tento rychlý start z [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv):
1. V prohlížeči klikněte pravým tlačítkem na **raw**.
2. Klikněte na **Uložit odkaz jako**.
3. Uložte soubor do adresáře aplikace jako soubor. csv.

Tato data časové řady jsou formátována jako soubor. csv a budou odeslána do rozhraní API detektoru anomálií.

Přečtěte si datový soubor pomocí `readFileSync()` metody knihovny CSV-Parse a analyzujte soubor pomocí `parse()`. Pro každý řádek nasaďte objekt [Point](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) obsahující časové razítko a číselnou hodnotu.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Detekovat anomálie v celé sadě dat 

Zavolejte rozhraní API pro detekci anomálií prostřednictvím celé časové řady jako dávky s metodou [entireDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) klienta. Uložte vrácený objekt [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) . Iterujte pomocí seznamu `isAnomaly` odpovědi a vytiskněte index všech hodnot `true`. Tyto hodnoty odpovídají indexu datových bodů neobvyklé, pokud byly nalezeny.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjistit stav anomálií nejnovějšího datového bodu

Zavolejte rozhraní API pro detekci anomálií, abyste zjistili, jestli je váš nejnovější datový bod anomálií pomocí metody [lastDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) klienta, a uložte vrácený objekt [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) . Hodnota `isAnomaly` odpovědi je logická hodnota, která určuje stav anomálie tohoto bodu.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `node` v souboru rychlého startu.

```console
node index.js
```

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
