---
title: 'Úvodní příručka: Kontrola pravopisu pomocí rozhraní REST API a jazyka C# – kontrola pravopisu bingu'
titleSuffix: Azure Cognitive Services
description: Můžete začít používat rozhraní REST API kontroly pravopisu Bingu ke kontrole pravopisu a gramatiky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 036ea00362b604957a1887127fca0b8d775d4e7b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382944"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Úvodní příručka: Kontrola pravopisu pomocí rozhraní REST API pro kontrolu pravopisu Bingu a c #

Pomocí tohoto rychlého startu můžete provést první volání do rozhraní REST API kontroly pravopisu Bingu. Tato jednoduchá aplikace jazyka C# odešle požadavek do rozhraní API a vrátí seznam navrhovaných oprav. Aplikace je sice napsaná v C#, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tuto aplikaci je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs).

## <a name="prerequisites"></a>Požadavky

* Libovolná edice [Visual Studia 2017 nebo novější](https://www.visualstudio.com/downloads/).
* Instalace `Newtonsoft.Json` jako balíček NuGet v sadě Visual Studio:
    1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na soubor řešení.
    1. Vyberte **spravovat balíčky NuGet pro řešení**.
    1. Vyhledejte `Newtonsoft.Json` a nainstalujte balíček.
* Pokud používáte Linux/MacOS, lze tuto aplikaci spustit pomocí [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nové řešení `SpellCheckSample` konzoly s názvem v sadě Visual Studio. Pak přidejte následující obory názvů do souboru hlavního kódu.
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. Vytvořte proměnné pro koncový bod rozhraní API, klíč předplatného a text, který má být kontrolován. Můžete použít globální koncový bod níže nebo vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený na portálu Azure pro váš prostředek.

    ```csharp
    namespace SpellCheckSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/spellcheck?";
            static string key = "<ENTER-KEY-HERE>";
            //text to be spell-checked
            static string text = "Hollo, wrld!";
        }
    }
    ```

3. Vytvořte proměnnou pro parametry vyhledávání. Připojit kód trhu `mkt=`za . Kód trhu je země, ze které podáváte žádost. Také připojit režim kontroly pravopisu po `&mode=`. Režim je `proof` buď (zachytí většinu `spell` pravopisných/gramatických chyb) nebo (zachytí většinu pravopisu, ale ne tolik gramatické chyby).
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Vytvoření a odeslání žádosti o kontrolu pravopisu

1. Vytvořte asynchronní funkci `SpellCheck()` volanou k odeslání požadavku do rozhraní API. Vytvořte `HttpClient`a přidejte klíč `Ocp-Apim-Subscription-Key` předplatného do záhlaví. Potom proveďte následující kroky v rámci funkce.

    ```csharp
    async static void SpellCheck()
    {
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = null;
        // add the rest of the code snippets here (except for main())...
    }
    ```

2. Vytvořte identifikátor URI pro váš požadavek připojením hostitele, cesty a parametrů.
    
    ```csharp
    string uri = host + path + params_;
    ```

3. Vytvořte seznam `KeyValuePair` s objektem obsahujícím text a `FormUrlEncodedContent` použijte ho k vytvoření objektu. Nastavte informace záhlaví a `PostAsync()` použijte k odeslání požadavku.

    ```csharp
    var values = new Dictionary<string, string>();
    values.Add("text", text);
    var content = new FormUrlEncodedContent(values);
    content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
    response = await client.PostAsync(uri, new FormUrlEncodedContent(values));
    ```

## <a name="get-and-print-the-api-response"></a>Získání a tisk odpovědi rozhraní API

### <a name="get-the-client-id-header"></a>Získání záhlaví ID klienta

Pokud odpověď obsahuje `X-MSEdge-ClientID` záhlaví, získejte hodnotu a vytiskněte ji.

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>Získejte odpověď

Získejte odpověď z rozhraní API. Deserializujte objekt JSON a vytiskněte jej do konzoly.

```csharp
string contentString = await response.Content.ReadAsStringAsync();

dynamic jsonObj = JsonConvert.DeserializeObject(contentString);
Console.WriteLine(jsonObj);
```

## <a name="call-the-spell-check-function"></a>Volání funkce kontroly pravopisu

V hlavní funkci projektu volejte `SpellCheck()`.

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="run-the-application"></a>Spuštění aplikace

Sestavení a spuštění projektu. Pokud používáte Visual Studio, ladíte soubor stisknutím **klávesy F5.**

## <a name="example-json-response"></a>Příklad odpovědi JSON

Úspěšná odpověď se vrátí ve formátu JSON, jak je znázorněno v následujícím příkladu: 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](../tutorials/spellcheck.md)

- [Co je rozhraní API pro kontrolu pravopisu Bingu?](../overview.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
