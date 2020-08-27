---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 0c74c3709848e4296f07844278b076fdc7ffa9bc
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88921399"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Vytvoření projektu .NET Core

Otevřete nový příkazový řádek (nebo relaci terminálu) a spusťte tyto příkazy:

```console
dotnet new console -o translate-sample
cd translate-sample
```

První příkaz provede dvě věci. Vytvoří novou konzolovou aplikaci .NET a vytvoří adresář s názvem `translate-sample` . Druhý příkaz změní adresář pro váš projekt.

V dalším kroku budete muset nainstalovat Json.Net. Z adresáře projektu spusťte:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Vyberte verzi jazyka C#.

Tento rychlý Start vyžaduje C# 7,1 nebo novější. Existuje několik způsobů, jak změnit verzi v jazyce C# pro váš projekt. V této příručce vám ukážeme, jak `translate-sample.csproj` soubor upravit. Všechny dostupné možnosti, jako je například změna jazyka v aplikaci Visual Studio, najdete v tématu [Výběr jazykové verze jazyka C#](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Otevřete projekt a pak otevřete `translate-sample.csproj` . Ujistěte se, že `LangVersion` je nastavená na 7,1 nebo novější. Pokud pro danou jazykovou verzi není skupina vlastností, přidejte tyto řádky:

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

Nyní vytvoříme sadu tříd, které se použijí při deserializaci odpovědi JSON vrácené překladatelem.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator.
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

## <a name="create-a-function-to-translate-text"></a>Vytvoření funkce pro překlad textu

Ve `Program` třídě Vytvořte asynchronní funkci s názvem `TranslateTextRequest()` . Tato funkce přijímá čtyři argumenty: `subscriptionKey` , `host` , a `route` `inputText` .

```csharp
// This sample requires C# 7.1 or later for async/await.
// Async call to the Translator
static public async Task TranslateTextRequest(string subscriptionKey, string endpoint, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-translation-request"></a>Serializace požadavku překladu

Dále je potřeba vytvořit a serializovat objekt JSON, který obsahuje text, který chcete přeložit. Mějte na paměti, že v nástroji můžete předat více než jeden objekt `body` .

```csharp
object[] body = new object[] { new { Text = inputText } };
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
* Vytiskněte odpověď pomocí tříd, které jste vytvořili dříve.

Přidejte tento kód do `HttpRequestMessage` :

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

Pokud používáte Cognitive Services předplatné s více službami, musíte taky zahrnout `Ocp-Apim-Subscription-Region` do parametrů žádosti. [Přečtěte si další informace o ověřování pomocí předplatného s více službami](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

Posledním krokem je volání `TranslateTextRequest()` `Main` funkce. V této ukázce se překládáme na němčinu ( `de` ), italštinu ( `it` ), japonštinu ( `ja` ) a thajštinu ( `th` ). Vyhledejte `static void Main(string[] args)` ho a nahraďte ho tímto kódem:

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

Všimněte si, že v `Main` , deklarujete `subscriptionKey` , `endpoint` a `route` . Kromě toho vyzýváte uživatele k zadání `Console.Readline()` a přiřazení hodnoty k `textToTranslate` .

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

To je to, že jste připraveni spustit ukázkovou aplikaci. Z příkazového řádku (nebo relace Terminálové služby) přejděte do adresáře projektu a spusťte příkaz:

```console
dotnet run
```

## <a name="sample-response"></a>Ukázková odpověď

Po spuštění ukázky by se měla zobrazit následující tištěná do terminálu:

```bash
Detected input language: en
Confidence score: 1

Translated to de: Hallo Welt!
Translated to it: Salve, mondo!
Translated to ja: ハローワールド！
Translated to th: หวัดดีชาวโลก!
```

Tato zpráva je sestavena z nezpracovaného formátu JSON, který bude vypadat takto:

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

Nezapomeňte ze zdrojového kódu ukázkové aplikace odebrat všechny důvěrné informace, jako jsou klíče předplatného.

## <a name="next-steps"></a>Další kroky

Podívejte se na reference k rozhraní API, abyste porozuměli všem, co můžete s překladatelem dělat.

> [!div class="nextstepaction"]
> [Referenční materiály k rozhraní API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
