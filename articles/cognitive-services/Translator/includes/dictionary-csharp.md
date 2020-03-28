---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 55ad3591a8c2e7d5de6d1efe255e0f3a4b3c11bd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69907050"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Vytvoření projektu .NET Core

Otevřete nový příkazový řádek (nebo terminálovou relaci) a spusťte tyto příkazy:

```console
dotnet new console -o alternate-sample
cd alternate-sample
```

První příkaz dělá dvě věci. Vytvoří novou aplikaci konzoly .NET a `alternate-sample`vytvoří adresář s názvem . Druhý příkaz se změní do adresáře pro váš projekt.

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

## <a name="get-subscription-information-from-environment-variables"></a>Získání informací o předplatném z proměnných prostředí

Přidejte do třídy `Program` následující řádky. Tyto řádky číst klíč předplatného a koncový bod z proměnných prostředí a vyvolá chybu, pokud narazíte na všechny problémy.

```csharp
private const string key_var = "TRANSLATOR_TEXT_SUBSCRIPTION_KEY";
private static readonly string subscriptionKey = Environment.GetEnvironmentVariable(key_var);

private const string endpoint_var = "TRANSLATOR_TEXT_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);

static Program()
{
    if (null == subscriptionKey)
    {
        throw new Exception("Please set/export the environment variable: " + key_var);
    }
    if (null == endpoint)
    {
        throw new Exception("Please set/export the environment variable: " + endpoint_var);
    }
}
// The code in the next section goes here.
```

## <a name="create-a-function-to-get-alternate-translations"></a>Vytvoření funkce pro získání alternativních překladů

V `Program` rámci třídy vytvořte funkci nazvanou `AltTranslation`. Tato třída zapouzdřuje kód používaný k volání prostředku Dictionary a vytiskne výsledek do konzoly.

```csharp
static void AltTranslation()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="construct-the-uri"></a>Vytvoření identifikátoru URI

Přidejte tyto `AltTranslation` řádky do funkce. Všimněte si, že `api-version`spolu s , byly deklarovány dva další parametry. Tyto parametry slouží k nastavení překladového vstupu a výstupu. V této ukázce`en`se jedná`es`o angličtinu ( ) a španělštinu ( ).

```csharp
string route = "/dictionary/lookup?api-version=3.0";
static string params_ = "from=en&to=es";
static string uri = endpoint + path + params_;
```

Dále musíme vytvořit a serializovat objekt JSON, který obsahuje text, který chcete přeložit. Mějte na paměti, můžete předat více `body` než jeden objekt v poli.

```csharp
System.Object[] body = new System.Object[] { new { Text = @"Elephants" } };
var requestBody = JsonConvert.SerializeObject(body);
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
* Vložení těla požadavku (serializovaný objekt JSON)
* Přidání požadovaných záhlaví
* Vytvoření asynchronního požadavku
* Tisk odpovědi

Přidejte tento `HttpRequestMessage`kód do :

```csharp
// Set the method to POST
request.Method = HttpMethod.Post;

// Construct the full URI
request.RequestUri = new Uri(uri);

// Add the serialized JSON object to your request
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");

// Add the authorization header
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;

// Print the response
Console.WriteLine(PrettyPrint(jsonResponse));
Console.WriteLine("Press any key to continue.");
```

Přidat `PrettyPrint` pro přidání formátování do odpovědi JSON:
```csharp
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

Pokud používáte předplatné služeb Cognitive Services s více `Ocp-Apim-Subscription-Region` službami, musíte také zahrnout parametry požadavku. [Přečtěte si další informace o ověřování pomocí předplatného s více službami](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

Posledním krokem je `AltTranslation()` volání `Main` funkce. Vyhledejte `static void Main(string[] args)` a přidejte tyto řádky:

```csharp
AltTranslation();
Console.WriteLine("Press any key to continue.");
Console.ReadKey();
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

To je ono, jste připraveni spustit ukázkovou aplikaci. Z příkazového řádku (nebo terminálové relace) přejděte do adresáře projektu a spusťte:

```console
dotnet run
```

## <a name="sample-response"></a>Ukázková odpověď

```json
[
    {
        "displaySource": "elephants",
        "normalizedSource": "elephants",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "elephants",
                        "frequencyCount": 1207,
                        "normalizedText": "elephants",
                        "numExamples": 5
                    }
                ],
                "confidence": 1.0,
                "displayTarget": "elefantes",
                "normalizedTarget": "elefantes",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nezapomeňte odebrat všechny důvěrné informace ze zdrojového kódu ukázkové aplikace, jako jsou klíče předplatného.

## <a name="next-steps"></a>Další kroky

Podívejte se na odkaz rozhraní API pochopit vše, co můžete dělat s překladačem text api.

> [!div class="nextstepaction"]
> [referenční dokumentace k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
