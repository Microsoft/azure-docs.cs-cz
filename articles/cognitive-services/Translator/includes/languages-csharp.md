---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: a7715577936b0e95392f2d561e4b492b20c9dbf5
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906925"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Vytvoření projektu .NET Core

Otevřete nový příkazový řádek (nebo terminálovou relaci) a spusťte tyto příkazy:

```console
dotnet new console -o languages-sample
cd languages-sample
```

První příkaz dělá dvě věci. Vytvoří novou aplikaci konzoly .NET a `languages-sample`vytvoří adresář s názvem . Druhý příkaz se změní do adresáře pro váš projekt.

Dále budete muset nainstalovat Json.Net. Z adresáře projektu spusťte:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>Přidání požadovaných oborů názvů do projektu

Příkaz, `dotnet new console` který jste dříve spustili, vytvořil projekt, včetně `Program.cs`. Tento soubor je místo, kam vložíte kód aplikace. Otevřete `Program.cs`a nahraďte existující příkazy using. Tyto příkazy zajistit, že máte přístup ke všem typům potřebné k sestavení a spuštění ukázkové aplikace.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="get-endpoint-information-from-an-environment-variable"></a>Získání informací o koncovém bodu z proměnné prostředí

Přidejte do třídy `Program` následující řádky. Tyto řádky číst klíč předplatného a koncový bod z proměnných prostředí a vyvolá chybu, pokud narazíte na všechny problémy.

```csharp
private const string endpoint_var = "TRANSLATOR_TEXT_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);

static Program()
{
    if (null == endpoint)
    {
        throw new Exception("Please set/export the environment variable: " + endpoint_var);
    }
}
```

## <a name="create-a-function-to-get-a-list-of-languages"></a>Vytvoření funkce pro získání seznamu jazyků

Ve `Program` třídě vytvořte funkci `GetLanguages`nazvanou . Tato třída zapouzdřuje kód používaný k volání prostředku Languages a vytiskne výsledek do konzoly.

```csharp
static void GetLanguages()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-route"></a>Nastavení trasy

Přidejte tyto `GetLanguages` řádky do funkce.

```csharp
string route = "/languages?api-version=3.0";
```

## <a name="instantiate-the-client-and-make-a-request"></a>Vytvořte instanci klienta a požádejte o to

Tyto řádky konkretizovat `HttpClient` `HttpRequestMessage`a a:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Sestavení požadavku a tisk odpovědi

Uvnitř `HttpRequestMessage` budete:

* Deklarovat metodu HTTP
* Vytvoření identifikátoru URI požadavku
* Přidání požadovaných záhlaví
* Vytvoření asynchronního požadavku
* Tisk odpovědi

Přidejte tento `HttpRequestMessage`kód do :

```csharp
// Set the method to GET
request.Method = HttpMethod.Get;
// Construct the full URI
request.RequestUri = new Uri(endpoint + route);
// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;
// Pretty print the response
Console.WriteLine(PrettyPrint(jsonResponse));
Console.WriteLine("Press any key to continue.");
```

Pokud používáte předplatné služeb Cognitive Services s více `Ocp-Apim-Subscription-Region` službami, musíte také zahrnout parametry požadavku. [Přečtěte si další informace o ověřování pomocí předplatného s více službami](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Chcete-li vytisknout odpověď s "Pretty Print" (formátování odpovědi), přidejte tuto funkci do třídy Program:

```csharp
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

Posledním krokem je `GetLanguages()` volání `Main` funkce. Vyhledejte `static void Main(string[] args)` a přidejte tyto řádky:

```csharp
GetLanguages();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

To je ono, jste připraveni spustit ukázkovou aplikaci. Z příkazového řádku (nebo terminálové relace) přejděte do adresáře projektu a spusťte:

```console
dotnet run
```

## <a name="sample-response"></a>Ukázková odpověď

Zkratku země/oblasti naleznete v tomto [seznamu jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nezapomeňte odebrat všechny důvěrné informace ze zdrojového kódu ukázkové aplikace, jako jsou klíče předplatného.

## <a name="next-steps"></a>Další kroky

Podívejte se na odkaz rozhraní API pochopit vše, co můžete dělat s překladačem text api.

> [!div class="nextstepaction"]
> [referenční dokumentace k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
