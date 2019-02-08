---
title: 'Rychlý start: Převést převod textu na řeč, .NET Core – hlasové služby'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu zjistíte jak převést převod textu na řeč pomocí rozhraní REST API pro převod textu na řeč. Ukázkový text v tomto průvodci strukturovaná jako jazyk pro značky syntézu řeči (SSML). To umožňuje zvolit hlasu a jazyk odpovědi řeči.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 0086746ee676127a3fcae9e7b05efd3f7a9f65f3
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878421"
---
# <a name="quickstart-convert-text-to-speech-using-net-core"></a>Rychlý start: Převést převod textu na řeč pomocí .NET Core

V tomto rychlém startu se dozvíte jak převést převod textu na řeč pomocí .NET Core a rozhraní REST API pro převod textu na řeč. Ukázkový text v tomto průvodci má strukturu [řeči syntézu Markup Language (SSML)](speech-synthesis-markup.md), která umožňuje výběr hlasu a jazyk odpovědi.

Tento rychlý start vyžaduje [účtu služeb Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem Speech Service. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Sada .NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), nebo vašem oblíbeném textovém editoru
* Klíč rozhraní předplatné Azure pro službu rozpoznávání řeči

## <a name="create-a-net-core-project"></a>Vytvoření projektu .NET Core

Otevřete nový příkazový řádek (nebo relaci Terminálové služby) a spusťte tyto příkazy:

```console
dotnet new console -o tts-sample
cd tts-sample
```

První příkaz provede dvě věci. Vytvoří novou konzolovou aplikaci .NET a vytvoří adresář s názvem `tts-sample`. Druhý příkaz změní adresář pro projekt.

## <a name="select-the-c-language-version"></a>Vyberte C# jazykovou verzi

Tento rychlý start vyžaduje C# 7.1 nebo novější. Existuje několik způsobů, jak změnit C# verze pro váš projekt. V tomto průvodci, vám ukážeme, jak upravit `tts-sample.csproj` souboru. Všechny dostupné možnosti, jako je například změna jazyka v aplikaci Visual Studio, naleznete v části [vyberte C# jazykovou verzi](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Otevřete svůj projekt a pak otevřete `tts-sample.csproj`. Ujistěte se, že `LangVersion` je nastavená na 7.1 nebo novější. Pokud není k dispozici skupina vlastností jazykové verze, přidejte tyto řádky:

```csharp
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

Ujistěte se, že změny uložte.

## <a name="add-required-namespaces-to-your-project"></a>Do projektu přidejte požadované obory názvů

`dotnet new console` Příkaz, který byl dříve vytvořili projekt, včetně `Program.cs`. Tento soubor je místo, kam budete dáte kódu aplikace. Otevřít `Program.cs`a nahraďte existující příkazy using. Tyto příkazy Ujistěte se, že máte přístup ke všem typům, které jsou potřebné k sestavení a spuštění ukázkové aplikace.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.IO;
using System.Threading.Tasks;
```

## <a name="create-a-class-for-token-exchange"></a>Vytvořte třídu pro výměnu tokenů

Převod textu na řeč rozhraní REST API vyžaduje přístupového tokenu pro ověřování. Chcete-li získat přístupový token, je potřeba systému exchange. Tato ukázka vymění váš klíč předplatného Speech Service token přístupu k použití `issueToken` koncového bodu.

Tento příklad předpokládá, že je vaše předplatné Speech Service v oblasti západní USA. Pokud používáte jiné oblasti, aktualizujte hodnotu pro `FetchTokenUri`. Úplný seznam najdete v tématu [oblastech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

```csharp
public class Authentication
{
    private string subscriptionKey;
    private string tokenFetchUri;

    public Authentication(string tokenFetchUri, string subscriptionKey)
    {
        if (string.IsNullOrWhiteSpace(tokenFetchUri))
        {
            throw new ArgumentNullException(nameof(tokenFetchUri));
        }
        if (string.IsNullOrWhiteSpace(subscriptionKey))
        {
            throw new ArgumentNullException(nameof(subscriptionKey));
        }
        this.tokenFetchUri = tokenFetchUri;
        this.subscriptionKey = subscriptionKey;
    }

    public async Task<string> FetchTokenAsync()
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(this.tokenFetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null).ConfigureAwait(false);
            return await result.Content.ReadAsStringAsync().ConfigureAwait(false);
        }
    }
}
```

> [!NOTE]
> Další informace o ověřování najdete v tématu [ověřování pomocí přístupového tokenu](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="get-an-access-token-and-set-the-host-url"></a>Získání přístupového tokenu a nastavit adresu URL pro hostitele

Vyhledejte `static void Main(string[] args)` a nahraďte ho hodnotou `static async Task Main(string[] args)`.

V dalším kroku zkopírujte tento kód do metody main. Provádí několik věcí, ale co je nejdůležitější, trvá text jako vstup a volání `Authentication` funkce k výměně váš klíč předplatného pro přístupový token. Pokud dojde k nějaké chybě, chyba je vytištěna do konzoly.

Ujistěte se, že chcete přidat klíč předplatného. před spuštěním aplikace.

```csharp
// Prompts the user to input text for TTS conversion
Console.Write("What would you like to convert to speech? ");
string text = Console.ReadLine();

// Gets an access token
string accessToken;
Console.WriteLine("Attempting token exchange. Please wait...\n");

// Add your subscription key here
// If your resource isn't in WEST US, change the endpoint
Authentication auth = new Authentication("https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken", "YOUR_SUBSCRIPTION_KEY");
try
{
    accessToken = await auth.FetchTokenAsync().ConfigureAwait(false);
    Console.WriteLine("Successfully obtained an access token. \n");
}
catch (Exception ex)
{
    Console.WriteLine("Failed to obtain an access token.");
    Console.WriteLine(ex.ToString());
    Console.WriteLine(ex.Message);
    return;
}
```

Nastavte hostitele a tras pro převod textu na řeč:

```csharp
string host = "https://westus.tts.speech.microsoft.com/cognitiveservices/v1";
```

## <a name="build-the-ssml-request"></a>Žádost o SSML sestavení

Text se odešle jako text `POST` požadavku. U SSML můžete zadat hlasu. V tomto rychlém startu použijeme SSML jazyk nastavena na `en-US` a hlasu nastavit jako `ZiraRUS`. Pojďme vytvořit SSML za vaši žádost:

```csharp
string body = @"<speak version='1.0' xmlns='http://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
              <voice name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>" +
              text + "</voice></speak>";
```

> [!NOTE]
> Tento příklad používá `ZiraRUS` hlasového písma. Pro úplný seznam Microsoft poskytuje hlasy/jazyky, naleznete v tématu [jazykovou podporu](language-support.md). Pokud vás zajímá vytváření jedinečné, rozpoznat hlas pro hodnotu značky, přečtěte si téma [vytváření vlastního hlasového písma](how-to-customize-voice-font.md).

## <a name="instantiate-the-client-make-a-request-and-save-synthesized-audio-to-a-file"></a>Vytvořit instanci klienta, vytvoříte žádost o a uložit do souboru syntetizovaný zvuku

Je mnohem více v této ukázce kódu. Pojďme rychle se podívat na co se děje:

* Jsou vytvořena instance klienta a požadavek.
* Metoda protokolu HTTP je nastaven jako `POST`.
* Požadované záhlaví jsou přidáno k žádosti.
* Žádost se poslala a kontroluje stavový kód.
* Odpověď je asynchronně číst datové části a zapsána do souboru s názvem sample.wav.

Zkopírujte tento kód do vašeho projektu. Nezapomeňte nahradit hodnoty `User-Agent` záhlaví s názvem prostředku na webu Azure Portal.

```csharp
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Set the HTTP method
        request.Method = HttpMethod.Post;
        // Construct the URI
        request.RequestUri = new Uri(host);
        // Set the content type header
        request.Content = new StringContent(body, Encoding.UTF8, "application/ssml+xml");
        // Set additional header, such as Authorization and User-Agent
        request.Headers.Add("Authorization", "Bearer " + accessToken);
        request.Headers.Add("Connection", "Keep-Alive");
        // Update your resource name
        request.Headers.Add("User-Agent", "YOUR_RESOURCE_NAME");
        request.Headers.Add("X-Microsoft-OutputFormat", "riff-24khz-16bit-mono-pcm");
        // Create a request
        Console.WriteLine("Calling the TTS service. Please wait... \n");
        using (var response = await client.SendAsync(request).ConfigureAwait(false))
        {
            response.EnsureSuccessStatusCode();
            // Asynchronously read the response
            using (var dataStream = await response.Content.ReadAsStreamAsync().ConfigureAwait(false))
            {
                Console.WriteLine("Your speech file is being written to file...");
                using (var fileStream = new FileStream(@"sample.wav", FileMode.Create, FileAccess.Write, FileShare.Write))
                {
                    await dataStream.CopyToAsync(fileStream).ConfigureAwait(false);
                    fileStream.Close();
                }
                Console.WriteLine("\nYour file is ready. Press any key to exit.");
                Console.ReadLine();
            }
        }
    }
}
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Je to, jste připraveni ke spuštění aplikace pro převod textu na řeč. Z příkazového řádku (nebo relaci Terminálové služby) přejděte do adresáře vašeho projektu a spusťte:

```console
dotnet run
```

V případě úspěšného ověření řeči soubor je uložen ve složce vašeho projektu. Přehrávání pomocí vašeho oblíbeného přehrávač.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste do svého programu pevně zakódovali klíč předplatného, nezapomeňte po dokončení tohoto rychlého startu tento klíč předplatného odebrat.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky .NET na Githubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NETCore)

## <a name="see-also"></a>Další informace najdete v tématech

* [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)
* [Vytvoření vlastního hlasového písma](how-to-customize-voice-font.md)
* [Záznam hlasu ukázky vytvořte vlastní hlasové](record-custom-voice-samples.md)
