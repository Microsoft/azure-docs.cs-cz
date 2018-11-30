---
title: 'Rychlý start: Překlad textu, C# – Translator Text API'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu přeložíte text z jednoho jazyka do jiného pomocí služby Translator Text API pomocí jazyka C#.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 11/20/2018
ms.author: erhopf
ms.openlocfilehash: 06e294187247636c552dfd4a7d02167eb55be19a
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334699"
---
# <a name="quickstart-translate-text-with-the-translator-text-rest-api-c"></a>Rychlý start: Překlad textu pomocí rozhraní REST API služby Translator Text (C#)

V tomto rychlém startu zjistíte jak Převede textový řetězec z angličtiny italština a němčina pomocí .NET Core a rozhraní REST Translator Text API.

K tomuto rychlému startu potřebujete [účet služby Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby Translator Text. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Balíček NuGet Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), nebo vašem oblíbeném textovém editoru
* Klíč rozhraní předplatné Azure pro službu rozpoznávání řeči

## <a name="create-a-net-core-project"></a>Vytvoření projektu .NET Core

Otevřete nový příkazový řádek (nebo relaci Terminálové služby) a spusťte tyto příkazy:

```console
dotnet new console -o translate-sample
cd translate-sample
```

První příkaz provede dvě věci. Vytvoří novou konzolovou aplikaci .NET a vytvoří adresář s názvem `translate-sample`. Druhý příkaz změní adresář pro projekt.

V dalším kroku budete muset nainstalovat Json.Net. Z adresáře vašeho projektu spusťte:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>Do projektu přidejte požadované obory názvů

`dotnet new console` Příkaz, který byl dříve vytvořili projekt, včetně `Program.cs`. Tento soubor je místo, kam budete dáte kódu aplikace. Otevřít `Program.cs`a nahraďte existující příkazy using. Tyto příkazy Ujistěte se, že máte přístup ke všem typům, které jsou potřebné k sestavení a spuštění ukázkové aplikace.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="create-a-function-to-translate-text"></a>Vytvoření funkce pro převod textu

V rámci `Program` třídy, vytvořte funkci s názvem `TranslateText`. Tato třída zapouzdří kód používá k volání zdroje přeložit a vytiskne výsledek do konzoly.

```csharp
static void TranslateText()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-subscription-key-host-name-and-path"></a>Nastavte klíč předplatného, název hostitele a cestu

Přidejte tyto řádky do `TranslateText` funkce. Uvidíte, že spolu s `api-version`, dva další parametry se připojili k `route`. Tyto parametry slouží k nastavení překladu výstupy. V této ukázce je nastaven na němčinu (`de`) a italština (`it`). Nezapomeňte že aktualizovat hodnotu klíče předplatného.

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/translate?api-version=3.0&to=de&to=it";
string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
```

Dále musíme vytvořit a serializaci objektu JSON, který obsahuje text, který chcete přeložit. Mějte na paměti, můžete předat více než jeden objekt `body` pole.

```csharp
System.Object[] body = new System.Object[] { new { Text = @"Hello world!" } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Vytvořte instanci klienta a vytvořit žádost

Vytvoření instance tyto řádky `HttpClient` a `HttpRequestMessage`:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Vytvořit požadavek a tisku odpověď

Uvnitř `HttpRequestMessage` , je nutné:

* Deklarovat metodu HTTP
* Vytvořit identifikátor URI požadavku
* Vložit text požadavku (serializovaný objekt JSON)
* Přidat povinné hlavičky
* Ujistěte se, asynchronního požadavku
* Tisk odpovědi

Přidejte tento kód `HttpRequestMessage`:

```csharp
// Set the method to POST
request.Method = HttpMethod.Post;

// Construct the full URI
request.RequestUri = new Uri(host + route);

// Add the serialized JSON object to your request
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");

// Add the authorization header
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;

// Print the response
Console.WriteLine(jsonResponse);
Console.WriteLine("Press any key to continue.");
```

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

Posledním krokem je volání `TranslateText()` v `Main` funkce. Vyhledejte `static void Main(string[] args)` a přidejte tyto řádky:

```csharp
TranslateText();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Je to, jste připraveni spustit ukázkovou aplikaci. Z příkazového řádku (nebo relaci Terminálové služby) přejděte do adresáře vašeho projektu a spusťte:

```console
dotnet run
```

## <a name="sample-response"></a>Ukázková odpověď

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      }
    ]
  }
]
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Ujistěte se, že zdrojový kód ukázkové aplikace, jako jsou klíče předplatného odebrání jakýchkoli důvěrných informací.

## <a name="next-steps"></a>Další postup

Prozkoumejte vzorový kód pro tento a další rychlé starty, včetně transkripce a identifikace jazyka a také dalších vzorových projektů služby Translator Text na GitHubu.

> [!div class="nextstepaction"]
> [Prozkoumejte příklady C# na GitHubu](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>Další informace najdete v tématech

* [Transliterace textu](quickstart-csharp-transliterate.md)
* [Identifikace jazyka podle vstupu](quickstart-csharp-detect.md)
* [Získání alternativních překladů](quickstart-csharp-dictionary.md)
* [Získání seznamu podporovaných jazyků](quickstart-csharp-languages.md)
* [Určení délky věty ze vstupu](quickstart-csharp-sentences.md)
