---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 53e6382cf8d046b2c9818b906890bc64642fd2ed
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371741"
---
Pomocí klientské knihovny pro vytváření jazyka (Language Understanding (LUIS) pro rozhraní .NET:

* Vytvoření aplikace
* Přidání záměrů, entit a ukázkových promluv
* Přidání funkcí, jako je seznam frází
* Trénování a publikování aplikace

[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) | [knihovny (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) | [C#](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs)

## <a name="prerequisites"></a>Požadavky

* Účet portálu language understanding (LUIS) – [vytvořte si účet zdarma](https://www.luis.ai)
* Aktuální verze rozhraní [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).


## <a name="setting-up"></a>Nastavení

### <a name="get-your-language-understanding-luis-starter-key"></a>Získejte počáteční klíč luis (Language Understanding" (LUIS)

Získejte [počáteční klíč](../luis-how-to-azure-subscription.md#starter-key) vytvořením vývojového prostředku LUIS. Udržujte klíč a oblast klíče pro další krok.

### <a name="create-an-environment-variable"></a>Vytvoření proměnné prostředí

Pomocí klíče a oblasti pro klíč vytvořte dvě proměnné prostředí pro ověřování:

* `COGNITIVESERVICE_AUTHORING_KEY`- Klíč zdroje pro ověřování vašich požadavků.
* `COGNITIVESERVICE_REGION`- Oblast spojená s vaším klíčem. Například `westus`.

Použijte pokyny pro váš operační systém.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVESERVICE_AUTHORING_KEY <replace-with-your-authoring-key>
setx COGNITIVESERVICE_REGION <replace-with-your-authoring-region>
```

Po přidání proměnné prostředí restartujte okno konzoly.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bashrc`, aby se změny projevily.

#### <a name="macos"></a>[Macos](#tab/unix)

Upravte `.bash_profile`aplikaci a přidejte proměnnou prostředí:

```bash
export COGNITIVESERVICE_AUTHORING_KEY=<replace-with-your-authoring-key>
export COGNITIVESERVICE_REGION=<replace-with-your-authoring-region>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source .bash_profile`, aby se změny projevily.
***

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace C#

Vytvořte novou aplikaci .NET Core ve vašem upřednostňovaném editoru nebo ide.

1. V okně konzoly (například cmd, PowerShell nebo Bash) vytvořte pomocí příkazu `new` `language-understanding-quickstart`dotnet novou konzolovou aplikaci s názvem . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" `Program.cs`s jedním zdrojovým souborem: .

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Změňte adresář do nově vytvořené složky aplikace.

1. Aplikaci můžete sestavit pomocí:

    ```dotnetcli
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


### <a name="install-the-sdk"></a>Instalace sady SDK

V adresáři aplikace nainstalujte klientskou knihovnu pro vytváření jazyka .NET pomocí následujícího příkazu:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.0.0
```

Pokud používáte IDE sady Visual Studio, klientská knihovna je k dispozici jako balíček NuGet ke stažení.


## <a name="object-model"></a>Objektový model

Autorský klient jazyka (LUIS) je objekt [LUISAuthoringClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) který se ověřuje v Azure, který obsahuje váš klíč pro vytváření.

Po vytvoření klienta použijte tento klient pro přístup k funkcím, včetně:

* Aplikace - [vytvořit](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet), [odstranit](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet), [publikovat](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* Příklad y utterances - [add by batch](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet), delete by [ID](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet)
* Funkce - správa [seznamů frází](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet)
* Model - správa [záměrů](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) a entit
* Vzor - správa [vzorů](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet)
* Vlak - [vlak](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet) aplikace a anketa pro [stav školení](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)
* [Verze](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) - správa pomocí klonování, exportu a odstranění


## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí knihovny klientů pro vytváření jazyka (Language Understanding ( LUIS) pro rozhraní .NET:

* [Vytvoření aplikace](#create-a-luis-app)
* [Přidání entit](#create-entities-for-the-app)
* [Přidání záměrů](#create-intent-for-the-app)
* [Přidat ukázkové promluvy](#add-example-utterance-to-intent)
* [Trénování aplikace](#train-the-app)
* [Publikování aplikace](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>Přidání závislostí

V adresáři projektu otevřete *soubor Program.cs* v upřednostňovaném editoru nebo rozhraní IDE. Nahraďte `using` existující kód `using` následujícími směrnicemi:

[!code-csharp[Using statements](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>Ověření klienta

1. Vytvořte proměnnou pro správu vývojového klíče `COGNITIVESERVICES_AUTHORING_KEY`vytaženého z proměnné prostředí s názvem . Pokud jste vytvořili proměnnou prostředí po spuštění aplikace, editor, IDE nebo prostředí běží bude nutné zavřít a znovu načíst pro přístup k proměnné. Metody budou vytvořeny později.

1. Vytvořte proměnné pro uložení oblasti vytváření a koncového bodu. Oblast vývojového klíče závisí na tom, kde je vytvářen. Tři [vývojové oblasti](../luis-reference-regions.md) jsou:

    * Austrálie -`australiaeast`
    * Evropa -`westeurope`
    * USA a další regiony `westus` - (Výchozí)

    [!code-csharp[Authorization to resource key](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Variables)]

1. Vytvořte objekt [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) s klíčem a použijte ho s koncovým bodem k vytvoření objektu [LUISAuthoringClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet)

    [!code-csharp[Create LUIS client object](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Vytvoření aplikace LUIS

1. Vytvořte aplikaci LUIS, která bude obsahovat model zpracování přirozeného jazyka (NLP), který obsahuje záměry, entity a ukázkové projevy.

1. Vytvořte [ApplicationCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet). Název a jazyková verze jsou požadované vlastnosti.

1. Zavolejte metodu [Apps.AddAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) Odpověď je ID aplikace.

    [!code-csharp[Create a LUIS app](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>Vytvoření záměru pro aplikaci
Primární objekt v modelu aplikace LUIS je záměr. Záměr zarovná se seskupí _záměry projevy_uživatele . Uživatel může položit otázku, nebo učinit prohlášení hledá konkrétní _zamýšlené_ odpovědi od robota (nebo jiné klientské aplikace). Příklady záměrů jsou rezervace letu, dotazování se na počasí v cílovém městě a dotazování se na kontaktní informace pro služby zákazníkům.

Vytvořte [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet) s názvem jedinečného záměru a pak předejte ID aplikace, ID verze a ModelCreateObject metodě [Model.AddIntentAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet) Odpověď je ID záměru.

[!code-csharp[Create intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>Vytvoření entit pro aplikaci

Zatímco entity nejsou vyžadovány, nacházejí se ve většině aplikací. Entita extrahuje informace z promluvy uživatele, nezbytné k fullfil záměruživatele. Existuje několik typů [předem sestavených](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet) a vlastních entit, z nichž každý má vlastní modely objektu transformace dat (DTO).  Běžné předem vytvořené entity, které chcete přidat do aplikace, zahrnují [číslo](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinální](../luis-reference-prebuilt-ordinal.md).

Tato metoda **AddEntities** vytvořila jednoduchou `Location` `Class` entitu `Flight` se dvěma rolemi, jednoduchou entitou, složenou entitou a přidá několik předem sestavených entit.

Je důležité vědět, že entity nejsou označeny záměrem. Mohou a obvykle se vztahují na mnoho záměrů. Pouze ukázkové projevy uživatelů jsou označeny pro konkrétní, jeden záměr.

Metody vytváření entit jsou součástí třídy [Model.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) Každý typ entity má svůj vlastní model objektu transformace dat `model` (DTO), který obvykle obsahuje slovo v oboru názvů [Models.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet)

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>Přidat ukázkový projev záměru

Chcete-li určit záměr utterance a extrahovat entity, aplikace potřebuje příklady projevy. Příklady musí cílit na konkrétní, jediný záměr a měly by označit všechny vlastní entity. Předem sestavené entity není nutné označovat.

Přidejte ukázkové projevy vytvořením seznamu objektů [ExampleLabelObject,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) jednoho objektu pro každý příklad utterance. Každý příklad by měl označit všechny entity slovníkem dvojic název/hodnota názvu entity a hodnoty entity. Hodnota entity by měla být přesně tak, jak je uvedena v textu promluvy příkladu.

Volání [Examples.BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_) s ID aplikace, ID verze a seznam příkladů. Volání odpoví seznamem výsledků. Je třeba zkontrolovat výsledek každého příkladu a ujistěte se, že byl úspěšně přidán do modelu.

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringBatchAddUtterancesForIntent)]

**Metody CreateUtterance** a **CreateLabel** jsou metody nástroje, které vám pomohou vytvářet objekty.

## <a name="train-the-app"></a>Trénování aplikace

Po vytvoření modelu aplikace LUIS musí být trénované pro tuto verzi modelu. Trénovaný model lze použít v [kontejneru](../luis-container-howto.md)nebo [publikovat](../luis-how-to-publish-app.md) do pracovních nebo produktových slotů.

Metoda [Train.TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) potřebuje ID aplikace a ID verze.

Velmi malý model, jako je tento rychlý start ukazuje, bude trénovat velmi rychle. Pro aplikace na úrovni produkčního prostředí by školení aplikace měla zahrnovat volání dotazování na metodu [GetStatusAsync,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) která určuje, kdy nebo zda bylo školení úspěšné. Odpověď je seznam [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) objekty se samostatným stavem pro každý objekt. Všechny objekty musí být úspěšné, aby školení bylo považováno za úplné.

[!code-csharp[Train the app's version](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>Publikování aplikace Language Understanding

Publikujte aplikaci LUIS pomocí metody [PublishAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet) To publikuje aktuální trénované verze do zadaného slotu v koncovém bodě. Klientská aplikace používá tento koncový bod k odeslání projevy uživatele pro předpověď záměru a extrakce entity.

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `dotnet run` z adresáře aplikace.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit, můžete odstranit aplikaci LUIS. Odstranění aplikace se provádí metodou [Apps.DeleteAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) Aplikaci můžete také odstranit z [portálu LUIS](https://www.luis.ai).