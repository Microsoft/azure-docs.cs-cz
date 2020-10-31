---
title: 'Rychlý Start: Kontrola pravopisu pomocí REST API a C# – Kontrola pravopisu Bingu'
titleSuffix: Azure Cognitive Services
description: Začněte používat Kontrola pravopisu Bingu REST API a C# pro kontrolu pravopisu a gramatiky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 060b648a0d1ee963b44c25e45a7de2888f662335
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93084132"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Rychlý Start: Kontrola pravopisu pomocí Kontrola pravopisu Bingu REST API a C #

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

V tomto rychlém startu můžete provést první volání REST API Kontrola pravopisu Bingu. Tato jednoduchá aplikace v jazyce C# pošle požadavek do rozhraní API a vrátí seznam navrhovaných oprav. 

I když je tato aplikace napsaná v jazyce C#, rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód této aplikace je k dispozici na [GitHubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs).

## <a name="prerequisites"></a>Předpoklady

* Libovolná edice sady [Visual Studio 2017 nebo novější](https://www.visualstudio.com/downloads/).
* Newtonsoft.Jsna balíčku NuGet. 
     
   Instalace tohoto balíčku v aplikaci Visual Studio:

     1. V **Průzkumník řešení** klikněte pravým tlačítkem myši na soubor řešení.
     1. Vyberte **Spravovat balíčky NuGet pro řešení** .
     1. Vyhledejte *Newtonsoft.Js* a nainstalujte balíček.

* Pokud používáte Linux/MacOS, můžete tuto aplikaci spustit pomocí [mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvořte nové řešení konzoly s názvem SpellCheckSample v aplikaci Visual Studio. Pak přidejte následující obory názvů do hlavního souboru kódu:
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. Vytvořte proměnné pro koncový bod rozhraní API, klíč předplatného a text, který se má zkontrolovat pravopis. Můžete použít globální koncový bod v následujícím kódu nebo použít vlastní koncový bod [subdomény](../../../cognitive-services/cognitive-services-custom-subdomains.md) zobrazený v Azure Portal pro váš prostředek.

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

3. Vytvořte řetězec pro parametry hledání: 

   1. Přiřaďte svůj kód na trhu k `mkt` parametru pomocí `=` operátoru. Kód trhu je kód země nebo oblasti, ze které provedete požadavek. 

   1. Přidejte `mode` parametr s `&` operátorem a potom přiřaďte režim kontroly pravopisu. Režim může být buď `proof` (catch nejvíc pravopisné/gramatické chyby) `spell` , nebo (catch nejvíc pravopisných chyb, ale ne tolik gramatických chyb).
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Vytvoření a odeslání žádosti o kontrolu pravopisu

1. Vytvořte asynchronní funkci volanou `SpellCheck()` k odeslání žádosti do rozhraní API. Vytvořte `HttpClient` a přidejte do záhlaví klíč předplatného `Ocp-Apim-Subscription-Key` . V rámci funkce postupujte podle následujících kroků.

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

3. Vytvoří seznam s `KeyValuePair` objektem, který obsahuje váš text, a použije ho k vytvoření `FormUrlEncodedContent` objektu. Nastavte informace záhlaví a použijte `PostAsync()` k odeslání žádosti.

    ```csharp
    var values = new Dictionary<string, string>();
    values.Add("text", text);
    var content = new FormUrlEncodedContent(values);
    content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
    response = await client.PostAsync(uri, new FormUrlEncodedContent(values));
    ```

## <a name="get-and-print-the-api-response"></a>Načtení a tisk odpovědi rozhraní API

### <a name="get-the-client-id-header"></a>Získat hlavičku ID klienta

Pokud odpověď obsahuje `X-MSEdge-ClientID` hlavičku, získá hodnotu a vytiskne ji.

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

Ve `Main()` funkci projektu volejte `SpellCheck()` .

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="run-the-application"></a>Spuštění aplikace

Sestavte a spusťte projekt. Pokud používáte Visual Studio, můžete soubor ladit stisknutím klávesy **F5** .

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
- [Odkaz na rozhraní API Bingu pro kontrolu pravopisu v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
