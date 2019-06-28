---
title: 'Rychlý start: Získání délky věty C# – Translator Text API'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu zjistíte jak určit délka větu pomocí rozhraní Translator Text API a .NET Core.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/13/2019
ms.author: swmachan
ms.openlocfilehash: 27f515c775d4b1a77563ac10338fc2255e42731c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448183"
---
# <a name="quickstart-use-the-translator-text-api-to-determine-sentence-length-using-c"></a>Rychlý start: Použití rozhraní Translator Text API určit pomocí délka větuC#

V tomto rychlém startu se dozvíte určení délky větu pomocí .NET Core, C# 7.1 nebo novější a rozhraní Translator Text API.

K tomuto rychlému startu potřebujete [účet služby Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby Translator Text. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

* C#7.1 nebo novější
* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Balíček NuGet Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), nebo vašem oblíbeném textovém editoru
* Klíč předplatného Azure pro službu Translator Text

## <a name="create-a-net-core-project"></a>Vytvoření projektu .NET Core

Otevřete nový příkazový řádek (nebo relaci Terminálové služby) a spusťte tyto příkazy:

```console
dotnet new console -o sentences-sample
cd sentences-sample
```

První příkaz provede dvě věci. Vytvoří novou konzolovou aplikaci .NET a vytvoří adresář s názvem `sentences-sample`. Druhý příkaz změní adresář pro projekt.

V dalším kroku budete muset nainstalovat Json.Net. Z adresáře vašeho projektu spusťte:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="select-the-c-language-version"></a>Vyberte C# jazykovou verzi

Tento rychlý start vyžaduje C# 7.1 nebo novější. Existuje několik způsobů, jak změnit C# verze pro váš projekt. V tomto průvodci, vám ukážeme, jak upravit `sentences-sample.csproj` souboru. Všechny dostupné možnosti, jako je například změna jazyka v aplikaci Visual Studio, naleznete v části [vyberte C# jazykovou verzi](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Otevřete svůj projekt a pak otevřete `sentences-sample.csproj`. Ujistěte se, že `LangVersion` je nastavená na 7.1 nebo novější. Pokud není k dispozici skupina vlastností jazykové verze, přidejte tyto řádky:

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

## <a name="add-required-namespaces-to-your-project"></a>Do projektu přidejte požadované obory názvů

`dotnet new console` Příkaz, který byl dříve vytvořili projekt, včetně `Program.cs`. Tento soubor je místo, kam budete dáte kódu aplikace. Otevřít `Program.cs`a nahraďte existující příkazy using. Tyto příkazy Ujistěte se, že máte přístup ke všem typům, které jsou potřebné k sestavení a spuštění ukázkové aplikace.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
// Install Newtonsoft.Json with NuGet
using Newtonsoft.Json;
```

## <a name="create-classes-for-the-json-response"></a>Vytvoření třídy pro odpověď JSON

V dalším kroku vytvoříme vytvořit třídu, která se používá při deserializaci odpověď JSON vrátil Translator Text API.

```csharp
/// <summary>
/// The C# classes that represents the JSON returned by the Translator Text API.
/// </summary>
public class BreakSentenceResult
{
    public int[] SentLen { get; set; }
    public DetectedLanguage DetectedLanguage { get; set; }
}

public class DetectedLanguage
{
    public string Language { get; set; }
    public float Score { get; set; }
}
```

## <a name="create-a-function-to-determine-sentence-length"></a>Vytvoření funkce k určení délky větu

V rámci `Program` třídy, vytvořte funkci s názvem `BreakSentence()`. Tato funkce přebírá čtyři argumenty: `subscriptionKey`, `host`, `route`, a `inputText`.

```csharp
static public async Task BreakSentenceRequest(string subscriptionKey, string host, string route, string inputText)
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="serialize-the-break-sentence-request"></a>Serializovat věty přerušení.

Dále je třeba vytvořit a serializaci objektu JSON, který obsahuje text. Mějte na paměti, můžete předat více než jeden objekt `body` pole.

```csharp
object[] body = new object[] { new { Text = inputText } };
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
// Build the request.
// Set the method to POST
request.Method = HttpMethod.Post;
// Construct the URI and add headers.
request.RequestUri = new Uri(host + route);
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send the request and get response.
HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
// Read response as a string.
string result = await response.Content.ReadAsStringAsync();
// Deserialize the response using the classes created earlier.
BreakSentenceResult[] deserializedOutput = JsonConvert.DeserializeObject<BreakSentenceResult[]>(result);
foreach (BreakSentenceResult o in deserializedOutput)
{
    Console.WriteLine("The detected language is '{0}'. Confidence is: {1}.", o.DetectedLanguage.Language, o.DetectedLanguage.Score);
    Console.WriteLine("The first sentence length is: {0}", o.SentLen[0]);
}
```

Pokud používáte víc služeb předplatného služeb Cognitive Services, musíte taky zahrnout `Ocp-Apim-Subscription-Region` v parametry požadavku. [Další informace o dvojúrovňovém víc služeb předplatného](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication). 

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

Posledním krokem je volání `BreakSentenceRequest()` v `Main` funkce. Vyhledejte `static void Main(string[] args)` a nahraďte tento kód:

```csharp
static async Task Main(string[] args)
{
    // This is our main function.
    // Output languages are defined in the route.
    // For a complete list of options, see API reference.
    string subscriptionKey = "YOUR_TRANSLATOR_TEXT_KEY_GOES_HERE";
    string host = "https://api.cognitive.microsofttranslator.com";
    string route = "/breaksentence?api-version=3.0";
    // Feel free to use any string.
    string breakSentenceText = @"How are you doing today? The weather is pretty pleasant. Have you been to the movies lately?";
    await BreakSentenceRequest(subscriptionKey, host, route, breakSentenceText);
}
```

Můžete si všimnout, že v `Main`, budete deklarace `subscriptionKey`, `host`, `route`a text, který se vyhodnotí `breakSentenceText`.

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Je to, jste připraveni spustit ukázkovou aplikaci. Z příkazového řádku (nebo relaci Terminálové služby) přejděte do adresáře vašeho projektu a spusťte:

```console
dotnet run
```

## <a name="sample-response"></a>Ukázková odpověď

Po spuštění ukázky byste měli vidět následující tisk do terminálu:

```bash
The detected language is \'en\'. Confidence is: 1.
The first sentence length is: 25
```

Tato zpráva je sestaven z nezpracovaném formátu JSON, který bude vypadat například takto:

```json
[
    {
        "detectedLanguage":
        {
            "language":"en",
            "score":1.0
        },
        "sentLen":[25,32,35]
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

* [Překlad textu](quickstart-csharp-translate.md)
* [Transliterace textu](quickstart-csharp-transliterate.md)
* [Identifikace jazyka podle vstupu](quickstart-csharp-detect.md)
* [Získání alternativních překladů](quickstart-csharp-dictionary.md)
* [Získání seznamu podporovaných jazyků](quickstart-csharp-languages.md)
* [Určení délky věty ze vstupu](quickstart-csharp-sentences.md)
