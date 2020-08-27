---
title: 'Rychlý Start: zjištění anomálií v datech časových řad pomocí REST API detektoru anomálií a C #'
titleSuffix: Azure Cognitive Services
description: Naučte se používat rozhraní API pro detekci anomálií ke zjištění anomálií v datové řadě buď jako dávku, nebo na streamovaná data.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 06/30/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: a364588d77fb24e96c831ce541c5bb4e63d93e98
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88922340"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Rychlý Start: zjištění anomálií v datech časových řad pomocí REST API detektoru anomálií a C #

Tento rychlý Start vám umožní začít používat dva režimy zjišťování rozhraní API pro detekci anomálií ke zjištění anomálií v datech časových řad. Tato aplikace v jazyce C# odesílá dvě požadavky rozhraní API obsahující data časových řad ve formátu JSON a získává odpovědi.

| Požadavek rozhraní API                                        | Výstup aplikace                                                                                                                                         |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Zjištění anomálií jako dávky                        | Odpověď JSON obsahující stav anomálie (a další data) pro každý datový bod v datech časové řady a pozice všech zjištěných anomálií. |
| Zjistit stav anomálií nejnovějšího datového bodu | Odpověď JSON obsahující stav anomálie (a další data) pro poslední datový bod v datech časové řady.                                        |

 I když je tato aplikace napsaná v jazyce C#, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tento rychlý Start najdete na [GitHubu](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/csharp-detect-anomalies.cs).

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
- Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" vytvořte prostředek pro detekci anomálií "  target="_blank"> vytvořením prostředku detektoru anomálií <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Počkejte na nasazení a klikněte na tlačítko **Přejít k prostředku** .
    - K připojení aplikace k rozhraní API detektoru anomálií budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
- Libovolná edice sady [Visual Studio 2017 nebo novější](https://visualstudio.microsoft.com/downloads/)
- Rozhraní [Json.NET](https://www.newtonsoft.com/json), k dispozici jako balíček NuGet. Instalace Newtonsoft.Jsjako balíčku NuGet v aplikaci Visual Studio:

    1. Klikněte pravým tlačítkem na projekt v **Průzkumník řešení**.
    2. Vyberte **Spravovat balíčky NuGet**.
    3. Vyhledejte *Newtonsoft.Js* a nainstalujte balíček.

- Pokud používáte Linux/MacOS, můžete tuto aplikaci spustit pomocí [mono](https://www.mono-project.com/).

- Soubor JSON, který obsahuje datové body časové řady. Ukázková data pro tento rychlý Start najdete na [GitHubu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [anomaly-detector-environment-variables](../includes/environment-variables.md)]

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

1. Vytvořte novou asynchronní funkci s názvem `Request` , která přijímá proměnné vytvořené výše.

2. Pomocí objektu nastavte informace o protokolu zabezpečení a hlavičce klienta `HttpClient` . Nezapomeňte do hlavičky přidat svůj klíč předplatného `Ocp-Apim-Subscription-Key` . Pak vytvořte `StringContent` objekt pro požadavek.

3. Odešlete požadavek pomocí `PostAsync()` a pak vraťte odpověď.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Zjištění anomálií jako dávky

1. Vytvořte novou funkci s názvem `detectAnomaliesBatch()` . Sestavte požadavek a odešlete ho voláním `Request()` funkce s vaším koncovým bodem, klíčem předplatného, adresou URL pro detekci anomálií dávky a daty časových řad.

2. Deserializovat objekt JSON a zapsat ho do konzoly.

3. Pokud odpověď obsahuje `code` pole, vytiskněte kód chyby a chybovou zprávu.

4. V opačném případě najděte pozice anomálií v datové sadě. `isAnomaly`Pole odpovědi obsahuje pole logických hodnot, z nichž každý označuje, zda je datový bod anomálií. Převeďte tuto hodnotu na pole řetězců pomocí funkce objektu Response `ToObject<bool[]>()` . Iterujte v poli a vytiskněte index všech `true` hodnot. Tyto hodnoty odpovídají indexu datových bodů neobvyklé, pokud byly nalezeny.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjistit stav anomálií nejnovějšího datového bodu

1. Vytvořte novou funkci s názvem `detectAnomaliesLatest()` . Sestavte požadavek a odešlete ho voláním `Request()` funkce s vaším koncovým bodem, klíčem předplatného, adresou URL pro detekci anomálií posledního bodu a daty časové řady.

2. Deserializovat objekt JSON a zapsat ho do konzoly.

    [!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Načtěte data časové řady a odešlete žádost.

1. V metodě Main vaší aplikace načtěte data časové řady JSON pomocí `File.ReadAllText()` .

2. Zavolejte funkce detekce anomálií vytvořené výše. Slouží `System.Console.ReadKey()` k zachování otevřeného okna konzoly po spuštění aplikace.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Příklad odpovědi

Ve formátu JSON se vrátí úspěšná odpověď. Kliknutím na následující odkazy zobrazíte odpověď JSON na GitHubu:
* [Příklad odpovědi na zjišťování dávky](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Příklad odpovědi na nejnovější zjištění bodu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

[!INCLUDE [anomaly-detector-next-steps](../includes/quickstart-cleanup-next-steps.md)]
