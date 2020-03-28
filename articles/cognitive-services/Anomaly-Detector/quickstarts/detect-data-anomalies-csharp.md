---
title: 'Úvodní příručka: Detekce anomálií v datech časových řad pomocí rozhraní REST API detektoru anomálií a C #'
titleSuffix: Azure Cognitive Services
description: Naučte se, jak pomocí rozhraní API detektoru anomálií zjistit abnormality v datových řadách jako dávku nebo na streamovaná data.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: b53fecad3655048a7b9d799134926b2730b16dae
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80239115"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Úvodní příručka: Detekce anomálií v datech časových řad pomocí rozhraní REST API detektoru anomálií a C # 

Pomocí tohoto rychlého startu můžete začít používat dva režimy detekce rozhraní API detektoru anomálií k detekci anomálií v datech časových řad. Tato aplikace Jazyka C# odešle dva požadavky rozhraní API obsahující data časových řad ve formátu JSON a získá odpovědi.

| Požadavek na rozhraní API                                        | Výstup aplikace                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Detekce anomálií jako dávky                        | Odpověď JSON obsahující stav anomálie (a další data) pro každý datový bod v datech časových řad a pozice všech zjištěných anomálií. |
| Zjištění stavu anomálií nejnovějšího datového bodu | Odpověď JSON obsahující stav anomálie (a další data) pro nejnovější datový bod v datech časové řady.                                        |

 Zatímco tato aplikace je napsána v jazyce C#, ROZHRANÍ API je restful webová služba kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tento rychlý start najdete na [GitHubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs).

## <a name="prerequisites"></a>Požadavky

- Jakékoli vydání [sady Visual Studio 2017 nebo novější](https://visualstudio.microsoft.com/downloads/)
- Klíč a koncový bod detektoru anomálií
- Rozhraní [Json.NET](https://www.newtonsoft.com/json), k dispozici jako balíček NuGet. Instalace Newtonsoft.Json jako balíčku NuGet v sadě Visual Studio:
    
    1. Klikněte pravým tlačítkem myši na projekt v **Průzkumníku řešení**.
    2. Vyberte **spravovat nugetové balíčky**.
    3. Vyhledejte *Newtonsoft.Json* a nainstalujte balíček.

- Pokud používáte Linux/MacOS, lze tuto aplikaci spustit pomocí [mono](https://www.mono-project.com/).

- Soubor JSON obsahující datové body časových řad. Ukázková data pro tento rychlý start lze nalézt na [GitHubu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Vytvoření prostředku detektoru anomálií

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. V sadě Visual Studio vytvořte nové řešení konzoly a přidejte následující balíčky. 

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. Vytvořte proměnné pro klíč předplatného a koncový bod. Níže jsou identifikátory URI, které můžete použít pro detekci anomálií. Ty budou později připojeny ke koncovému bodu služby, aby se vytvořily adresy URL požadavků rozhraní API.

    | Metoda detekce                   | Identifikátor URI                                              |
    |------------------------------------|--------------------------------------------------|
    | Detekce dávky                    | `/anomalydetector/v1.0/timeseries/entire/detect` |
    | Detekce na nejnovějším datovém bodu | `/anomalydetector/v1.0/timeseries/last/detect`   |
        
    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce pro odesílání požadavků

1. Vytvořte novou asynchronní funkci s názvem, `Request` která přebírá výše vytvořené proměnné.

2. Nastavte protokol zabezpečení klienta a informace `HttpClient` záhlaví pomocí objektu. Nezapomeňte do `Ocp-Apim-Subscription-Key` záhlaví přidat klíč předplatného. Pak vytvořte `StringContent` objekt pro požadavek.

3. Odešlete `PostAsync()`požadavek s , a potom vrátit odpověď.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Detekce anomálií jako dávky

1. Vytvořte novou `detectAnomaliesBatch()`funkci nazvanou . Vytvořte požadavek a odešlete jej voláním `Request()` funkce s koncovým bodem, klíčem předplatného, adresou URL pro detekci dávkových anomálií a daty časových řad.

2. Deserializujte objekt JSON a zapište jej do konzoly.

3. Pokud odpověď `code` obsahuje pole, vytiskněte kód chyby a chybovou zprávu. 

4. V opačném případě najděte pozice anomálií v datové sadě. `isAnomaly` Pole odpovědi obsahuje pole logických hodnot, z nichž každá označuje, zda je datový bod anomálií. Převeďte to na pole řetězců `ToObject<bool[]>()` s funkcí objektu odpovědi. Iterate prostřednictvím pole a vytisknout `true` index všechny hodnoty. Tyto hodnoty odpovídají indexu anomálních datových bodů, pokud byly nalezeny.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjištění stavu anomálií nejnovějšího datového bodu

1. Vytvořte novou `detectAnomaliesLatest()`funkci nazvanou . Vytvořte požadavek a odešlete jej voláním `Request()` funkce s koncovým bodem, klíčem předplatného, adresou URL pro detekci nejnovějších point anomaly a daty časových řad.

2. Deserializujte objekt JSON a zapište jej do konzoly.

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]
 
## <a name="load-your-time-series-data-and-send-the-request"></a>Načtěte data časových řad a odešlete požadavek

1. V hlavní metodě aplikace načtěte data časových `File.ReadAllText()`řad JSON pomocí aplikace . 

2. Volání funkce detekce anomálií vytvořené výše. Slouží `System.Console.ReadKey()` k zachování okna konzoly otevřené po spuštění aplikace.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Příklad odpovědi

Úspěšná odpověď je vrácena ve formátu JSON. Kliknutím na níže uvedené odkazy zobrazíte odpověď JSON na GitHubu:
* [Příklad odpovědi detekce dávky](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Příklad nejnovější odpovědi na detekci bodů](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
