---
title: 'Rychlý Start: Klientská knihovna pro přizpůsobování pro .NET'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak začít s klientskou knihovnou přizpůsobování pro .NET pomocí výukové smyčky.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: c17bf54d89e3a98ca667eeba40f2d2b166550833
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446396"
---
# <a name="quickstart-personalizer-client-library-for-net"></a>Rychlý Start: Klientská knihovna pro přizpůsobování pro .NET

Zobrazit přizpůsobený obsah C# v tomto rychlém startu pomocí služby pro přizpůsobení.

Začínáme s klientskou knihovnou pro přizpůsobování pro .NET Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy.

 * Seřadit seznam akcí pro přizpůsobení.
 * Vykázat skóre odměňování, které indikuje úspěšnost horní seřazené akce.

[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview) | [Ukázka](https://github.com/Azure-Samples/cognitive-services-personalizer-samples) | [ukázka zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [balíčků (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="using-this-quickstart"></a>V tomto rychlém startu

V tomto rychlém startu se používá několik kroků:

* V Azure Portal vytvořte prostředek pro přizpůsobení.
* V Azure Portal u prostředku přizpůsobeného nástroji změňte na stránce **Konfigurace** četnost aktualizace modelu.
* V editoru kódu vytvořte soubor kódu a upravte soubor s kódem.
* V příkazovém řádku nebo terminálu nainstalujte sadu SDK z příkazového řádku.
* V příkazovém řádku nebo terminálu spusťte soubor kódu.

## <a name="create-a-personalizer-azure-resource"></a>Vytvoření prostředku Azure pro přizpůsobování

Vytvořte prostředek pro přizpůsobení pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services) platný po dobu 7 dnů zdarma. Po registraci bude k dispozici na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Prohlédněte si prostředek na [Azure Portal](https://portal.azure.com/).

Po získání klíče ze zkušebního předplatného nebo prostředku vytvořte dvě [proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_RESOURCE_KEY` klíče prostředku.
* `PERSONALIZER_RESOURCE_ENDPOINT` pro koncový bod prostředku

V Azure Portal jsou hodnoty klíč i koncový bod k dispozici na stránce **rychlý Start** .

## <a name="change-the-model-update-frequency"></a>Změna frekvence aktualizace modelu

V Azure Portal změňte v prostředku přizpůsobeném na stránce **Konfigurace** **Četnost aktualizace modelu** na 10 sekund. Tato krátká doba bude službu vytvářet rychle, což vám umožní zjistit, jak se hlavní akce mění pro každou iteraci.

![Změna frekvence aktualizace modelu](./media/settings/configure-model-update-frequency-settings.png)

Když se poprvé vytvoří instance smyčky pro přizpůsobení, neexistuje žádný model, protože neexistovala žádná Neplatní volání rozhraní API pro vlak. Volání Rank budou pro každou položku vracet stejné pravděpodobnosti. Vaše aplikace by měla stále vždy Seřadit obsah pomocí výstupu RewardActionId.

## <a name="create-a-new-c-application"></a>Vytvoření nové C# aplikace

Vytvořte novou aplikaci .NET Core v upřednostňovaném editoru nebo integrovaném vývojovém prostředí (IDE). 

V okně konzoly (například cmd, PowerShell nebo bash) použijte příkaz dotnet `new` k vytvoření nové konzolové aplikace s názvem `personalizer-quickstart`. Tento příkaz vytvoří jednoduchý projekt "Hello World" C# s jedním zdrojovým souborem: `Program.cs`. 

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

Chcete-li se zeptat na rozměr obsahu, vytvořte [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)a pak ho předejte [klientovi. Rank](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) – metoda Metoda Rank vrátí RankResponse, který obsahuje seřazený obsah. 

Pokud chcete odeslat odměnu pro přizpůsobení, vytvořte [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)a pak ho předejte [klientovi. Metoda odměňování](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview) . 

Stanovení odměňování je v tomto rychlém startu triviální. V produkčním systému se určení toho, co ovlivňuje [skóre záměna](concept-rewards.md) a kolik může být složitý proces, se může stát, že se v průběhu času rozhodnete změnit. Toto rozhodnutí o návrhu by mělo být jedním z primárních rozhodnutí v architektuře pro přizpůsobování. 

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny pro přizpůsobování pro .NET:

* [Vytvoření klienta přizpůsobeného pro přizpůsobování](#create-a-personalizer-client)
* [Vyžádat pořadí](#request-a-rank)
* [Poslat odměnu](#send-a-reward)

## <a name="add-the-dependencies"></a>Přidat závislosti

V adresáři projektu otevřete soubor **program.cs** v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Nahraďte existující kód `using` následujícími direktivami `using`:

[!code-csharp[Using statements](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Přidat informace o prostředku pro přizpůsobení

Ve třídě **program** vytvořte proměnné pro klíč Azure prostředku a koncový bod načtený z proměnných prostředí s názvem `PERSONALIZER_RESOURCE_KEY` a `PERSONALIZER_RESOURCE_ENDPOINT`. Pokud jste po spuštění aplikace vytvořili proměnné prostředí, bude nutné editor, rozhraní IDE nebo prostředí, které je spuštěno, zavřít a znovu načíst pro přístup k proměnné. Metody budou vytvořeny později v tomto rychlém startu.

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Vytvoření klienta přizpůsobeného pro přizpůsobování

Dále vytvořte metodu, která vrátí klienta přizpůsobeného pro personalizaci. Parametr metody je `PERSONALIZER_RESOURCE_ENDPOINT` a ApiKey je `PERSONALIZER_RESOURCE_KEY`.

[!code-csharp[Create the Personalizer client](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Získání položek stravy jako operací s pořadím

Akce reprezentují volby obsahu, které má přizpůsobené přizpůsobovat. Do třídy program přidejte následující metody, které reprezentují sadu akcí, které je potřeba seřadit.

[!code-csharp[Food items as actions](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>Získat předvolby uživatele pro kontext

Přidejte následující metody do třídy program pro získání vstupu uživatele z příkazového řádku pro denní a aktuální preference v potravinách. Tyto akce budou použity jako kontext při řazení akcí.

[!code-csharp[Present time out day preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=createUserFeatureTastePreference)]

Obě metody používají metodu `GetKey` ke čtení výběru uživatele z příkazového řádku. 

[!code-csharp[Read user's choice from the command line](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>Vytvoření výukové smyčky

Výuková smyčka pro přizpůsobování je cyklem volání pořadí a odměňování. V tomto rychlém startu každé volání pořadí, pro přizpůsobení obsahu, je následováno volání odměna, které přizpůsobuje, jak dobře se služba přiřadí k obsahu. 

Následující kód v metodě `main` smyčky program projde cyklem, který uživateli požádá o jejich Předvolby na příkazovém řádku, pošle tyto informace do přizpůsobeného zařazení a prezentuje seřazený výběr zákazníkovi, který si zvolí ze seznamu, a pak odešle odměnu, jak dobře služba provedla řazení výběru.

[!code-csharp[Learning loop](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=mainLoop)]

Přidejte následující metody, které [získají volby obsahu](#get-food-items-as-rankable-actions)před spuštěním souboru kódu:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-a-rank"></a>Vyžádat pořadí

Chcete-li dokončit požadavek na řazení, program požádá o předvolby uživatele, aby vytvořil `currentContent` možností obsahu. Proces může vytvořit obsah, který se má vyloučit z rozsahu, který je zobrazený jako `excludeActions`. Požadavek na řazení potřebuje akce, currentContext, excludeActions a jedinečné ID události klasifikace (jako identifikátor GUID), aby mohl přijímat seřazené odpovědi. 

Tento rychlý Start má jednoduché kontextové funkce pro denní prioritu a uživatelské preference. V produkčních systémech může být určení a [vyhodnocení](concept-feature-evaluation.md) [akcí a funkcí](concepts-features.md) netriviální.  

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Poslat odměnu

Aby bylo možné dokončit požadavek na odměnu, program získá výběr uživatele z příkazového řádku, přiřadí číselné hodnoty každému výběru a pak pošle jedinečné ID události klasifikace a číselnou hodnotu k metodě odměňování.

V tomto rychlém startu se přiřadí jednoduché číslo jako odměna, buď nula, nebo 1. V produkčních systémech může být určení, kdy a co má být odesláno volání [odměna](concept-rewards.md) , v závislosti na konkrétních potřebách netriviální povahy. 

[!code-csharp[The Personalizer learning loop ranks the request.](~/samples-personalizer/quickstarts/csharp/PersonalizerExample/Program.cs?name=reward)]

## <a name="run-the-program"></a>Spuštění programu

Spusťte aplikaci pomocí příkazu dotnet `run` z adresáře aplikace.

```console
dotnet run
```

![V programu rychlý Start se požádá o shromáždění uživatelských předvoleb, označovaných jako funkce, a pak poskytuje hlavní akci.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

[Zdrojový kód pro tento rychlý Start](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/csharp/PersonalizerExample/Program.cs) je k dispozici v úložišti GitHub Samples Samples.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Jak přizpůsobovat práci](how-personalizer-works.md)

* [Co je přizpůsobování?](what-is-personalizer.md)
* [Kde můžete použít přizpůsobování?](where-can-you-use-personalizer.md)
* [Řešení potíží](troubleshooting.md)
