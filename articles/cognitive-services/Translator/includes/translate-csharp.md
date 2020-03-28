---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 64a934196bb964561f36b9d95a2467b149847225
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69906727"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Vytvoření projektu .NET Core

Otevřete nový příkazový řádek (nebo terminálovou relaci) a spusťte tyto příkazy:

```console
dotnet new console -o translate-sample
cd translate-sample
```

První příkaz dělá dvě věci. Vytvoří novou aplikaci konzoly .NET a `translate-sample`vytvoří adresář s názvem . Druhý příkaz se změní do adresáře pro váš projekt.

Dále budete muset nainstalovat Json.Net. Z adresáře projektu spusťte:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Výběr jazykové verze jazyka C#

Tento rychlý start vyžaduje C# 7.1 nebo novější. Existuje několik způsobů, jak změnit verzi jazyka C# pro váš projekt. V této příručce vám ukážeme, `translate-sample.csproj` jak soubor upravit. Všechny dostupné možnosti, jako je například změna jazyka v sadě Visual Studio, naleznete [v tématu Výběr jazykové verze jazyka C#](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Otevřete projekt a `translate-sample.csproj`otevřete . Ujistěte `LangVersion` se, že je nastavena na 7.1 nebo novější. Pokud pro jazykovou verzi neexistuje skupina vlastností, přidejte tyto řádky:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Přidání požadovaných oborů názvů do projektu

Příkaz, `dotnet new console` který jste dříve spustili, vytvořil projekt, včetně `Program.cs`. Tento soubor je místo, kam vložíte kód aplikace. Otevřete `Program.cs`a nahraďte existující příkazy using. Tyto příkazy zajistit, že máte přístup ke všem typům potřebné k sestavení a spuštění ukázkové aplikace.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Vytvořit třídy pro odpověď JSON

Dále vytvoříme sadu tříd, které se používají při deserializaci odpovědi JSON vrácené textovým rozhraním TRANSLATOR API.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class TranslationResult
{
    public DetectedLanguage DetectedLanguage { get; set; }
    public TextResult SourceText { get; set; }
    public Translation[] Translations { get; set; }
}

public class DetectedLanguage
{
    public string Language { get; set; }
    public float Score { get; set; }
}

public class TextResult
{
    public string Text { get; set; }
    public string Script { get; set; }
}

public class Translation
{
    public string Text { get; set; }
    public TextResult Transliteration { get; set; }
    public string To { get; set; }
    public Alignment Alignment { get; set; }
    public SentenceLength SentLen { get; set; }
}

public class Alignment
{
    public string Proj { get; set; }
}

public class SentenceLength
{
    public int[] SrcSentLen { get; set; }
    public int[] TransSentLen { get; set; }
}
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

## <a name="create-a-function-to-translate-text"></a>Vytvoření funkce pro překlad textu

Ve `Program` třídě vytvořte asynchronní funkci `TranslateTextRequest()`nazvanou . Tato funkce má čtyři `subscriptionKey` `host`argumenty: , , `route`a `inputText`.

```csharp
// This sample requires C# 7.1 or later for async/await.
// Async call to the Translator Text API
static public async Task TranslateTextRequest(string subscriptionKey, string endpoint, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-translation-request"></a>Serializace žádosti o překlad

Dále musíme vytvořit a serializovat objekt JSON, který obsahuje text, který chcete přeložit. Mějte na paměti, můžete předat více `body`než jeden objekt v .

```csharp
object[] body = new object[] { new { Text = inputText } };
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
* Tisk odpovědi pomocí dříve vytvořených tříd

Přidejte tento `HttpRequestMessage`kód do :

```csharp
// Build the request.
// Set the method to Post.
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
TranslationResult[] deserializedOutput = JsonConvert.DeserializeObject<TranslationResult[]>(result);
// Iterate over the deserialized results.
foreach (TranslationResult o in deserializedOutput)
{
    // Print the detected input language and confidence score.
    Console.WriteLine("Detected input language: {0}\nConfidence score: {1}\n", o.DetectedLanguage.Language, o.DetectedLanguage.Score);
    // Iterate over the results and print each translation.
    foreach (Translation t in o.Translations)
    {
        Console.WriteLine("Translated to {0}: {1}", t.To, t.Text);
    }
}
```

Pokud používáte předplatné služeb Cognitive Services s více `Ocp-Apim-Subscription-Region` službami, musíte také zahrnout parametry požadavku. [Přečtěte si další informace o ověřování pomocí předplatného s více službami](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

Posledním krokem je `TranslateTextRequest()` volání `Main` funkce. V této ukázce překládáme`de`do němčiny ( ), italštiny (`it`), japonštiny (`ja`) a thajštiny (`th`). Vyhledejte `static void Main(string[] args)` a nahraďte jej tímto kódem:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    // https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate
    string route = "/translate?api-version=3.0&to=de&to=it&to=ja&to=th";
    // Prompts you for text to translate. If you'd prefer, you can
    // provide a string as textToTranslate.
    Console.Write("Type the phrase you'd like to translate? ");
    string textToTranslate = Console.ReadLine();
    await TranslateTextRequest(subscriptionKey, endpoint, route, textToTranslate);
    Console.WriteLine("Press any key to continue.");
    Console.ReadKey();
}
```

Všimněte si, `Main`že v , `subscriptionKey`jste `endpoint`deklarování , a `route`. Kromě toho budete vyzváni k zadání `Console.Readline()` uživatele s a `textToTranslate`přiřazení hodnoty .

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

To je ono, jste připraveni spustit ukázkovou aplikaci. Z příkazového řádku (nebo terminálové relace) přejděte do adresáře projektu a spusťte:

```console
dotnet run
```

## <a name="sample-response"></a>Ukázková odpověď

Po spuštění ukázky, měli byste vidět následující vytištěno na terminálu:

```bash
Detected input language: en
Confidence score: 1

Translated to de: Hallo Welt!
Translated to it: Salve, mondo!
Translated to ja: ハローワールド！
Translated to th: หวัดดีชาวโลก!
```

Tato zpráva je sestavena z raw JSON, který bude vypadat takto:

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
      },
      {
        "text": "ハローワールド！",
        "to": "ja"
      },
      {
        "text": "หวัดดีชาวโลก!",
        "to": "th"
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
