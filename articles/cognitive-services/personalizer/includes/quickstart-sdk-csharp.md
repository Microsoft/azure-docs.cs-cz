---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 07/30/2020
ms.openlocfilehash: 41665193ab78f4d29785171ef3fee9a7ce1d9cea
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246500"
---
[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer)  |  [Balíček (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/)  |  [Ukázky](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="using-this-quickstart"></a>V tomto rychlém startu

V tomto rychlém startu se používá několik kroků:

* V Azure Portal vytvořte prostředek pro přizpůsobení.
* V Azure Portal u prostředku přizpůsobeného nástroji změňte na stránce **Konfigurace** četnost aktualizace modelu na velmi krátký interval.
* V editoru kódu vytvořte soubor kódu a upravte soubor s kódem.
* V příkazovém řádku nebo terminálu nainstalujte sadu SDK z příkazového řádku.
* V příkazovém řádku nebo terminálu spusťte soubor kódu.

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-c-application"></a>Vytvoření nové aplikace v C#

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

## <a name="install-the-sdk"></a>Instalace sady SDK

V adresáři aplikace nainstalujte klientskou knihovnu pro přizpůsobování pro .NET pomocí následujícího příkazu:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

Pokud používáte integrované vývojové prostředí (IDE) sady Visual Studio, je knihovna klienta k dispozici jako balíček NuGet ke stažení.

## <a name="object-model"></a>Objektový model

Klient pro přizpůsobování je objekt [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) , který se ověřuje v Azure pomocí Microsoft. REST. ServiceClientCredentials, který obsahuje váš klíč.

Pokud chcete požádat o jednu nejlepší položku obsahu, vytvořte [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)a pak ji předejte [klientovi. Rank](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) – metoda Metoda Rank vrátí RankResponse.

Chcete-li odeslat skóre odměňování, vytvořte [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)a pak ho předejte [klientovi. Metoda odměňování](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview) .

Stanovení skóre odměňování je v tomto rychlém startu triviální. V produkčním systému se určení toho, co ovlivňuje [skóre záměna](../concept-rewards.md) a kolik může být složitý proces, se může stát, že se v průběhu času rozhodnete změnit. Toto rozhodnutí o návrhu by mělo být jedním z primárních rozhodnutí v architektuře pro přizpůsobování.

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny pro přizpůsobování pro .NET:

* [Vytvoření klienta přizpůsobeného pro přizpůsobování](#create-a-personalizer-client)
* [Rozhraní API pro řazení](#request-the-best-action)
* [API pro odměnu](#send-a-reward)

## <a name="add-the-dependencies"></a>Přidat závislosti

V adresáři projektu otevřete soubor **program.cs** v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Nahraďte existující `using` kód následujícími `using` direktivami:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Přidat informace o prostředku pro přizpůsobení

Ve třídě **program** upravte klíč a proměnné koncového bodu směrem k hornímu okraji souboru kódu pro klíč a koncový bod prostředku Azure. 

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Vytvoření klienta přizpůsobeného pro přizpůsobování

Dále vytvořte metodu, která vrátí klienta přizpůsobeného pro personalizaci. Parametr metody je `PERSONALIZER_RESOURCE_ENDPOINT` a ApiKey je `PERSONALIZER_RESOURCE_KEY` .

[!code-csharp[Create the Personalizer client](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Získání položek stravy jako operací s pořadím

Akce reprezentují volby obsahu, ze kterých chcete přizpůsobovat výběr nejlepší položky obsahu. Do třídy program přidejte následující metody, které reprezentují sadu akcí a jejich funkcí. 

[!code-csharp[Food items as actions](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>Získat předvolby uživatele pro kontext

Přidejte následující metody do třídy program pro získání vstupu uživatele z příkazového řádku pro denní a aktuální preference v potravinách. Budou použity jako kontextové funkce.

[!code-csharp[Present time out day preference to the user](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=createUserFeatureTastePreference)]

Obě metody používají `GetKey` metodu ke čtení výběru uživatele z příkazového řádku.

[!code-csharp[Read user's choice from the command line](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>Vytvoření výukové smyčky

Výuková smyčka pro přizpůsobování je cyklem volání [pořadí](#request-the-best-action) a [odměňování](#send-a-reward) . V tomto rychlém startu každé volání pořadí pro přizpůsobení obsahu je následováno voláním odměna, které přizpůsobuje, jak dobře se služba provádí.

Následující smyčka kódu projde cyklem, který uživatele požádá o jejich Předvolby na příkazovém řádku, aby si vybrali nejlepší akci, a to tak, že si vybere výběr zákazníka, který si zvolí ze seznamu, a pak pošle skóre odměňování, jak dobře ve výběru služby.

[!code-csharp[Learning loop](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=mainLoop)]

Přidejte následující metody, které [získají volby obsahu](#get-food-items-as-rankable-actions)před spuštěním souboru kódu:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>Požádat o nejlepší akci

Chcete-li dokončit požadavek na řazení, program požádá o předvolby uživatele, aby vytvořil `currentContent` Výběr obsahu. Proces může vytvořit obsah, který se má vyloučit z akcí, které jsou zobrazené jako `excludeActions` . Požadavek na řazení potřebuje pro příjem odpovědi akce a jejich funkce, currentContext funkce, excludeActions a jedinečné ID události.

Tento rychlý Start má jednoduché kontextové funkce pro denní prioritu a uživatelské preference. V produkčních systémech může být určení a [vyhodnocení](../concept-feature-evaluation.md) [akcí a funkcí](../concepts-features.md) netriviální.

[!code-csharp[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Poslat odměnu

Chcete-li získat skóre odměňování v žádosti o odměnu, program získá výběr uživatele z příkazového řádku, přiřadí číselnou hodnotu výběru a pak odešle jedinečné ID události a skóre odměňování jako číselnou hodnotu pro rozhraní API pro odměnu.

V tomto rychlém startu se přiřadí jednoduché číslo jako skóre odměňování, buď nula, nebo 1. V produkčních systémech může být určení, kdy a co má být odesláno volání [odměna](../concept-rewards.md) , v závislosti na konkrétních potřebách netriviální povahy.

[!code-csharp[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=reward)]

## <a name="run-the-program"></a>Spuštění programu

Spusťte aplikaci pomocí `run` příkazu dotnet z adresáře aplikace.

```console
dotnet run
```

![V programu rychlý Start se požádá o shromáždění uživatelských předvoleb, označovaných jako funkce, a pak poskytuje hlavní akci.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

[Zdrojový kód pro tento rychlý Start](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer) je k dispozici.
