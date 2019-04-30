---
title: 'Rychlý start: Kontrola pravopisu pomocí rozhraní API Bingu pro kontrolu pravopisu zkontrolujte REST aC#'
titlesuffix: Azure Cognitive Services
description: Začněte používat rozhraní API Bingu pro kontrolu pravopisu zkontrolujte REST pro kontrolu pravopisu a gramatiky.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: 7a17c695482f2e9c8158c437c9c40c0abcb07e67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61066212"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>Rychlý start: Kontrola pravopisu pomocí rozhraní API Bingu pro kontrolu pravopisu zkontrolujte REST aC#

V tomto rychlém startu můžete provést první volání do rozhraní API Bingu pro kontrolu pravopisu zkontrolujte REST. Tento jednoduchý C# aplikace odešle požadavek na rozhraní API a vrátí seznam navrhovaných oprav. Aplikace je sice napsaná v C#, ale rozhraní API je webová služba RESTful kompatibilní s většinou programovacích jazyků. Zdrojový kód pro tuto aplikaci je k dispozici na [Githubu](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs).

## <a name="prerequisites"></a>Požadavky

* Libovolná edice sady [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* Chcete-li nainstalovat `Newtonsoft.Json` jako balíček NuGet v sadě Visual studio:
    1. V Průzkumníku řešení klikněte pravým tlačítkem na soubor řešení.
    1. Vyberte **spravovat balíčky NuGet pro řešení**.
    1. Vyhledejte `Newtonsoft.Json` a instalaci balíčku.
* Pokud používáte Linux nebo MacOS, bude možné spustit tuto aplikaci pomocí [Mono](https://www.mono-project.com/).

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Vytvoření a inicializace projektu

1. Vytvoření nového řešení konzoly s názvem `SpellCheckSample` v sadě Visual Studio. Pak přidejte následující obory názvů do souboru hlavního kódu.
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. Vytváření proměnných pro koncový bod rozhraní API, váš klíč předplatného a text, který má být zaškrtnuto pravopisu.

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

3. Vytvořte proměnnou pro parametry hledání. Připojit na trhu kódu po `mkt=`. Kód na trhu je země, které provedete žádost z. Navíc vaše režimu kontroly pravopisu po připojení `&mode=`. Režim je buď `proof` (zachytí většina pravopisné nebo gramatické chyby) nebo `spell` (zachycuje většinu pravopisu, ale ne tolik gramatické chyby).
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>Vytvořit a odeslat žádost o kontrolu pravopisu

1. Vytvoření asynchronní funkce volána `SpellCheck()` odeslat požadavek na rozhraní API. Vytvoření `HttpClient`a přidat váš klíč předplatného na `Ocp-Apim-Subscription-Key` záhlaví. Pak proveďte následující kroky v rámci této funkce.

    ```csharp
    async static void SpellCheck()
    {
        HttpClient client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = new HttpResponseMessage();
        // add the rest of the code snippets here (except for main())...
    }

2. Create the URI for your request by appending your host, path, and parameters.
    
    ```csharp
    string uri = host + path + params_;
    ```

3. Vytvoření seznamu s `KeyValuePair` objekt obsahující text a použijte ji k vytvoření `FormUrlEncodedContent` objektu. Nastavit informace hlavičky a pomocí `PostAsync()` odešlete žádost.

    ```csharp
    List<KeyValuePair<string, string>> values = new List<KeyValuePair<string, string>>();
    values.Add(new KeyValuePair<string, string>("text", text));
    
    using (FormUrlEncodedContent content = new FormUrlEncodedContent(values))
    {
        content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
        response = await client.PostAsync(uri, content);
    }
    ```

## <a name="get-and-print-the-api-response"></a>Získání a tisku odpověď rozhraní API

### <a name="get-the-client-id-header"></a>Získat záhlaví ID klienta

Pokud odpověď obsahuje `X-MSEdge-ClientID` záhlaví, získat hodnotu a vytisknout si ho.

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>Získejte odpovědi

Získání odpovědi z rozhraní API. Deserializace objektu JSON a vypíše do konzoly.

```csharp
string contentString = await response.Content.ReadAsStringAsync();

dynamic jsonObj = JsonConvert.DeserializeObject(contentString);
Console.WriteLine(jsonObj);
```

## <a name="call-the-spell-check-function"></a>Volání funkce kontroly pravopisu

Ve volání funkce Main vašeho projektu, `SpellCheck()`.

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="example-json-response"></a>Příklad JSON odpovědi

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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření jednostránkové webové aplikace](../tutorials/spellcheck.md)

- [Co je API kontrola pravopisu Bingu?](../overview.md)
- [Referenční informace k rozhraní API pro kontrolu pravopisu Bingu v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
