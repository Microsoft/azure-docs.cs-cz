---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 4bf86c616420bb049e1d7a82ad0e942e6eb7b36f
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369244"
---
Použijte Language Understanding (LUIS) předpovědi klienta pro .NET pro:

* Získat předpovědi podle slotu
* Předpověď podle verze

[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime)  |  [Předpředpověď runtime – balíček (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)  |  [Ukázky jazyka C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>Požadavky

* Účet portálu Language Understanding (LUIS) – [Vytvořte si ho zdarma](https://www.luis.ai) .
* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* ID aplikace LUIS – používá veřejné ID aplikace IoT `df67dcdb-c37d-46af-88e1-8b97951ca1c2` . Dotaz uživatele použitý v kódu pro rychlý Start je specifický pro danou aplikaci.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace v C#

Vytvořte novou aplikaci .NET Core v upřednostňovaném editoru nebo integrovaném vývojovém prostředí (IDE).

1. V okně konzoly (například cmd, PowerShell nebo bash) `new` vytvořte pomocí příkazu dotnet novou konzolovou aplikaci s názvem `language-understanding-quickstart` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: `Program.cs` .

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. Změňte adresář na nově vytvořenou složku aplikace.

1. Aplikaci můžete vytvořit pomocí:

    ```dotnetcli
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

### <a name="install-the-sdk"></a>Instalace sady SDK

V adresáři aplikace nainstalujte klientskou knihovnu modulu runtime pro Language Understanding (LUIS) pro rozhraní .NET pomocí následujícího příkazu:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

Pokud používáte integrované vývojové prostředí (IDE) sady Visual Studio, je knihovna klienta k dispozici jako balíček NuGet ke stažení.

## <a name="object-model"></a>Objektový model

Běhový klient pro modul runtime pro Language Understanding (LUIS) je objekt [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) , který se ověřuje v Azure, který obsahuje váš klíč prostředku.

Po vytvoření klienta použijte tohoto klienta k přístupu k funkcím, včetně:

* Předpověď podle [přípravy nebo výrobního slotu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)
* Předpověď podle [verze](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)


## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny pro Language Understanding (LUIS) předpovědi modulu runtime pro .NET:

* [Předpověď podle slotu](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Přidat závislosti

V adresáři projektu otevřete soubor *program.cs* v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Nahraďte existující `using` kód následujícími `using` direktivami:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Ověření klienta

1. Vytvořte proměnné pro klíč, název prostředku, ID aplikace a slot pro publikování. Nastavte ID aplikace na veřejnou aplikaci IoT:

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. Vytvořte objekt [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) .

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>Získat předpovědi z modulu runtime

Přidejte následující metodu pro vytvoření žádosti pro modul runtime předpovědi.

Uživatel utterance je součástí objektu [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet) .

Metoda **GetSlotPredictionAsync** potřebuje několik parametrů, jako je ID aplikace, název patice, objekt žádosti o předpověď, který požadavek splní. Další možnosti jako verbose, Zobrazit všechny záměry a protokol jsou volitelné.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>Hlavní kód pro předpověď

Pomocí následující metody Main spojíte proměnné a metody dohromady a získáte předpověď.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `dotnet run` příkazu z adresáře aplikace.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s předpovědi hotovi, vyčistěte práci z tohoto rychlého startu tak, že odstraníte soubor program.cs a jeho podadresáře.
