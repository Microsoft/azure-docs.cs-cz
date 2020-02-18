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
ms.openlocfilehash: 3bb0b9fb8b268c3e036ffb365eb8d5b80a241269
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2020
ms.locfileid: "77372020"
---
Použijte Language Understanding (LUIS) předpovědi klienta pro .NET pro:

* Získat předpovědi podle slotu

[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [předpovědi za běhu balíčku (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [ C# ukázky](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>Požadavky

* Účet portálu Language Understanding (LUIS) – [Vytvořte si ho zdarma](https://www.luis.ai) .
* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

Hledáte další dokumentaci?

 * [Referenční dokumentace k sadě SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)

## <a name="setting-up"></a>Nastavení

### <a name="create-an-environment-variable"></a>Vytvoření proměnné prostředí

Pomocí klíče a názvu prostředku vytvořte dvě proměnné prostředí pro ověřování:

* `LUIS_PREDICTION_KEY` – klíč prostředku pro ověřování vašich požadavků.
* `LUIS_ENDPOINT_NAME` – název prostředku, který je přidružený k vašemu klíči.

Použijte pokyny pro váš operační systém.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_PREDICTION_KEY <replace-with-your-resource-key>
setx LUIS_ENDPOINT_NAME <replace-with-your-resource-name>
```

Po přidání proměnné prostředí restartujte okno konzoly.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bashrc`, aby se změny projevily.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Upravte `.bash_profile`a přidejte proměnnou prostředí:

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source .bash_profile`, aby se změny projevily.

---

### <a name="create-a-new-c-application"></a>Vytvoření nové C# aplikace

Vytvořte novou aplikaci .NET Core v upřednostňovaném editoru nebo integrovaném vývojovém prostředí (IDE).

1. V okně konzoly (například cmd, PowerShell nebo bash) použijte příkaz dotnet `new` k vytvoření nové konzolové aplikace s názvem `language-understanding-quickstart`. Tento příkaz vytvoří jednoduchý projekt "Hello World" C# s jedním zdrojovým souborem: `Program.cs`.

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

V adresáři projektu otevřete soubor *program.cs* v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Nahraďte existující kód `using` následujícími direktivami `using`:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Ověření klienta

1. Vytvořte proměnné pro klíč, název a ID aplikace:

    Proměnné pro správu vašeho klíč předpovědi načtené z proměnné prostředí s názvem `LUIS_PREDICTION_KEY`. Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, bude nutné editor, rozhraní IDE nebo prostředí, které je spuštěno, zavřít a znovu načíst pro přístup k proměnné. Metody budou vytvořeny později.

    Vytvořte proměnnou pro uložení názvu prostředku `LUIS_ENDPOINT_NAME`.

    Vytvořte proměnnou pro ID aplikace jako proměnnou prostředí s názvem `LUIS_APP_ID`. Nastavte proměnnou prostředí na veřejnou aplikaci IoT:

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

Spusťte aplikaci pomocí příkazu `dotnet run` z adresáře aplikace.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete s předpovědi hotovi, vyčistěte práci z tohoto rychlého startu tak, že odstraníte soubor program.cs a jeho podadresáře.
