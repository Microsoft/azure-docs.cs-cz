---
title: 'Rychlý start: C#Sada SDK dotaz předpovědi koncového bodu'
titleSuffix: Azure Cognitive Services
description: Použití C# SDK odesílat utterance uživatele k LUIS a přijímat predikcí.
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: diberry
ms.openlocfilehash: e009ef159a51a22c56409cd1564fa9fde3c67b55
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56405558"
---
# <a name="quickstart-query-prediction-endpoint-with-c-net-sdk"></a>Rychlý start: Dotazy předpovědi koncový bod s C# sady .NET SDK

Použití sady .NET SDK, nalezeno na [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/), pro odesílání utterance uživatele Language Understanding (LUIS) a přijímání predikcí záměru uživatele. 

V tomto rychlém startu odešle utterance uživatele, jako například `turn on the bedroom light`, do veřejné aplikace Language Understanding, pak obdrží odhadu a zobrazí záměr nejvyšší hodnocení `HomeAutomation.TurnOn` a entitou `HomeAutomation.Room` v rámci utterance nebyl nalezen. 

## <a name="prerequisites"></a>Požadavky

* [Visual Studio Community 2017](https://visualstudio.microsoft.com/vs/community/)
* Programovací jazyk C# (je součástí sady VS Community 2017)
* ID veřejné aplikace: df67dcdb-c37d-46af-88e1-8b97951ca1c2

> [!Note]
> Kompletní řešení je k dispozici [cognitive services – language porozumění](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/sdk-quickstarts/c%23/UsePredictionRuntime) úložiště GitHub.

Hledáte další dokumentaci?

 * [Referenční dokumentace k sadě SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)


## <a name="get-cognitive-services-or-language-understanding-key"></a>Získání klíče služeb Cognitive Services nebo Language Understanding

Chcete-li použít veřejné aplikace pro domácí automatizace, budete potřebovat platný klíč pro koncový bod předpovědi. Můžete použít buď služeb Cognitive Services klíč (vytvořené pod pomocí Azure CLI), které platí pro mnoho služeb cognitive services, nebo `Language Understanding` klíč. 

Pomocí následujících [rozhraní příkazového řádku Azure k vytvoření klíče služby Cognitive Services](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create):

```azurecli-interactive
az cognitiveservices account create \
    -n my-cog-serv-resource \
    -g my-cog-serv-resource-group \
    --kind CognitiveServices \
    --sku S0 \
    -l WestEurope \ 
    --yes
```

## <a name="create-net-core-project"></a>Vytvoření projektu .NET Core

Vytvořte projekt konzoly .NET Core v sadě Visual Studio Community 2017.

1. Open Visual Studio Community 2017.
1. Vytvoření nového projektu z **Visual C#**  zvolte **Konzolová aplikace (.NET Core)**.
1. Zadejte název projektu `QueryPrediction`, ponechejte zbývající výchozí hodnoty a vyberte **OK**.
    Tím se vytvoří jednoduchý projekt s primární kód soubor s názvem **Program.cs**.

## <a name="add-sdk-with-nuget"></a>Přidejte sadu SDK s NuGet

1. V **Průzkumníka řešení**, vyberte projekt v zobrazení stromu s názvem **QueryPrediction**, klepněte pravým tlačítkem myši. V nabídce vyberte **spravovat balíčky NuGet...** .
1. Vyberte **Procházet** zadejte `Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime`. Jakmile se zobrazí informace o balíčku, vyberte **nainstalovat** k instalaci balíčku do projektu. 
1. Přidejte následující _pomocí_ příkazy k hornímu okraji **Program.cs**. Neodebírejte existující _pomocí_ příkaz pro `System`. 

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

## <a name="create-a-new-method-for-the-prediction"></a>Vytvoření nové metody pro předpověď

Vytvoření nové metody, `GetPrediction` odeslat dotaz na koncový bod dotaz předpovědi. Metoda vytvoří a konfigurace všech nezbytných objektů potom vrátí `Task` s [ `LuisResult` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.luisresult?view=azure-dotnet) predikované výsledky. 

```csharp
static async  Task<LuisResult> GetPrediction() {
}
```

## <a name="create-credentials-object"></a>Vytvořte objekt přihlašovacích údajů

Přidejte následující kód, který `GetPrediction` metodu pro vytvoření přihlašovacích údajů klienta klíč služby Cognitive Services.

Nahraďte `<REPLACE-WITH-YOUR-KEY>` s oblastí klíč služby Cognitive Services. Klíč má [webu Azure portal](https://portal.azure.com) na stránce klíče pro daný prostředek.

```csharp
// Use Language Understanding or Cognitive Services key
// to create authentication credentials
var endpointPredictionkey = "<REPLACE-WITH-YOUR-KEY>";
var credentials = new ApiKeyServiceClientCredentials(endpointPredictionkey);
```

## <a name="create-language-understanding-client"></a>Vytvoření klienta služby Language Understanding

V `GetPrediction` metodu po předchozím kódu přidejte následující kód pomocí nových přihlašovacích údajů, vytváření [ `LUISRuntimeClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Runtime_LUISRuntimeClient__ctor_Microsoft_Rest_ServiceClientCredentials_System_Net_Http_DelegatingHandler___) objektu klienta. 

Nahraďte `<REPLACE-WITH-YOUR-KEY-REGION>` s oblastí vašeho klíče, jako například `westus`. Klíčové oblasti se [webu Azure portal](https://portal.azure.com) na stránce s přehledem pro daný prostředek.

```csharp
// Create Luis client and set endpoint
// region of endpoint must match key's region, for example `westus`
var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { });
luisClient.Endpoint = "https://<REPLACE-WITH-YOUR-KEY-REGION>.api.cognitive.microsoft.com";
```

## <a name="set-query-parameters"></a>Nastavit parametry dotazu

V `GetPrediction` metodu po předchozím kódu přidejte následující kód můžete nastavit parametry dotazu.

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

V `GetPrediction` metodu po předchozím kódu přidejte následující kód můžete nastavit parametry dotazu:

```csharp
// Create prediction client
var prediction = new Prediction(luisClient);

// get prediction
return await prediction.ResolveAsync(appId, query, timezoneOffset, verbose, staging, spellCheck, bingSpellCheckKey, log, CancellationToken.None);
```

## <a name="display-prediction-results"></a>Zobrazit výsledky předpovědí

Změnit **hlavní** metodu pro zavolání nové `GetPrediction` metoda a vraťte se předpověď výsledků:

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

Sestavení projektu v sadě Studio a spuštění projektu se výstup dotazu:

```console
Query:'turn on the bedroom light'
Top intent is 'HomeAutomation.TurnOn' with score 0.809439957
HomeAutomation.Room:'bedroom' begins at position 12 and ends at position 18
```

## <a name="next-steps"></a>Další postup

Další informace o [sady .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) a [referenční dokumentace k .NET](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet). 

> [!div class="nextstepaction"] 
> [Kurz: Sestavení aplikace LUIS k určení záměry uživatele](luis-quickstart-intents-only.md) 