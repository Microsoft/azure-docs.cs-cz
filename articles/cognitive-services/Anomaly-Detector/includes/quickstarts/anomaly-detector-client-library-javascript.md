---
title: Rychlý start klientské knihovny JavaScript javascriptového detektoru anomálií
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2020
ms.author: aahi
ms.openlocfilehash: dca07f37377880008160fa3521d66ed4f6afc084
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759983"
---
Začínáme s klientskou knihovnou Detektoranolií pro JavaScript. Následujícím postupem nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly. Služba Detektor anomálií umožňuje najít abnormality v datech časových řad automaticky pomocí nejvhodnějších modelů, bez ohledu na odvětví, scénář nebo objem dat.

Pomocí klientské knihovny Detektor anomálií pro JavaScript:

* Detekce anomálií v celé datové sadě časových řad jako dávkový požadavek
* Zjištění stavu anomálií nejnovějšího datového bodu v časových řadách

[Referenční dokumentace](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/?view=azure-node-latest) | [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | [Package (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-anomalydetector) | [Najít kód na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/AnomalyDetector)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Aktuální verze [souboru Node.js](https://nodejs.org/)
* Klíč a koncový bod detektoru anomálií

## <a name="setting-up"></a>Nastavení

### <a name="create-an-anomaly-detector-azure-resource"></a>Vytvoření prostředku Azure detektoru anomálií

[!INCLUDE [anomaly-detector-resource-creation](../../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo Bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spuštěním `npm init` příkazu vytvořte aplikaci `package.json` uzlu se souborem. 

```console
npm init
```

Vytvořte soubor `index.js` s názvem a importujte následující knihovny:

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Vytvořte proměnné koncového bodu a klíče Azure vašeho prostředku. Pokud jste vytvořili proměnnou prostředí po spuštění aplikace, budete muset zavřít a znovu otevřít editor, IDE nebo prostředí, které ji spustilo, abyste měli přístup k proměnné. Vytvořte další proměnnou pro ukázkový datový soubor, který stáhnete v pozdějším kroku, a prázdný seznam datových bodů. Pak vytvořte objekt, `ApiKeyCredentials` který bude obsahovat klíč.

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Nainstalujte `ms-rest-azure` `azure-cognitiveservices-anomalydetector` balíčky a balíčky NPM. Knihovna csv-parse se také používá v tomto rychlém startu:

```console
npm install  @azure/cognitiveservices-anomalydetector @azure/ms-rest-js csv-parse
```

`package.json` Soubor aplikace bude aktualizován závislostmi.

## <a name="object-model"></a>Objektový model

Správce anomálií je objekt [AnomalyDetectorClient,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) který se ověřuje v Azure pomocí vašeho klíče. Klient poskytuje dvě metody detekce anomálií: Na celou datovou sadu pomocí [wholeDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--)a na nejnovější datový bod pomocí [LastDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-). 

Data časových řad jsou odesílána jako řada [bodů](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) v objektu [Požadavku.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest) Objekt `Request` obsahuje vlastnosti popisující data (například[rozlišovací schopnost)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity) a parametry pro detekci anomálií. 

Odpověď detektoru anomálií je [lastdetectresponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) nebo [wholedetectresponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) objekt v závislosti na použité metodě. 

## <a name="code-examples"></a>Příklady kódu 

Tyto fragmenty kódu ukazují, jak provést následující akce s klientskou knihovnou Detektor anomálií pro soubor Node.js:

* [Ověření klienta](#authenticate-the-client)
* [Načtení datové sady časových řad ze souboru](#load-time-series-data-from-a-file)
* [Detekce anomálií v celé sadě dat](#detect-anomalies-in-the-entire-data-set) 
* [Zjištění stavu anomálií nejnovějšího datového bodu](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci objektu [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) s koncovým bodem a přihlašovacími údaji.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Načtení dat časových řad ze souboru

Stáhněte si ukázková data pro tento rychlý start z [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv):
1. V prohlížeči klepněte pravým tlačítkem myši na **položku Nerejzovní**.
2. Klepněte na **tlačítko Uložit odkaz jako**.
3. Uložte soubor do adresáře aplikace jako soubor .csv.

Tato data časové řady jsou formátována jako soubor .csv a budou odeslána do rozhraní API detektoru anomálií.

Přečtěte si datový soubor pomocí metody knihovny `readFileSync()` csv-parse `parse()`a analyzujte soubor pomocí aplikace . Pro každý řádek stiskněte [point](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) objekt obsahující časové razítko a číselnou hodnotu.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Detekce anomálií v celé sadě dat 

Volání rozhraní API pro detekci anomálií v celé časové řady jako dávka s celou metodu [zjištění()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) klienta. Uložte vrácený objekt [WholeDetectResponse.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) Iterate prostřednictvím `isAnomaly` seznamu odpovědí a vytisknout `true` index všech hodnot. Tyto hodnoty odpovídají indexu anomálních datových bodů, pokud byly nalezeny.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjištění stavu anomálií nejnovějšího datového bodu

Volání rozhraní API detektoru anomálií k určení, pokud váš nejnovější datový bod je anomálie pomocí metody [lastDetect()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) klienta a uložit vrácený [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) objektu. `isAnomaly` Hodnota odpovědi je logická hodnota, která určuje stav anomálií tohoto bodu.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `node` v souboru quickstart.

```console
node index.js
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
