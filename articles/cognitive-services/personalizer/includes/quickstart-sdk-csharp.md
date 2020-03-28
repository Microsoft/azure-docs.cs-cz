---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 7fd63841cabd91d46dd311f571fd100bbcfdd0fe
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122884"
---
[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [knihovny](https://github.com/Azure-Samples/cognitive-services-personalizer-samples) [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Aktuální verze rozhraní [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="using-this-quickstart"></a>Pomocí tohoto rychlého startu

Tento rychlý start lze použít několika kroky:

* Na webu Azure Portal vytvořte prostředek personalisty
* Na webu Azure Portal pro prostředek personalikátoru na stránce **Konfigurace** změňte frekvenci aktualizace modelu na velmi krátký interval.
* V editoru kódu vytvořte soubor kódu a upravte soubor kódu
* V příkazovém řádku nebo terminálu nainstalujte sdk z příkazového řádku
* V příkazovém řádku nebo terminálu spusťte soubor kódu

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-c-application"></a>Vytvoření nové aplikace C#

Vytvořte novou aplikaci .NET Core ve vašem upřednostňovaném editoru nebo ide.

V okně konzoly (například cmd, PowerShell nebo Bash) vytvořte pomocí příkazu `new` `personalizer-quickstart`dotnet novou konzolovou aplikaci s názvem . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" `Program.cs`s jedním zdrojovým souborem: .

```console
dotnet new console -n personalizer-quickstart
```

Změňte adresář do nově vytvořené složky aplikace. Aplikaci můžete sestavit pomocí:

```console
dotnet build
```

Výstup sestavení by měl obsahovat žádná upozornění nebo chyby.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="install-the-sdk"></a>Instalace sady SDK

V adresáři aplikace nainstalujte klientskou knihovnu Personalisty pro rozhraní .NET pomocí následujícího příkazu:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

Pokud používáte IDE sady Visual Studio, klientská knihovna je k dispozici jako balíček NuGet ke stažení.

## <a name="object-model"></a>Objektový model

Klient Personalista je objekt [PersonalistaClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) který se ověřuje v Azure pomocí Microsoft.Rest.ServiceClientCredentials, který obsahuje váš klíč.

Chcete-li požádat o jednu nejlepší položku obsahu, vytvořte [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)a předejte jej [klientovi. Rank](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) metoda. Rank Metoda vrátí RankResponse.

Chcete-li odeslat skóre odměny personalistovi, vytvořte [požadavek na odměnu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)a předejte jej [klientovi. Metoda odměny.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview)

Určení skóre odměny je v tomto rychlém startu triviální. Ve výrobním systému, určení toho, co má vliv na [skóre odměny](../concept-rewards.md) a o kolik může být složitý proces, který se můžete rozhodnout změnit v průběhu času. Toto rozhodnutí o návrhu by mělo být jedním z primárních rozhodnutí v architektuře personalisty.

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující úkoly s klientskou knihovnou Personalista pro rozhraní .NET:

* [Vytvoření klienta personalisty](#create-a-personalizer-client)
* [Pořadí API](#request-the-best-action)
* [Rozhraní API pro odměny](#send-a-reward)

## <a name="add-the-dependencies"></a>Přidání závislostí

V adresáři projektu otevřete **soubor Program.cs** v upřednostňovaném editoru nebo rozhraní IDE. Nahraďte `using` existující kód `using` následujícími směrnicemi:

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Přidání informací o prostředcích personalisty

Ve třídě **Program** vytvořte proměnné pro klíč Azure a koncový bod vašeho prostředku, který je vytažen z proměnných prostředí s názvem `PERSONALIZER_RESOURCE_KEY` a `PERSONALIZER_RESOURCE_ENDPOINT`. Pokud jste vytvořili proměnné prostředí po spuštění aplikace, editor, IDE nebo prostředí běží bude nutné zavřít a znovu načíst pro přístup k proměnné. Metody budou vytvořeny později v tomto rychlém startu.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Vytvoření klienta personalisty

Dále vytvořte metodu pro vrácení klienta personalisty. Parametr metody je `PERSONALIZER_RESOURCE_ENDPOINT` a ApiKey je `PERSONALIZER_RESOURCE_KEY`.

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Získejte potraviny jako rankable akce

Akce představují volby obsahu, ze kterých má personalikátor vybrat nejlepší položku obsahu. Přidejte do třídy Program následující metody, které představují sadu akcí a jejich funkcí.

[!code-csharp[Food items as actions](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>Získání uživatelských předvoleb pro kontext

Přidejte do třídy Program následující metody, abyste získali vstup uživatele z příkazového řádku pro denní dobu a aktuální předvolbu potravin. Ty budou použity jako kontextové funkce.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Obě metody `GetKey` používají metodu ke čtení výběru uživatele z příkazového řádku.

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>Vytvoření výukové smyčky

Učební smyčka personalisty je cyklus volání [hodnosti](#request-the-best-action) a [odměny.](#send-a-reward) V tomto rychlém startu následuje každé volání hodnosti pro přizpůsobení obsahu po výzvě odměny, která personalistovi sdělí, jak si služba vedla.

Následující kód prochází cyklem dotazování uživatele o jejich preference na příkazovém řádku, odesílání těchto informací personalistovi vybrat nejlepší akci, předložení výběru zákazníkovi vybrat ze seznamu, pak odeslání maty Personalizátor signalizace, jak dobře služba udělala ve svém výběru.

[!code-csharp[Learning loop](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=mainLoop)]

Před spuštěním souboru kódu přidejte následující metody, které [získají volby obsahu](#get-food-items-as-rankable-actions):

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>Vyžádejte si nejlepší akci

Chcete-li dokončit požadavek na pořadí, program požádá `currentContent` uživatele o předvolby k vytvoření volby obsahu. Proces může vytvořit obsah, který má `excludeActions`být vyloučen z akcí, zobrazený jako . Rank požadavek potřebuje akce a jejich funkce, currentContext funkce, excludeActions a jedinečné ID události, přijímat odpovědi.

Tento rychlý start má jednoduché kontextové vlastnosti denní doby a preference uživatelského jídla. Ve výrobních systémech může být určení a [vyhodnocení](../concept-feature-evaluation.md) [akcí a funkcí](../concepts-features.md) netriviální záležitostí.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Poslat odměnu

Chcete-li získat skóre odměny k odeslání žádosti o odměnu, program získá výběr uživatele z příkazového řádku, přiřadí výběru číselnou hodnotu a poté odešle jedinečné ID události a skóre odměny jako číselnou hodnotu do rozhraní API odměny.

Tento rychlý start přiřadí jednoduché číslo jako skóre odměny, buď nulu nebo 1. Ve výrobních systémech může být určení, kdy a co poslat na [výzvu Odměna,](../concept-rewards.md) netriviální záležitostí v závislosti na vašich konkrétních potřebách.

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>Spuštění programu

Spusťte aplikaci pomocí `run` příkazu dotnet z adresáře aplikace.

```console
dotnet run
```

![Program rychlého startu se zeptá na několik otázek, aby shromáždil uživatelské předvolby, známé jako funkce, a poté poskytuje nejvyšší akci.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

[Zdrojový kód pro tento rychlý start](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) je k dispozici v personalizer ukázky GitHub úložiště.
