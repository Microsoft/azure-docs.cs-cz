---
title: 'Rychlý Start: zjištění anomálií v datech časových řad pomocí REST API detektoru anomáliíC#'
titleSuffix: Azure Cognitive Services
description: Rozhraní API pro detekci anomálií vám umožní detekovat anomálie v datové řadě buď jako dávku, nebo při streamování pomocí tohoto rychlého startu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: aahi
ms.openlocfilehash: b5fb8bb424af47eb7793d38f24b6334677c6a5ea
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75385305"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Rychlý Start: zjištění anomálií v datech časových řad pomocí REST API detektoru anomáliíC# 

Tento rychlý Start vám umožní začít používat dva režimy zjišťování rozhraní API pro detekci anomálií ke zjištění anomálií v datech časových řad. Tato C# aplikace posílá dvě požadavky rozhraní API obsahující data časových řad ve formátu JSON a získá odpovědi.

| Požadavek rozhraní API                                        | Výstup aplikace                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Zjištění anomálií jako dávky                        | Odpověď JSON obsahující stav anomálie (a další data) pro každý datový bod v datech časové řady a pozice všech zjištěných anomálií. |
| Zjistit stav anomálií nejnovějšího datového bodu | Odpověď JSON obsahující stav anomálie (a další data) pro poslední datový bod v datech časové řady.                                        |

 I když je tato aplikace napsaná v C#, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tento rychlý Start najdete na [GitHubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs).

## <a name="prerequisites"></a>Požadavky

- Libovolná edice sady [Visual Studio 2017 nebo novější](https://visualstudio.microsoft.com/downloads/),
- Klíč a koncový bod detektoru anomálií
- Rozhraní [Json.NET](https://www.newtonsoft.com/json), k dispozici jako balíček NuGet. Postup instalace Newtonsoft. JSON jako balíčku NuGet v aplikaci Visual Studio:
    
    1. Klikněte pravým tlačítkem na projekt v **Průzkumník řešení**.
    2. Vyberte **Spravovat balíčky NuGet**.
    3. Vyhledejte *Newtonsoft. JSON* a nainstalujte balíček.

- Pokud používáte Linux/MacOS, můžete tuto aplikaci spustit pomocí [mono](https://www.mono-project.com/).

- Soubor JSON, který obsahuje datové body časové řady. Ukázková data pro tento rychlý Start najdete na [GitHubu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Vytvoření prostředku detektoru anomálií

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. V aplikaci Visual Studio vytvořte nové řešení konzoly a přidejte následující balíčky. 

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. Vytvořte proměnné pro svůj klíč předplatného a koncový bod. Níže jsou uvedeny identifikátory URI, které lze použít pro detekci anomálií. Ty se připojí ke koncovému bodu služby později a vytvoří adresy URL žádostí o rozhraní API.

    | Metoda detekce                   | Identifikátor URI                                              |
    |------------------------------------|--------------------------------------------------|
    | Zjišťování dávky                    | `/anomalydetector/v1.0/timeseries/entire/detect` |
    | Zjišťování nejnovějšího datového bodu | `/anomalydetector/v1.0/timeseries/last/detect`   |
        
    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce pro odesílání požadavků

1. Vytvořte novou asynchronní funkci nazvanou `Request`, která přebírá proměnné vytvořené výše.

2. Pomocí objektu `HttpClient` nastavte informace o protokolu zabezpečení a hlavičce klienta. Nezapomeňte do hlavičky `Ocp-Apim-Subscription-Key` přidat svůj klíč předplatného. Pak vytvořte objekt `StringContent` pro požadavek.

3. Odešlete žádost pomocí `PostAsync()`a potom odpověď vraťte.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Zjištění anomálií jako dávky

1. Vytvořte novou funkci nazvanou `detectAnomaliesBatch()`. Sestavte požadavek a odešlete ho voláním funkce `Request()` s vaším koncovým bodem, klíčem předplatného, adresou URL pro detekci anomálií služby Batch a daty časových řad.

2. Deserializovat objekt JSON a zapsat ho do konzoly.

3. Pokud odpověď obsahuje `code` pole, vytiskněte kód chyby a chybovou zprávu. 

4. V opačném případě najděte pozice anomálií v datové sadě. Pole odpovědi `isAnomaly` obsahuje pole logických hodnot, z nichž každý označuje, zda je datový bod anomálií. Převeďte tuto hodnotu na pole řetězců s funkcí `ToObject<bool[]>()` objektu Response. Iterujte v poli a vytiskněte index všech `true`ch hodnot. Tyto hodnoty odpovídají indexu datových bodů neobvyklé, pokud byly nalezeny.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjistit stav anomálií nejnovějšího datového bodu

1. Vytvořte novou funkci nazvanou `detectAnomaliesLatest()`. Sestavte požadavek a odešlete ho voláním funkce `Request()` s vaším koncovým bodem, klíčem předplatného, adresou URL pro detekci anomálií posledního bodu a daty časové řady.

2. Deserializovat objekt JSON a zapsat ho do konzoly.

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]
 
## <a name="load-your-time-series-data-and-send-the-request"></a>Načtěte data časové řady a odešlete žádost.

1. V metodě Main vaší aplikace načtěte data časové řady JSON pomocí `File.ReadAllText()`. 

2. Zavolejte funkce detekce anomálií vytvořené výše. Pomocí `System.Console.ReadKey()` ponechejte okno konzoly otevřené po spuštění aplikace.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Příklad odpovědi

Ve formátu JSON se vrátí úspěšná odpověď. Kliknutím na následující odkazy zobrazíte odpověď JSON na GitHubu:
* [Příklad odpovědi na zjišťování dávky](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Příklad odpovědi na nejnovější zjištění bodu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
