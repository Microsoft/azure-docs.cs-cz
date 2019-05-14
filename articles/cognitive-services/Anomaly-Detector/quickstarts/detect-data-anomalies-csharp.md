---
title: 'Rychlý start: Detekovat anomálie ve vašich datech časových řad pomocí rozhraní REST API detekce anomálií a C# | Dokumentace Microsoftu'
description: Zjištění anomálií v datových řad v dávce nebo na streamovaných datech pomocí rozhraní API detekce anomálií.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 04b331f3b63ad6400b4bb8efcd053d04ac88989b
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595838"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Rychlý start: Detekovat anomálie ve vašich datech časových řad pomocí rozhraní REST API detekce anomálií aC# 

V tomto rychlém startu můžete začít používat rozhraní API detekce anomálií dva režimy zjišťování zjišťovat anomálie ve vašich datech časových řad. To C# aplikace odesílá požadavky dvě rozhraní API, obsahující data ve formátu JSON časových řad a získá odpovědi.

| Žádosti na rozhraní API                                        | Výstup aplikace                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Zjišťovat anomálie v dávce                        | Odpověď JSON, který obsahuje stav anomálií (a další data) pro každý datový bod v datech časových řad a pozice jakékoli detekované anomálie. |
| Zjištění anomálií stavu nejnovější datového bodu | Odpověď JSON, který obsahuje stav anomálií (a další data) pro poslední datový bod v datech časové řady.                                                                                                                                         |

 Zatímco tato aplikace je napsána v C#, rozhraní API je kompatibilní s Většina programovacích jazyků rozhraní RESTful webová služba.

## <a name="prerequisites"></a>Požadavky

- Libovolná edice [sady Visual Studio 2017 nebo novější](https://visualstudio.microsoft.com/downloads/),

- Rozhraní [Json.NET](https://www.newtonsoft.com/json), k dispozici jako balíček NuGet. Chcete-li nainstalovat Newtonsoft.Json jako balíček NuGet v sadě Visual Studio:
    
    1. Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení**.
    2. Vyberte **spravovat balíčky NuGet**.
    3. Vyhledejte *Newtonsoft.Json* a instalaci balíčku.

- Pokud používáte Linux nebo MacOS, tuto aplikaci můžete spustit pomocí [Mono](https://www.mono-project.com/).

- Odkazuje JSON soubor obsahující data časových řad. Ukázková data pro účely tohoto rychlého startu můžete najít na [Githubu](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. V sadě Visual Studio vytvořit nové řešení konzoly a přidejte následující balíčky. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Vytváření proměnných pro váš klíč předplatného a váš koncový bod. V následující tabulce jsou identifikátory URI, můžete použít k detekci anomálií. Ty se připojí k vašeho koncového bodu služby později k vytvoření rozhraní API pro adresy URL požadavků.

    |Metoda detekce  |Identifikátor URI  |
    |---------|---------|
    |Zjišťování služby batch    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Zjišťování na nejnovější datového bodu     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
    ```csharp
    // Replace the subscriptionKey string value with your valid subscription key.
    const string subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";
    // Replace the endpoint URL with the correct one for your subscription. 
    // Your endpoint can be found in the Azure portal. For example: https://westus2.api.cognitive.microsoft.com
    const string endpoint = "[YOUR_ENDPOINT_URL]";
    // Replace the dataPath string with a path to the JSON formatted time series data.
    const string dataPath = "[PATH_TO_TIME_SERIES_DATA]";
    const string latestPointDetectionUrl = "/anomalydetector/v1.0/timeseries/last/detect";
    const string batchDetectionUrl = "/anomalydetector/v1.0/timeseries/entire/detect";
    ```

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce k odesílání požadavků

1. Vytvoření nové asynchronní funkce volána `Request` , která přebírá proměnných vytvořené výše.

2. Nastavení protokolu zabezpečení a záhlaví informací pomocí klienta `HttpClient` objektu. Nezapomeňte přidat klíč předplatného. Chcete `Ocp-Apim-Subscription-Key` záhlaví. Pak vytvořte `StringContent` pro žádost.

3. Odeslat požadavek s `PostAsync()`a pak vrací odpověď.

```csharp
static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
    using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
        System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
        client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

        var content = new StringContent(requestData, Encoding.UTF8, "application/json");
        var res = await client.PostAsync(endpoint, content);
        return await res.Content.ReadAsStringAsync();
    }
}
```

## <a name="detect-anomalies-as-a-batch"></a>Zjišťovat anomálie v dávce

1. Vytvořit novou funkci s názvem `detectAnomaliesBatch()`. Vytvoření požadavku a odeslat ji voláním `Request()` funkce s váš koncový bod, klíč předplatného, adresu URL pro detekci anomálií služby batch a data časových řad.

2. Deserializace objektu JSON a zapište ho do konzoly.

3. Pokud odpověď obsahuje `code` pole, kód chyby a chybová zpráva. 

4. V opačném případě vyhledání pozice anomálie v datové sadě. Odpověď na `isAnomaly` pole obsahuje celou řadu logické hodnoty, z nichž každý určuje, zda datový bod je anomálie. Tento účet převést na pole řetězců pomocí objektu odpovědi `ToObject<bool[]>()` funkce. Iterovat přes pole a vytisknout index žádné `true` hodnoty. Tyto hodnoty odpovídat indexu neobvyklé datových bodů, pokud některá.

```csharp
static void detectAnomaliesBatch(string requestData){
    System.Console.WriteLine("Detecting anomalies as a batch");

    var result = Request(
        endpoint,
        batchDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);

    if (jsonObj["code"] != null){
        System.Console.WriteLine($"Detection failed. ErrorCode:{jsonObj["code"]}, ErrorMessage:{jsonObj["message"]}");
    }
    else{
        bool[] anomalies = jsonObj["isAnomaly"].ToObject<bool[]>();
        System.Console.WriteLine("\nAnomalies detected in the following data positions:");
        for (var i = 0; i < anomalies.Length; i++){
            if (anomalies[i])
            {
                System.Console.Write(i + ", ");
            }
        }
    }
}
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Zjištění anomálií stavu nejnovější datového bodu

1. Vytvořit novou funkci s názvem `detectAnomaliesLatest()`. Vytvoření požadavku a odeslat ji voláním `Request()` funkce s váš koncový bod, klíč předplatného, adresu URL pro zjišťování anomálií nejnovější bod a data časových řad.

2. Deserializace objektu JSON a zapište ho do konzoly.

```csharp
static void detectAnomaliesLatest(string requestData){
    System.Console.WriteLine("\n\nDetermining if latest data point is an anomaly");
    var result = Request(
        endpoint,
        latestPointDetectionUrl,
        subscriptionKey,
        requestData).Result;

    dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
    System.Console.WriteLine(jsonObj);
}
```

## <a name="load-your-time-series-data-and-send-the-request"></a>Načtení dat časových řad a odeslat žádost

1. V hlavní metodě aplikace načíst data JSON časových řad s `File.ReadAllText()`. 

2. Volání funkce detekce anomálií vytvořili výše. Použití `System.Console.ReadKey()` nechat otevřené v okně konzoly se po spuštění aplikace.

```csharp
static void Main(string[] args){

    var requestData = File.ReadAllText(dataPath);

    detectAnomaliesBatch(requestData);
    detectAnomaliesLatest(requestData);

    System.Console.ReadKey();
}
```

### <a name="example-response"></a>Příklad odpovědi

Úspěšná odpověď se vrátí ve formátu JSON. Kliknutím na odkazy níže zobrazíte odpověď JSON na Githubu:
* [Příklad odpovědi zjišťování služby batch](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Příklad nejnovější bod zjišťování odpovědi](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)
