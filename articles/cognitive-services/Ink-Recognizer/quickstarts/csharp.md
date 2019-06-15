---
title: 'Rychlý start: Rozpoznat digitálního inkoustu pomocí rozhraní REST API pro rozpoznávání rukopisu aC#'
description: Pomocí rozhraní API pro rozpoznávání rukopisu spusťte uznání digitální inkoustových tahů.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: d661d6eca6e4916946944c48cc2e5411aeaf8f14
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060996"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Rychlý start: Rozpoznat digitálního inkoustu pomocí rozhraní REST API pro rozpoznávání rukopisu aC#

V tomto rychlém startu můžete zahájit odesílání digitální inkoustových tahů do rozhraní API pro rozpoznávání rukopisu. To C# aplikace odešle požadavek rozhraní API, obsahující data ve formátu JSON inkoustu stroke a získá odpověď.

Zatímco tato aplikace je napsána v C#, rozhraní API je kompatibilní s Většina programovacích jazyků rozhraní RESTful webová služba.

Obvykle by volání rozhraní API z aplikace pro digitální rukopisu. V tomto rychlém startu odesílá data inkoustu stroke pro následující ukázka rukou psaný ze souboru JSON.

![Obrázek rukou psaný text](../media/handwriting-sample.jpg)

Zdrojový kód v tomto rychlém startu najdete na [Githubu](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Požadavky

- Libovolná edice sady [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Chcete-li nainstalovat Newtonsoft.Json jako balíček NuGet v sadě Visual studio:
        1. Klikněte pravým tlačítkem na **správci řešení**
        2. Klikněte na tlačítko **spravovat balíčky NuGet...**
        3. Vyhledejte `Newtonsoft.Json` a instalace balíčku
- Pokud používáte Linux nebo MacOS, můžete tuto aplikaci spustili pomocí [Mono](https://www.mono-project.com/).

- Příklad inkoustu stroke data pro účely tohoto rychlého najdete na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


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
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    ```

2. Vytváření proměnných pro váš klíč předplatného a váš koncový bod. Níže je identifikátor URI, můžete použít pro rozpoznávání rukopisu. Připojí se k vašeho koncového bodu služby později k vytvoření rozhraní API adresa URl požadavku.

    ```csharp
    // Replace the subscriptionKey string with your valid subscription key.
    const string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Replace the dataPath string with a path to the JSON formatted ink stroke data.
    const string dataPath = @"PATH-TO-INK-STROKE-DATA"; 

    // URI information for ink recognition:
    const string endpoint = "https://api.cognitive.microsoft.com";
    const string inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce k odesílání požadavků

1. Vytvoření nové asynchronní funkce volána `Request` , která přebírá proměnných vytvořené výše.

2. Nastavení protokolu zabezpečení a záhlaví informací pomocí klienta `HttpClient` objektu. Nezapomeňte přidat klíč předplatného. Chcete `Ocp-Apim-Subscription-Key` záhlaví. Pak vytvořte `StringContent` pro žádost.
 
3. Odeslat požadavek s `PutAsync()`. Pokud je žádost úspěšná, vrátí odpověď.  
    
    ```csharp
    static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
        
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
            System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            var res = await client.PutAsync(endpoint, content);
            if (res.IsSuccessStatusCode){
                return await res.Content.ReadAsStringAsync();
            }
            else{
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Poslat žádost o rozpoznávání rukopisu

1. Vytvořit novou funkci s názvem `recognizeInk()`. Sestavit požadavku a odeslat ho voláním `Request()` funkce s váš koncový bod, klíč předplatného, adresu URL pro rozhraní API a data digitálního inkoustu stroke.

2. Deserializace objektu JSON a zapište ho do konzoly. 
    
    ```csharp
    static void recognizeInk(string requestData){

        //construct the request
        var result = Request(
            endpoint,
            inkRecognitionUrl,
            subscriptionKey,
            requestData).Result;

        dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
        System.Console.WriteLine(jsonObj);
    }
    ```

## <a name="load-your-digital-ink-data"></a>Načtěte data digitálního inkoustu

Vytvořit funkci s názvem `LoadJson()` načtení inkoustu datového souboru JSON. Použití `StreamReader` a `JsonTextReader` k vytvoření `JObject` a vrátí jej.
    
```csharp
public static JObject LoadJson(string fileLocation){

    var jsonObj = new JObject();

    using (StreamReader file = File.OpenText(fileLocation))
    using (JsonTextReader reader = new JsonTextReader(file)){
        jsonObj = (JObject)JToken.ReadFrom(reader);
    }
    return jsonObj;
}
```

## <a name="send-the-api-request"></a>Odeslání požadavku rozhraní API

1. V hlavní metodě vaší aplikace načtěte JSON data pomocí funkce vytvořené výše. 

2. Volání `recognizeInk()` funkce vytvořené výše. Použití `System.Console.ReadKey()` nechat otevřené v okně konzoly se po spuštění aplikace.
    
    ```csharp
    static void Main(string[] args){

        var requestData = LoadJson(dataPath);
        string requestString = requestData.ToString(Newtonsoft.Json.Formatting.None);
        recognizeInk(requestString);
        System.Console.WriteLine("\nPress any key to exit ");
        System.Console.ReadKey();
        }
    ```

## <a name="run-the-application-and-view-the-response"></a>Spusťte aplikaci a zobrazit odpovědi

Spusťte aplikaci. Úspěšná odpověď se vrátí ve formátu JSON. Můžete také najít odpověď JSON na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://go.microsoft.com/fwlink/?linkid=2089907)


Pokud chcete zobrazit, jak rozhraní API pro rozpoznávání rukopisu funguje v digitální rukopisu aplikaci, podívejte se na následující ukázkové aplikace na Githubu:
* [C# a Univerzální platforma Windows (UPW)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# a Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikace webového prohlížeče v Javascriptu](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobilní aplikace v Javě a Androidu](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobilní aplikace ve Swiftu a iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
