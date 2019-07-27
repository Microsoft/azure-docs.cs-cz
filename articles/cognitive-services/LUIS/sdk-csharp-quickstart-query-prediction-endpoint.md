---
title: 'Rychlý start: C#Koncový bod předpovědi dotazu sady SDK – LUIS'
titleSuffix: Azure Cognitive Services
description: Pomocí C# sady SDK můžete odeslat uživatele UTTERANCE do Luis a získat předpovědi.
author: diberry
manager: nitinme
ms.service: cognitive-services
services: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: c0b534848232d60929722e2036f69f4b6e670a4a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563146"
---
# <a name="quickstart-query-prediction-endpoint-with-c-net-sdk"></a>Rychlý start: Koncový bod předpovědi dotazu C# pomocí sady .NET SDK

Pomocí sady .NET SDK, která se nachází na [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/), můžete odeslat uživatele utterance do Language UNDERSTANDING (Luis) a získat předpověď záměru uživatele. 

V tomto rychlém startu se pošle uživatelem `turn on the bedroom light`utterance, jako je například, do veřejné aplikace Language Understanding, potom obdrží předpověď a zobrazí záměr `HomeAutomation.TurnOn` nejvyšší bodování a entitu `HomeAutomation.Room` , která se nachází v rámci utterance. 

## <a name="prerequisites"></a>Požadavky

* [Visual Studio Community 2017](https://visualstudio.microsoft.com/vs/community/)
* Programovací jazyk C# (je součástí sady VS Community 2017)
* ID veřejné aplikace: df67dcdb-c37d-46af-88e1-8b97951ca1c2

> [!Note]
> Kompletní řešení je k dispozici v úložišti GitHubu pro [rozpoznávání služeb – jazyk – porozumění](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/sdk-quickstarts/c%23/UsePredictionRuntime) .

Hledáte další dokumentaci?

 * [Referenční dokumentace k sadě SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)


## <a name="get-cognitive-services-or-language-understanding-key"></a>Získat Cognitive Services nebo Language Understanding klíč

Aby bylo možné používat veřejnou aplikaci pro automatizaci domů, potřebujete platný klíč pro koncový bod předpovědi. Můžete použít Cognitive Services klíč (vytvořený níže pomocí Azure CLI), který je platný pro mnoho služeb rozpoznávání, nebo `Language Understanding` klíč. 

Pomocí následujícího [příkazu rozhraní příkazového řádku Azure vytvořte klíč služby pro rozpoznávání](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create):

```azurecli-interactive
az cognitiveservices account create \
    -n my-cog-serv-resource \
    -g my-cog-serv-resource-group \
    --kind CognitiveServices \
    --sku S0 \
    -l WestEurope \ 
    --yes
```

## <a name="create-net-core-project"></a>Vytvořit projekt .NET Core

Vytvořte projekt konzoly .NET Core v aplikaci Visual Studio Community 2017.

1. Otevřete Visual Studio Community 2017.
1. Vytvořte nový projekt, v části **vizuál C#**  vyberte **aplikace konzoly (.NET Core)** .
1. Zadejte název `QueryPrediction`projektu, ponechte zbývající výchozí hodnoty a vyberte **OK**.
    Tím se vytvoří jednoduchý projekt s primárním souborem kódu s názvem **program.cs**.

## <a name="add-sdk-with-nuget"></a>Přidání sady SDK s NuGet

1. V **Průzkumník řešení**vyberte projekt ve stromovém zobrazení s názvem **QueryPrediction**a potom klikněte pravým tlačítkem myši. V nabídce vyberte **Spravovat balíčky NuGet...** .
1. Vyberte **Procházet** a pak `Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime`zadejte. Až se zobrazí informace o balíčku, vyberte **instalovat** a nainstalujte balíček do projektu. 
1. Do horní části **program.cs**přidejte následující příkazy _using_ . Neodstraňujte existující příkaz _using_ pro `System`. 

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

## <a name="create-a-new-method-for-the-prediction"></a>Vytvořit novou metodu pro předpověď

Vytvořte novou metodu `GetPrediction` pro odeslání dotazu do koncového bodu předpovědi dotazu. Metoda vytvoří a nakonfiguruje všechny potřebné objekty a potom vrátí `Task` [`LuisResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.luisresult?view=azure-dotnet) výsledek předpovědi. 

```csharp
static async  Task<LuisResult> GetPrediction() {
}
```

## <a name="create-credentials-object"></a>Vytvořit objekt přihlašovacích údajů

Přidejte následující kód do `GetPrediction` metody pro vytvoření přihlašovacích údajů klienta s klíčem služby pro rozpoznávání.

Nahraďte `<REPLACE-WITH-YOUR-KEY>` oblastí vašeho klíče služby vyvnímání. Klíč je v [Azure Portal](https://portal.azure.com) na stránce klíče pro daný prostředek.

```csharp
// Use Language Understanding or Cognitive Services key
// to create authentication credentials
var endpointPredictionkey = "<REPLACE-WITH-YOUR-KEY>";
var credentials = new ApiKeyServiceClientCredentials(endpointPredictionkey);
```

## <a name="create-language-understanding-client"></a>Vytvořit klienta Language Understanding

V metodě za předchozí kód přidejte následující kód pro použití nových přihlašovacích údajů a [`LUISRuntimeClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Runtime_LUISRuntimeClient__ctor_Microsoft_Rest_ServiceClientCredentials_System_Net_Http_DelegatingHandler___) vytvoření objektu klienta. `GetPrediction` 

Nahraďte `<REPLACE-WITH-YOUR-KEY-REGION>` oblastí klíče, `westus`jako je například. Oblast klíče je [Azure Portal](https://portal.azure.com) na stránce Přehled pro daný prostředek.

```csharp
// Create Luis client and set endpoint
// region of endpoint must match key's region, for example `westus`
var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { });
luisClient.Endpoint = "https://<REPLACE-WITH-YOUR-KEY-REGION>.api.cognitive.microsoft.com";
```

## <a name="set-query-parameters"></a>Nastavit parametry dotazu

`GetPrediction` V metodě, za předchozí kód, přidejte následující kód pro nastavení parametrů dotazu.

```csharp
// public Language Understanding Home Automation app
var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

// query specific to home automation app
var query = "turn on the bedroom light";

// common settings for remaining parameters
Double? timezoneOffset = null;
var verbose = true;
var staging = false;
var spellCheck = false;
String bingSpellCheckKey = null;
var log = false;
```

## <a name="query-prediction-endpoint"></a>Koncový bod předpovědi dotazů

`GetPrediction` V metodě po předchozím kódu přidejte následující kód pro nastavení parametrů dotazu:

```csharp
// Create prediction client
var prediction = new Prediction(luisClient);

// get prediction
return await prediction.ResolveAsync(appId, query, timezoneOffset, verbose, staging, spellCheck, bingSpellCheckKey, log, CancellationToken.None);
```

## <a name="display-prediction-results"></a>Zobrazit výsledky předpovědi

Změňte metodu **Main** pro volání nové `GetPrediction` metody a vraťte výsledky předpovědi:

```csharp
static void Main(string[] args)
{

    var luisResult = GetPrediction().Result;

    // Display query
    Console.WriteLine("Query:'{0}'", luisResult.Query);

    // Display most common properties of query result
    Console.WriteLine("Top intent is '{0}' with score {1}", luisResult.TopScoringIntent.Intent,luisResult.TopScoringIntent.Score);

    // Display all entities detected in query utterance
    foreach (var entity in luisResult.Entities)
    {
        Console.WriteLine("{0}:'{1}' begins at position {2} and ends at position {3}", entity.Type, entity.Entity, entity.StartIndex, entity.EndIndex);
    }

    Console.Write("done");

}
```

## <a name="run-the-project"></a>Spuštění projektu

Sestavte projekt v studiu a spusťte projekt na výstup dotazu:

```console
Query:'turn on the bedroom light'
Top intent is 'HomeAutomation.TurnOn' with score 0.809439957
HomeAutomation.Room:'bedroom' begins at position 12 and ends at position 18
```

## <a name="next-steps"></a>Další postup

Přečtěte si další informace o sadě [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) a [referenční dokumentaci k rozhraní .NET](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet). 

> [!div class="nextstepaction"] 
> [Kurz: Sestavování aplikace LUIS pro určení záměrů uživatelů](luis-quickstart-intents-only.md) 