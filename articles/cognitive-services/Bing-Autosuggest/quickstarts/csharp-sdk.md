---
title: 'Rychlý Start: Klientská knihovna Automatické návrhy Bingu pro .NET | Microsoft Docs'
description: Začínáme s klientskou knihovnou Automatické návrhy Bingu pro .NET a získat návrhy hledání na základě částečných řetězců dotazů.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 12/20/2019
ms.author: aahi
ms.openlocfilehash: 6bcd89422188d8c0064547c37b4d425f47dd6496
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473924"
---
# <a name="quickstart-bing-autosuggest-client-library-for-net"></a>Rychlý Start: Klientská knihovna Automatické návrhy Bingu pro .NET

Začněte s klientskou knihovnou Automatické návrhy Bingu pro .NET. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy.

Pomocí klientské knihovny Automatické návrhy Bingu pro .NET můžete získat návrhy hledání na základě částečných řetězců dotazů.

[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet) | [ukázkového kódu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/BingAutoSuggest/Program.cs) knihovny pro | [balíčků (NuGet | )](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.AutoSuggest/) pro [zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingAutoSuggest)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Nastavení

### <a name="create-an-azure-resource"></a>Vytvoření prostředku Azure

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

### <a name="create-an-environment-variable"></a>Vytvoření proměnné prostředí

>[!NOTE]
> Koncové body pro prostředky nevyužívající zkušební verzi vytvořené po 1. červenci 2019 používají vlastní formát subdomény, který vidíte níže. Další informace a úplný seznam regionálních koncových bodů najdete v tématu [názvy vlastních subdomén pro Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Pomocí klíče a koncového bodu z prostředku, který jste vytvořili, vytvořte dvě proměnné prostředí pro ověřování:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY` – klíč prostředku pro ověřování vašich požadavků.
* `AUTOSUGGEST_ENDPOINT` – koncový bod prostředku pro odesílání požadavků rozhraní API. Bude vypadat takto: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Použijte pokyny pro váš operační systém.
<!-- replace the below endpoint and key examples -->
#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Po přidání proměnné prostředí restartujte okno konzoly.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bashrc`, aby se změna projevila.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Upravte `.bash_profile`a přidejte proměnnou prostředí:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source .bash_profile`, aby se změna projevila.
***

### <a name="create-a-new-c-application"></a>Vytvoření nové C# aplikace

Vytvořte novou aplikaci .NET Core v upřednostňovaném editoru nebo integrovaném vývojovém prostředí (IDE). 

V okně konzoly (například cmd, PowerShell nebo bash) použijte příkaz `dotnet new` a vytvořte novou konzolovou aplikaci s názvem `bing-autosuggest-quickstart`. Tento příkaz vytvoří jednoduchý projekt "Hello World" C# s jedním zdrojovým souborem: *program.cs*. 

```console
dotnet new console -n bing-autosuggest-quickstart
```

Změňte adresář na nově vytvořenou složku aplikace. Aplikaci můžete vytvořit pomocí:

```console
dotnet build
```

Výstup sestavení by neměl obsahovat žádná upozornění ani chyby. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

V adresáři projektu otevřete soubor *program.cs* v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Přidejte následující direktivy `using`:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
```

Ve třídě `Program` vytvořte proměnné pro koncový bod a klíč Azure prostředku. Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, budete muset zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k této proměnné.

```csharp
private const string key_var = "AUTOSUGGEST_SUBSCRIPTION_KEY";
private static readonly string subscription_key = Environment.GetEnvironmentVariable(key_var);

// Note you must use the same region as you used to get your subscription key.
private const string endpoint_var = "AUTOSUGGEST_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);
```

Do `Main` metody aplikace přidejte následující volání metody, které budete definovat později.

```csharp
static void Main(string[] args)
{
    Task.WaitAll(RunQuickstart());
    Console.WriteLine("Press any key to exit.");
    Console.Read();
}
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny

V adresáři aplikace nainstalujte Automatické návrhy Bingu klientskou knihovnu pro .NET pomocí následujícího příkazu:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Search.AutoSuggest --version 2.0.0
```

Pokud používáte integrované vývojové prostředí (IDE) sady Visual Studio, je knihovna klienta k dispozici jako balíček NuGet ke stažení.

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny Automatické návrhy Bingu pro .NET:

* [Ověření klienta](#authenticate-the-client)
* [Odeslat žádost o Automatický návrh](#send-an-autosuggest-request)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> V tomto rychlém startu se předpokládá, že jste [vytvořili proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro automatické návrhy Bingu klíč s názvem `AUTOSUGGEST_SUBSCRIPTION_KEY`a jeden pro koncový bod s názvem `AUTOSUGGEST_ENDPOINT`.


V nové asynchronní metodě vytvoří instanci klienta s vaším koncovým bodem a klíčem. Vytvořte objekt [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.apikeyserviceclientcredentials?view=azure-dotnet) s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [AutosuggestClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclient?view=azure-dotnet) .

```csharp
async static Task RunQuickstart()
{
    // Generate the credentials and create the client.
    var credentials = new Microsoft.Azure.CognitiveServices.Search.AutoSuggest.ApiKeyServiceClientCredentials(subscription_key);
    var client = new AutoSuggestClient(credentials, new System.Net.Http.DelegatingHandler[] { })
    {
        Endpoint = endpoint
    };
}
```

## <a name="send-an-autosuggest-request"></a>Odeslat žádost o Automatický návrh

Ve stejné metodě odešlete dotaz do Bingu pomocí metody [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) klienta. Pak proiterujte odpověď na [návrhy](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) a vytiskněte první návrh.

```csharp
var result = await client.AutoSuggestMethodAsync("xb");
var groups = result.SuggestionGroups;
if (groups.Count > 0) {
    var group = groups[0];
    Console.Write("First suggestion group: {0}\n", group.Name);
    var suggestions = group.SearchSuggestions;
    if (suggestions.Count > 0)
    {
        Console.WriteLine("First suggestion:");
        Console.WriteLine("Query: {0}", suggestions[0].Query);
        Console.WriteLine("Display text: {0}", suggestions[0].DisplayText);
    }
    else
    {
        Console.WriteLine("No suggestions found in this group.");
    }
}
else
{
    Console.WriteLine("No suggestions found.");
}
```

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci z adresáře aplikace pomocí příkazu `dotnet run`.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz rozhraní API pro automatické návrhy Bingu](../tutorials/autosuggest.md)

## <a name="see-also"></a>Další informace najdete v tématech

- [Co jsou automatické návrhy Bingu?](../get-suggested-search-terms.md)
- [Odkaz na Automatické návrhy Bingu dotnet](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet)
