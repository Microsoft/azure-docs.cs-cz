---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 513de6d990884f9abf2378ea208ec1dbe556d397
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587130"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Vytvoření projektu .NET Core

Otevřete nový příkazový řádek (nebo relaci terminálu) a spusťte tyto příkazy:

```console
dotnet new console -o detect-sample
cd detect-sample
```

První příkaz provede dvě věci. Vytvoří novou konzolovou aplikaci .NET a vytvoří adresář s názvem `detect-sample` . Druhý příkaz změní adresář pro váš projekt.

V dalším kroku budete muset nainstalovat Json.Net. Z adresáře projektu spusťte:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Vyberte verzi jazyka C#.

Tento rychlý Start vyžaduje C# 7,1 nebo novější. Existuje několik způsobů, jak změnit verzi v jazyce C# pro váš projekt. V této příručce vám ukážeme, jak `detect-sample.csproj` soubor upravit. Všechny dostupné možnosti, jako je například změna jazyka v aplikaci Visual Studio, najdete v tématu [Výběr jazykové verze jazyka C#](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Otevřete projekt a pak otevřete `detect-sample.csproj` . Ujistěte se, že `LangVersion` je nastavená na 7,1 nebo novější. Pokud pro danou jazykovou verzi není skupina vlastností, přidejte tyto řádky:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Přidání požadovaných oborů názvů do projektu

`dotnet new console`Příkaz, který jste spustili dříve, vytvořil projekt, včetně `Program.cs` . Do tohoto souboru umístíte kód aplikace. Otevřete `Program.cs` a nahraďte existující příkazy using. Tyto příkazy zajistí, že máte přístup ke všem typům vyžadovaným pro sestavení a spuštění ukázkové aplikace.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Vytvoření tříd pro odpověď JSON

Nyní vytvoříme třídu, která se používá při deserializaci odpovědi JSON vrácené překladatelem.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator.
/// </summary>
public class DetectResult
{
    public string Language { get; set; }
    public float Score { get; set; }
    public bool IsTranslationSupported { get; set; }
    public bool IsTransliterationSupported { get; set; }
    public AltTranslations[] Alternatives { get; set; }
}
public class AltTranslations
{
    public string Language { get; set; }
    public float Score { get; set; }
    public bool IsTranslationSupported { get; set; }
    public bool IsTransliterationSupported { get; set; }
}
```

## <a name="get-subscription-information-from-environment-variables"></a>Získání informací o předplatném z proměnných prostředí

Do třídy přidejte následující řádky `Program` . Tyto řádky čtou klíč předplatného a koncový bod z proměnných prostředí a vyvolá chybu, pokud narazíte na nějaké problémy.

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

## <a name="create-a-function-to-detect-the-source-texts-language"></a>Vytvoření funkce pro detekci jazyka zdrojového textu

Ve `Program` třídě vytvořte funkci s názvem `DetectTextRequest()` . Tato třída zapouzdřuje kód používaný k volání prostředku Detect a vytiskne výsledek do konzoly.

```csharp
static public async Task DetectTextRequest(string subscriptionKey, string endpoint, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-detect-request"></a>Serializace žádosti o detekci

Dále je potřeba vytvořit a serializovat objekt JSON, který obsahuje text, který se bude předrobit detekci jazyka.

```csharp
System.Object[] body = new System.Object[] { new { Text = inputText } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Vytvoření instance klienta a vytvoření žádosti

Tyto řádky vytváří instanci `HttpClient` a `HttpRequestMessage` :

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Sestavte požadavek a vytiskněte odpověď.

V rámci `HttpRequestMessage` budete:

* Deklarace metody HTTP
* Sestavit identifikátor URI žádosti
* Vložit text žádosti (serializovaný objekt JSON)
* Přidat požadovaná záhlaví
* Vytvořit asynchronní požadavek
* Tisk odpovědi

Přidejte tento kód do `HttpRequestMessage` :

```csharp
// Build the request.
request.Method = HttpMethod.Post;
// Construct the URI and add headers.
request.RequestUri = new Uri(endpoint + route);
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send the request and get response.
HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
// Read response as a string.
string result = await response.Content.ReadAsStringAsync();
// Deserialize the response using the classes created earlier.
DetectResult[] deserializedOutput = JsonConvert.DeserializeObject<DetectResult[]>(result);
// Iterate over the deserialized response.
foreach (DetectResult o in deserializedOutput)
{
    Console.WriteLine("The detected language is '{0}'. Confidence is: {1}.\nTranslation supported: {2}.\nTransliteration supported: {3}.\n",
        o.Language, o.Score, o.IsTranslationSupported, o.IsTransliterationSupported);
    // Create a counter
    int counter = 0;
    // Iterate over alternate translations.
    foreach (AltTranslations a in o.Alternatives)
    {
        counter++;
        Console.WriteLine("Alternative {0}", counter);
        Console.WriteLine("The detected language is '{0}'. Confidence is: {1}.\nTranslation supported: {2}.\nTransliteration supported: {3}.\n",
            a.Language, a.Score, a.IsTranslationSupported, a.IsTransliterationSupported);
    }
}
```

Pokud používáte Cognitive Services předplatné s více službami, musíte taky zahrnout `Ocp-Apim-Subscription-Region` do parametrů žádosti. [Přečtěte si další informace o ověřování pomocí předplatného s více službami](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

Posledním krokem je volání `DetectTextRequest()` `Main` funkce. Vyhledejte `static void Main(string[] args)` ho a nahraďte ho tímto kódem:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    string route = "/detect?api-version=3.0";
    string detectSentenceText = @"How are you doing today? The weather is pretty pleasant. Have you been to the movies lately?";
    await DetectTextRequest(subscriptionKey, endpoint, route, detectSentenceText);
    Console.WriteLine("Press any key to continue.");
    Console.ReadKey();
}
```
## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

To je to, že jste připraveni spustit ukázkovou aplikaci. Z příkazového řádku (nebo relace Terminálové služby) přejděte do adresáře projektu a spusťte příkaz:

```console
dotnet run
```

## <a name="sample-response"></a>Ukázková odpověď

Po spuštění ukázky by se měla zobrazit následující tištěná do terminálu:

> [!NOTE]
> V tomto [seznamu jazyků](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)Najděte zkratku země/oblasti.

```bash
The detected language is 'en'. Confidence is: 1.
Translation supported: True.
Transliteration supported: False.

Alternative 1
The detected language is 'fil'. Confidence is: 0.82.
Translation supported: True.
Transliteration supported: False.

Alternative 2
The detected language is 'ro'. Confidence is: 1.
Translation supported: True.
Transliteration supported: False.
```

Tato zpráva je sestavena z nezpracovaného formátu JSON, který bude vypadat takto:

```json
[  
    {  
        "language":"en",
        "score":1.0,
        "isTranslationSupported":true,
        "isTransliterationSupported":false,
        "alternatives":[  
            {  
                "language":"fil",
                "score":0.82,
                "isTranslationSupported":true,
                "isTransliterationSupported":false
            },
            {  
                "language":"ro",
                "score":1.0,
                "isTranslationSupported":true,
                "isTransliterationSupported":false
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nezapomeňte ze zdrojového kódu ukázkové aplikace odebrat všechny důvěrné informace, jako jsou klíče předplatného.

## <a name="next-steps"></a>Další kroky

Podívejte se na reference k rozhraní API, abyste porozuměli všem, co můžete s překladatelem dělat.

> [!div class="nextstepaction"]
> [referenční dokumentace k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
