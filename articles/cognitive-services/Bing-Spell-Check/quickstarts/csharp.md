---
title: 'Rychlý Start: Kontrola pravopisu pomocí Kontrola pravopisu Bingu REST API aC#'
titleSuffix: Azure Cognitive Services
description: Začněte používat REST API Kontrola pravopisu Bingu pro kontrolu pravopisu a gramatiky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: 93b5c395a0d121305c092229d862bf9ecaa4789c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72936066"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Rychlý Start: Kontrola pravopisu pomocí Kontrola pravopisu Bingu REST API aC#

V tomto rychlém startu můžete provést první volání REST API Kontrola pravopisu Bingu. Tato jednoduchá C# aplikace pošle požadavek do rozhraní API a vrátí seznam navrhovaných oprav. Aplikace je sice napsaná v C#, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód této aplikace je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs).

## <a name="prerequisites"></a>Předpoklady

* Libovolná edice sady [Visual Studio 2017 nebo novější](https://www.visualstudio.com/downloads/).
* Postup instalace `Newtonsoft.Json` jako balíčku NuGet v aplikaci Visual Studio:
    1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na soubor řešení.
    1. Vyberte **Spravovat balíčky NuGet pro řešení**.
    1. Vyhledejte `Newtonsoft.Json` a nainstalujte balíček.
* Pokud používáte Linux/MacOS, můžete tuto aplikaci spustit pomocí [mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nové řešení konzoly s názvem `SpellCheckSample` v aplikaci Visual Studio. Pak přidejte následující obory názvů do souboru hlavního kódu.
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. Vytvořte proměnné pro koncový bod rozhraní API, klíč předplatného a text, který se má zkontrolovat pravopis.

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

3. Vytvořte proměnnou pro parametry hledání. Po `mkt=`přidejte svůj kód na trhu. Kód na trhu je země, ze které provedete požadavek. Také po `&mode=`připojit režim kontroly pravopisu. Režim je buď `proof` (catch nejvíc pravopis/gramatické chyby) nebo `spell` (zachycuje většinu pravopisů, ale ne tolik gramatických chyb).
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Vytvoření a odeslání žádosti o kontrolu pravopisu

1. Vytvořte asynchronní funkci nazvanou `SpellCheck()` pro odeslání žádosti do rozhraní API. Vytvořte `HttpClient`a přidejte svůj klíč předplatného do hlavičky `Ocp-Apim-Subscription-Key`. Pak proveďte následující kroky v rámci této funkce.

    ```csharp
    async static void SpellCheck()
    {
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = null;
        // add the rest of the code snippets here (except for main())...
    }
    ```

2. Vytvořte identifikátor URI pro vaši žádost připojením hostitele, cesty a parametrů.
    
    ```csharp
    string uri = host + path + params_;
    ```

3. Vytvoří seznam s `KeyValuePair` objektem, který obsahuje váš text, a použije ho k vytvoření objektu `FormUrlEncodedContent`. Nastavte informace v hlavičce a použijte `PostAsync()` k odeslání žádosti.

    ```csharp
    var values = new Dictionary<string, string>();
    values.Add("text", text);
    var content = new FormUrlEncodedContent(values);
    content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
    response = await client.PostAsync(uri, new FormUrlEncodedContent(values));
    ```

## <a name="get-and-print-the-api-response"></a>Načtení a tisk odpovědi rozhraní API

### <a name="get-the-client-id-header"></a>Získat hlavičku ID klienta

Pokud odpověď obsahuje hlavičku `X-MSEdge-ClientID`, Získejte hodnotu a vytiskněte ji.

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>Získat odpověď

Získejte odpověď z rozhraní API. Deserializace objektu JSON a jeho tisk do konzoly.

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

- [Co je rozhraní API Bingu pro kontrolu pravopisu?](../overview.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
