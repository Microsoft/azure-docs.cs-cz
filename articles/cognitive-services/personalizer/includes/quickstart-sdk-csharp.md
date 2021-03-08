---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 08/25/2020
ms.openlocfilehash: f98c34a53de60adc7866c1b57918232b966a0783
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445327"
---
[Referenční dokumentace](/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/)  |  [Ukázky](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Jakmile budete mít předplatné Azure, vytvořte prostředek přizpůsobeného přizpůsobeného <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title=" "  target="_blank"> prostředku </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API pro přizpůsobení budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

[!INCLUDE [Change model frequency](change-model-frequency.md)]

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace v C#

Vytvořte novou aplikaci .NET Core v upřednostňovaném editoru nebo integrovaném vývojovém prostředí (IDE).

V okně konzoly (například cmd, PowerShell nebo bash) `new` vytvořte pomocí příkazu dotnet novou konzolovou aplikaci s názvem `personalizer-quickstart` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: `Program.cs` .

```console
dotnet new console -n personalizer-quickstart
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

### <a name="install-the-client-library"></a>Instalace klientské knihovny

V adresáři aplikace nainstalujte klientskou knihovnu pro přizpůsobování pro .NET pomocí následujícího příkazu:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

> [!TIP]
> Pokud používáte integrované vývojové prostředí (IDE) sady Visual Studio, je knihovna klienta k dispozici jako balíček NuGet ke stažení.

V adresáři projektu otevřete `Program.cs` soubor v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Přidejte následující direktivy using:

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
using Microsoft.Azure.CognitiveServices.Personalizer.Models;
using System;
using System.Collections.Generic;
using System.Linq;
```

## <a name="object-model"></a>Objektový model

Klient pro přizpůsobování je objekt [PersonalizerClient](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient) , který se ověřuje v Azure pomocí Microsoft. REST. ServiceClientCredentials, který obsahuje váš klíč.

Pokud chcete požádat o jednu nejlepší položku obsahu, vytvořte [RankRequest](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest)a pak ji předejte [klientovi. Rank](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank) – metoda Metoda Rank vrátí RankResponse.

Chcete-li odeslat skóre odměňování, vytvořte [RewardRequest](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest)a pak ho předejte [klientovi. Metoda odměňování](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward) .

Stanovení skóre odměňování je v tomto rychlém startu triviální. V produkčním systému se určení toho, co ovlivňuje [skóre záměna](../concept-rewards.md) a kolik může být složitý proces, se může stát, že se v průběhu času rozhodnete změnit. Toto rozhodnutí o návrhu by mělo být jedním z primárních rozhodnutí v architektuře pro přizpůsobování.

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny pro přizpůsobování pro .NET:

* [Vytvoření klienta přizpůsobeného pro přizpůsobování](#authenticate-the-client)
* [Rozhraní API pro řazení](#request-the-best-action)
* [API pro odměnu](#send-a-reward)


## <a name="authenticate-the-client"></a>Ověření klienta

V této části provedete dvě věci:
* Zadejte svůj klíč a koncový bod.
* Vytvoření klienta přizpůsobeného pro přizpůsobování

Začněte přidáním následujících řádků do vaší třídy programu. Nezapomeňte přidat klíč a koncový bod z prostředku vlastního nastavení.

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```csharp
private static readonly string ApiKey = "REPLACE-WITH-YOUR-PERSONALIZER-KEY";
private static readonly string ServiceEndpoint = "https://REPLACE-WITH-YOUR-PERSONALIZER-RESOURCE-NAME.cognitiveservices.azure.com";
```

Dále do programu přidejte metodu pro vytvoření nového klienta pro přizpůsobování.

```csharp
static PersonalizerClient InitializePersonalizerClient(string url)
{
    PersonalizerClient client = new PersonalizerClient(
        new ApiKeyServiceClientCredentials(ApiKey)) { Endpoint = url };

    return client;
}
```

## <a name="get-food-items-as-rankable-actions"></a>Získání položek stravy jako operací s pořadím

Akce reprezentují volby obsahu, ze kterých chcete přizpůsobovat výběr nejlepší položky obsahu. Do třídy program přidejte následující metody, které reprezentují sadu akcí a jejich funkcí. 

```csharp
static IList<RankableAction> GetActions()
{
    IList<RankableAction> actions = new List<RankableAction>
    {
        new RankableAction
        {
            Id = "pasta",
            Features =
            new List<object>() { new { taste = "salty", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
        },

        new RankableAction
        {
            Id = "ice cream",
            Features =
            new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
        },

        new RankableAction
        {
            Id = "juice",
            Features =
            new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
        },

        new RankableAction
        {
            Id = "salad",
            Features =
            new List<object>() { new { taste = "salty", spiceLevel = "low" }, new { nutritionLevel = 8 } }
        }
    };

    return actions;
}
```

## <a name="get-user-preferences-for-context"></a>Získat předvolby uživatele pro kontext

Přidejte následující metody do třídy program pro získání vstupu uživatele z příkazového řádku pro denní a aktuální preference v potravinách. Budou použity jako kontextové funkce.

```csharp
static string GetUsersTimeOfDay()
{
    string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

    Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
    if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
        timeIndex = 1;
    }

    return timeOfDayFeatures[timeIndex - 1];
}
```

```csharp
static string GetUsersTastePreference()
{
    string[] tasteFeatures = new string[] { "salty", "sweet" };

    Console.WriteLine("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet");
    if (!int.TryParse(GetKey(), out int tasteIndex) || tasteIndex < 1 || tasteIndex > tasteFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + tasteFeatures[0] + ".");
        tasteIndex = 1;
    }

    return tasteFeatures[tasteIndex - 1];
}
```

Obě metody používají `GetKey` metodu ke čtení výběru uživatele z příkazového řádku.

```csharp
private static string GetKey()
{
    return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
}
```

## <a name="create-the-learning-loop"></a>Vytvoření výukové smyčky

Výuková smyčka pro přizpůsobování je cyklem volání [pořadí](#request-the-best-action) a [odměňování](#send-a-reward) . V tomto rychlém startu každé volání pořadí pro přizpůsobení obsahu je následováno voláním odměna, které přizpůsobuje, jak dobře se služba provádí.

Následující smyčka kódu projde cyklem, který uživatele požádá o jejich Předvolby na příkazovém řádku, aby si vybrali nejlepší akci, a to tak, že si vybere výběr zákazníka, který si zvolí ze seznamu, a pak pošle skóre odměňování, jak dobře ve výběru služby.

```csharp
static void Main(string[] args)
{
    int iteration = 1;
    bool runLoop = true;

    IList<RankableAction> actions = GetActions();

    PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

    do
    {
        Console.WriteLine("\nIteration: " + iteration++);

        string timeOfDayFeature = GetUsersTimeOfDay();
        string tasteFeature = GetUsersTastePreference();

        IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature }
        };

        IList<string> excludeActions = new List<string> { "juice" };

        string eventId = Guid.NewGuid().ToString();

        var request = new RankRequest(actions, currentContext, excludeActions, eventId);
        RankResponse response = client.Rank(request);

        Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

        float reward = 0.0f;
        string answer = GetKey();

        if (answer == "Y")
        {
            reward = 1;
            Console.WriteLine("\nGreat! Enjoy your food.");
        }
        else if (answer == "N")
        {
            reward = 0;
            Console.WriteLine("\nYou didn't like the recommended food choice.");
        }
        else
        {
            Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
        }

        Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
        foreach (var rankedResponse in response.Ranking)
        {
            Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
        }

        client.Reward(response.EventId, new RewardRequest(reward));

        Console.WriteLine("\nPress q to break, any other key to continue:");
        runLoop = !(GetKey() == "Q");

    } while (runLoop);
}
```

Přidejte následující metody, které [získají volby obsahu](#get-food-items-as-rankable-actions)před spuštěním souboru kódu:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>Požádat o nejlepší akci

Chcete-li dokončit požadavek na řazení, program požádá o předvolby uživatele, aby vytvořil `currentContext` Výběr obsahu. Proces může vytvořit obsah, který se má vyloučit z akcí, které jsou zobrazené jako `excludeActions` . Požadavek na řazení potřebuje pro příjem odpovědi akce a jejich funkce, currentContext funkce, excludeActions a jedinečné ID události.

Tento rychlý Start má jednoduché kontextové funkce pro denní prioritu a uživatelské preference. V produkčních systémech může být určení a [vyhodnocení](../concept-feature-evaluation.md) [akcí a funkcí](../concepts-features.md) netriviální.

```csharp
string timeOfDayFeature = GetUsersTimeOfDay();
string tasteFeature = GetUsersTastePreference();

IList<object> currentContext = new List<object>() {
    new { time = timeOfDayFeature },
    new { taste = tasteFeature }
};

IList<string> excludeActions = new List<string> { "juice" };

string eventId = Guid.NewGuid().ToString();

var request = new RankRequest(actions, currentContext, excludeActions, eventId);
RankResponse response = client.Rank(request);
```

## <a name="send-a-reward"></a>Poslat odměnu

Chcete-li získat skóre odměňování v žádosti o odměnu, program získá výběr uživatele z příkazového řádku, přiřadí číselnou hodnotu výběru a pak odešle jedinečné ID události a skóre odměňování jako číselnou hodnotu pro rozhraní API pro odměnu.

V tomto rychlém startu se přiřadí jednoduché číslo jako skóre odměňování, buď nula, nebo 1. V produkčních systémech může být určení, kdy a co má být odesláno volání [odměna](../concept-rewards.md) , v závislosti na konkrétních potřebách netriviální povahy.

```csharp
float reward = 0.0f;
string answer = GetKey();

if (answer == "Y")
{
    reward = 1;
    Console.WriteLine("\nGreat! Enjoy your food.");
}
else if (answer == "N")
{
    reward = 0;
    Console.WriteLine("\nYou didn't like the recommended food choice.");
}
else
{
    Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
}

Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
foreach (var rankedResponse in response.Ranking)
{
    Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
}

// Send the reward for the action based on user response.
client.Reward(response.EventId, new RewardRequest(reward));
```

## <a name="run-the-program"></a>Spuštění programu

Spusťte aplikaci pomocí `run` příkazu dotnet z adresáře aplikace.

```console
dotnet run
```

![V programu rychlý Start se požádá o shromáždění uživatelských předvoleb, označovaných jako funkce, a pak poskytuje hlavní akci.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

[Zdrojový kód pro tento rychlý Start](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer) je k dispozici.
