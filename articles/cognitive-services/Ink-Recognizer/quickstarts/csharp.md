---
title: 'Rychlý start: Rozpoznávání digitálního inkoustu pomocí REST API pro rozpoznávání rukopisuC#'
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro rozpoznávání rukopisu můžete začít rozpoznávat tahy digitálního inkoustu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 09/23/2019
ms.author: aahi
ms.openlocfilehash: 86e69d75c067159a4daa637984a392a393dc46fa
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211781"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Rychlý start: Rozpoznávání digitálního inkoustu pomocí REST API pro rozpoznávání rukopisuC#

Pomocí tohoto rychlého startu můžete začít odesílat tahy digitálního inkoustu do rozhraní API pro rozpoznávání rukopisu. Tato C# aplikace pošle požadavek rozhraní API obsahující data tahu ve formátu JSON a získá odpověď.

I když je tato aplikace napsaná v C#, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků.

Obvykle byste volali rozhraní API z digitální aplikace pro psaní rukou. V tomto rychlém startu se v souboru JSON pošle data tahy perem pro následující psaný vzorek.

![Obrázek rukopisného textu](../media/handwriting-sample.jpg)

Zdrojový kód pro tento rychlý Start najdete na [GitHubu](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Požadavky

- Libovolná edice sady [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Postup instalace Newtonsoft. JSON jako balíčku NuGet v aplikaci Visual Studio:
        1. Klikněte pravým tlačítkem na **správce řešení** .
        2. Klikněte na **Spravovat balíčky NuGet...**
        3. `Newtonsoft.Json` Vyhledat a nainstalovat balíček
- Pokud používáte Linux/MacOS, může být tato aplikace spuštěná pomocí [mono](https://www.mono-project.com/).

- Ukázková data tahu perem pro tento rychlý Start najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


## <a name="create-a-new-application"></a>Vytvoření nové aplikace

1. V aplikaci Visual Studio vytvořte nové řešení konzoly a přidejte následující balíčky. 

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

2. Vytvořte proměnné pro svůj klíč předplatného a koncový bod. Nahraďte níže uvedený koncový bod následujícím vygenerovaným prostředkem pro rozpoznávání rukopisu. Připojte ho k identifikátoru URI pro rozpoznávání rukopisu pro připojení k rozhraní API.

    ```csharp
    // Replace the subscriptionKey string with your valid subscription key.
    const string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Replace the dataPath string with a path to the JSON formatted ink stroke data.
    const string dataPath = @"PATH-TO-INK-STROKE-DATA"; 

    // URI information for ink recognition:
    const string endpoint = "https://<your-custom-subdomain>.cognitiveservices.azure.com";
    const string inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Vytvoření funkce pro odesílání požadavků

1. Vytvořte novou asynchronní funkci s názvem `Request` , která přijímá proměnné vytvořené výše.

2. Pomocí `HttpClient` objektu nastavte informace o protokolu zabezpečení a hlavičce klienta. Nezapomeňte do `Ocp-Apim-Subscription-Key` hlavičky přidat svůj klíč předplatného. Pak vytvořte `StringContent` objekt pro požadavek.
 
3. Odešlete žádost pomocí `PutAsync()`. Pokud je požadavek úspěšný, vrátí odpověď.  
    
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

## <a name="send-an-ink-recognition-request"></a>Poslat požadavek na rozpoznávání rukopisu

1. Vytvořte novou funkci s názvem `recognizeInk()`. Sestavte požadavek a odešlete ho voláním `Request()` funkce s vaším koncovým bodem, klíčem předplatného, adresou URL pro rozhraní API a datovým tahem digitálního inkoustu.

2. Deserializovat objekt JSON a zapsat ho do konzoly. 
    
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

## <a name="load-your-digital-ink-data"></a>Načtení dat digitálního inkoustu

Vytvořte funkci nazvanou `LoadJson()` , která načte soubor JSON pro tisk dat. `StreamReader` Pomocí a `JsonTextReader` vytvořte avraťteho.`JObject`
    
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

## <a name="send-the-api-request"></a>Odeslat požadavek rozhraní API

1. V metodě Main aplikace načtěte data JSON pomocí funkce vytvořené výše. 

2. `recognizeInk()` Zavolejte funkci vytvořenou výše. Slouží `System.Console.ReadKey()` k zachování otevřeného okna konzoly po spuštění aplikace.
    
    ```csharp
    static void Main(string[] args){

        var requestData = LoadJson(dataPath);
        string requestString = requestData.ToString(Newtonsoft.Json.Formatting.None);
        recognizeInk(requestString);
        System.Console.WriteLine("\nPress any key to exit ");
        System.Console.ReadKey();
        }
    ```

## <a name="run-the-application-and-view-the-response"></a>Spuštění aplikace a zobrazení odpovědi

Spusťte aplikaci. Ve formátu JSON se vrátí úspěšná odpověď. Můžete také najít odpověď JSON na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [REST API – referenční informace](https://go.microsoft.com/fwlink/?linkid=2089907)


Pokud chcete zjistit, jak funguje rozhraní API pro rozpoznávání rukopisu v digitální aplikaci pro rukopis, podívejte se na následující ukázkové aplikace na GitHubu:
* [C# a Univerzální platforma Windows (UPW)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# a Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikace webového prohlížeče v Javascriptu](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobilní aplikace v Javě a Androidu](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobilní aplikace ve Swiftu a iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
