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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77372020"
---
Pomocí knihovny klienta předpověď jazykové ho jazyka (LUIS) pro rozhraní .NET:

* Získejte předpověď podle slotu

[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [Ukázky knihovního kódu původu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) | [Predikce runtime balíčku (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) | [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/LanguageUnderstanding/predict-with-sdk-3x)

## <a name="prerequisites"></a>Požadavky

* Účet portálu language understanding (LUIS) – [vytvořte si účet zdarma](https://www.luis.ai)
* Aktuální verze rozhraní [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

Hledáte další dokumentaci?

 * [Referenční dokumentace sady SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)

## <a name="setting-up"></a>Nastavení

### <a name="create-an-environment-variable"></a>Vytvoření proměnné prostředí

Pomocí klíče a názvu prostředku vytvořte dvě proměnné prostředí pro ověřování:

* `LUIS_PREDICTION_KEY`- Klíč zdroje pro ověřování vašich požadavků.
* `LUIS_ENDPOINT_NAME`- Název prostředku přidružený k vašemu klíči.

Použijte pokyny pro váš operační systém.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_PREDICTION_KEY <replace-with-your-resource-key>
setx LUIS_ENDPOINT_NAME <replace-with-your-resource-name>
```

Po přidání proměnné prostředí restartujte okno konzoly.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bashrc`, aby se změny projevily.

#### <a name="macos"></a>[Macos](#tab/unix)

Upravte `.bash_profile`aplikaci a přidejte proměnnou prostředí:

```bash
export LUIS_PREDICTION_KEY=<replace-with-your-resource-key>
export LUIS_ENDPOINT_NAME=<replace-with-your-resource-name>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source .bash_profile`, aby se změny projevily.

---

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

V adresáři aplikace nainstalujte runtime klientskou knihovnu prognózy jazyka Language Understanding (LUIS) pro rozhraní .NET pomocí následujícího příkazu:

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

Pokud používáte IDE sady Visual Studio, klientská knihovna je k dispozici jako balíček NuGet ke stažení.

## <a name="object-model"></a>Objektový model

Klient runtime předpovědi jazyka (LUIS) je objekt [LUISRuntimeClient,](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) který se ověřuje v Azure, který obsahuje klíč prostředků.

Po vytvoření klienta použijte tento klient pro přístup k funkcím, včetně:

* Predikce [podle pracovní hospůdky nebo slotu produktu](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)
* Predikce podle [verze](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)


## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce s knihovnou runtime predikce jazyka (LUIS) pro rozhraní .NET:

* [Predikce podle slotu](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Přidání závislostí

V adresáři projektu otevřete *soubor Program.cs* v upřednostňovaném editoru nebo rozhraní IDE. Nahraďte `using` existující kód `using` následujícími směrnicemi:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Ověření klienta

1. Vytvořte proměnné pro klíč, název a ID aplikace:

    Proměnné pro správu klíče předpověď vytáhl z `LUIS_PREDICTION_KEY`proměnné prostředí s názvem . Pokud jste vytvořili proměnnou prostředí po spuštění aplikace, editor, IDE nebo prostředí běží bude nutné zavřít a znovu načíst pro přístup k proměnné. Metody budou vytvořeny později.

    Vytvořte proměnnou, která `LUIS_ENDPOINT_NAME`bude obsahovat název prostředku .

    Vytvořte proměnnou pro ID aplikace `LUIS_APP_ID`jako proměnnou prostředí s názvem . Nastavte proměnnou prostředí na veřejnou aplikaci IoT:

    **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`**

    [!code-csharp[Create variables](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_variables)]

1. Vytvořte objekt [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) s klíčem a použijte ho s koncovým bodem k vytvoření objektu [LUISRuntimeClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet)

    [!code-csharp[Create LUIS client object](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_create_client)]

## <a name="get-prediction-from-runtime"></a>Získat předpověď z běhu

Přidejte následující metodu k vytvoření požadavku do běhu předpověď.

Projev uživatele je součástí [Objektu PredictionRequest.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet)

Metoda **GetSlotPredictionAsync** potřebuje několik parametrů, jako je Například ID aplikace, název patice, objekt požadavku předpověď ke splnění požadavku. Ostatní možnosti, jako je například podrobné, zobrazit všechny záměry a protokol jsou volitelné.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_maintask)]

## <a name="main-code-for-the-prediction"></a>Hlavní kód pro předpověď

Použijte následující hlavní metodu k provázání proměnných a metod dohromady, abyste získali předpověď.

[!code-csharp[Create method to get prediction runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/predict-with-sdk-3x/Program.cs?name=snippet_main)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí příkazu `dotnet run` z adresáře aplikace.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení s vaše předpovědi, vyčistit práci z tohoto rychlého startu odstraněním program.cs soubor a jeho podadresáře.
