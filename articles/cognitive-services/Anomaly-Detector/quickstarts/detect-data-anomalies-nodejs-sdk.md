---
title: 'Rychlý start: Zjištění anomálií dat pomocí klientské knihovny pro detekci anomálií pro Python'
titleSuffix: Azure Cognitive Services
description: Rozhraní API pro detekci anomálií použijte k detekci anomálií v datové řadě buď jako dávku, nebo na streamovaná data.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 09/17/2019
ms.author: aahi
ms.openlocfilehash: 320c690eb873f760af89b7514893f14ecc209323
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106957"
---
# <a name="quickstart-anomaly-detector-client-library-for-nodejs"></a>Rychlý start: Klientská knihovna detektoru anomálií pro Node. js

Začněte s klientskou knihovnou anomálií pro Node. js. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. Služba detektoru anomálií umožňuje v datech časových řad najít neobvyklé typy, a to díky tomu, že automaticky používá nejvhodnější modely, bez ohledu na obor, scénář nebo objem dat.

Pomocí klientské knihovny anomálií detektoru pro Node. js:

* Detekovat anomálie v celé datové sadě časových řad jako požadavek dávky
* Zjistit stav anomálií z nejnovějšího datového bodu v časové řadě

[](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/?view=azure-node-latest) | [Ukázky](https://github.com/Azure-Samples/anomalydetector) balíčku | [zdrojového kódu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector) | knihovny Referenční dokumentace[(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-anomalydetector)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Aktuální verze [Node. js](https://nodejs.org/)

## <a name="setting-up"></a>Nastavení

### <a name="create-an-anomaly-detector-azure-resource"></a>Vytvoření prostředku Azure detektoru anomálií

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro detektor anomálií pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo rozhraní příkazového [řádku Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) v místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu 7 dnů zdarma. Po registraci bude k dispozici na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Prohlédněte si prostředek na [Azure Portal](https://portal.azure.com/).

Po získání klíče ze zkušebního předplatného nebo prostředku vytvořte pro tento klíč [proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) s názvem `ANOMALY_DETECTOR_KEY`. Pak ho vytvořte pro koncový bod Azure s názvem `ANOMALY_DETECTOR_ENDPOINT`.
 
### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js

V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro vaši aplikaci a přejděte na něj. 

```console
mkdir myapp && cd myapp
```

Spuštěním příkazu vytvořte aplikaci uzlu `package.json` se souborem. `npm init` 

```console
npm init
```

Vytvořte soubor s názvem `index.js` a importujte následující knihovny:

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Vytvořte proměnné koncového bodu a klíče Azure prostředku. Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, budete muset zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k této proměnné. Vytvořte další proměnnou pro ukázkový datový soubor, který budete stahovat v pozdějším kroku. Pak vytvořte objekt ApiKeyCredentials, který bude obsahovat klíč.

[!code-javascript[Initial variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Nainstalujte balíčky `azure-cognitiveservices-anomalydetector`anpm. `ms-rest-azure` Knihovna CSV-Parse se používá také v tomto rychlém startu:

```console
npm install  @azure/cognitiveservices-anomalydetector ms-rest-azure csv-parse
```

`package.json` Soubor vaší aplikace bude aktualizován pomocí závislostí.

## <a name="object-model"></a>Objektový model

Klient detektoru anomálií je objekt [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) , který se ověřuje v Azure pomocí vašeho klíče. Klient nabízí dvě metody detekce anomálií: Pro celou datovou sadu pomocí [entireDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--servicecallback-entiredetectresponse--)a na nejnovějším datovém bodu pomocí [LastDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-). 

Data časové řady se odesílají jako řada [bodů](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) v objektu [Request](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest) . `Request` Objekt obsahuje vlastnosti pro popis dat a parametrů pro detekci anomálií.[(například členitosti)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/request?view=azure-node-latest#granularity) 

Odezva detektoru anomálií je objekt [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) nebo [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) v závislosti na použité metodě. 

## <a name="code-examples"></a>Příklady kódu 

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny pro detekci anomálií pro Node. js:

* [Ověření klienta](#authenticate-the-client)
* [Načtení datové sady časových řad ze souboru](#load-time-series-data-from-a-file)
* [Detekovat anomálie v celé sadě dat](#detect-anomalies-in-the-entire-data-set) 
* [Zjistit stav anomálií nejnovějšího datového bodu](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> V tomto rychlém startu se předpokládá, že jste [vytvořili proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíč detektoru anomálií s názvem `ANOMALY_DETECTOR_KEY`.

Vytvořte instanci objektu [AnomalyDetectorClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest) s vaším koncovým bodem a přihlašovacími údaji.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Načtení dat časových řad ze souboru

Stáhněte si ukázková data pro tento rychlý start z [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv):
1. V prohlížeči klikněte pravým tlačítkem na **raw**.
2. Klikněte na **Uložit odkaz jako**.
3. Uložte soubor do adresáře aplikace jako soubor. csv.

Tato data časové řady jsou formátována jako soubor. csv a budou odeslána do rozhraní API detektoru anomálií.

Přečtěte si datový soubor pomocí `readFileSync()` metody knihovny CSV-Parse a soubor Analyzujte pomocí. `parse()` Pro každý řádek nasaďte objekt [Point](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/point?view=azure-node-latest) obsahující časové razítko a číselnou hodnotu.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Detekovat anomálie v celé sadě dat 

Zavolejte rozhraní API pro detekci anomálií prostřednictvím celé časové řady jako dávky s metodou [entireDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#entiredetect-request--msrest-requestoptionsbase-) klienta. Uložte vrácený objekt [EntireDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse?view=azure-node-latest) . Iterujte v `isAnomaly` seznamu odpovědí a vytiskněte index všech `true` hodnot. Tyto hodnoty odpovídají indexu datových bodů neobvyklé, pokud byly nalezeny.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjistit stav anomálií nejnovějšího datového bodu

Zavolejte rozhraní API pro detekci anomálií, abyste zjistili, jestli je váš nejnovější datový bod anomálií pomocí metody [lastDetect ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient?view=azure-node-latest#lastdetect-request--msrest-requestoptionsbase-) klienta, a uložte vrácený objekt [LastDetectResponse](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse?view=azure-node-latest) . `isAnomaly` Hodnota odpovědi je logická hodnota, která určuje stav anomálie tohoto bodu.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `node` příkazu v souboru rychlého startu.

```console
node index.js
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Detekce anomálií streamování pomocí Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* Co je rozhraní API pro detekci [anomálií?](../overview.md)
* [Osvědčené postupy](../concepts/anomaly-detection-best-practices.md) při použití rozhraní API detektoru anomálií
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs)
