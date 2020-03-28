---
title: 'Úvodní příručka: Klientská knihovna nástroje Pro rozpoznávání formulářů pro rozhraní .NET'
description: Začněte s klientskou knihovnou nástroje rozpoznávání formulářů pro rozhraní .NET pro trénování, extrahování, analýzu a získání výstupu strukturovaných dat pomocí tohoto rychlého startu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: b9db9daf06b59e1a26a9b03a93aff63984841862
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118361"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>Úvodní příručka: Klientská knihovna nástroje Pro rozpoznávání formulářů pro rozhraní .NET

Začínáme s klientskou knihovnou nástroje Pro rozpoznávání formulářů pro rozhraní .NET. Nástroj Pro rozpoznávání formulářů je služba Cognitive Service, která využívá technologii strojového učení k identifikaci a extrahování párů klíčů a hodnot a dat tabulek z dokumentů formuláře. Jejím výstupem jsou strukturovaná data, která zahrnují relace z původního souboru. Následujícím postupem nainstalujte balíček sady SDK a vyzkoušejte ukázkový kód pro základní úkoly.

Pomocí klientské knihovny nástroje Pro rozpoznávání formulářů pro rozhraní .NET můžete:

* [Trénování vlastního modelu nástroje pro rozpoznávání formulářů](#train-a-custom-model)
* [Získání seznamu extrahovaných klíčů](#get-a-list-of-extracted-keys)
* [Analýza formulářů pomocí vlastního modelu](#analyze-forms-with-a-custom-model)
* [Získání seznamu vlastních modelů](#get-a-list-of-custom-models)
* [Odstranění vlastního modelu](#delete-a-custom-model)

[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | [Knihovna zdrojový kód](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/FormRecognizer) | [Balíček (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/).
* Objekt blob úložiště Azure, který obsahuje sadu trénovacích dat. V [tématu Vytvoření trénovací datové sady pro vlastní model,](../build-training-data-set.md) kde najdete tipy a možnosti pro sestavení trénovacích dat. Pro tento rychlý start můžete použít soubory ve složce **Vlak** [ukázkové datové sady](https://go.microsoft.com/fwlink/?linkid=2090451).
* Aktuální verze rozhraní [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-form-recognizer-azure-resource"></a>Vytvoření prostředku Azure pro rozpoznávání formulářů

[!INCLUDE [create resource](../includes/create-resource.md)]

Po získání klíče z zkušebního předplatného nebo prostředku [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) `FORM_RECOGNIZER_KEY` pro `FORM_RECOGNIZER_ENDPOINT`klíč a koncový bod s názvem a , resp.

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace C#

V okně konzoly (například cmd, PowerShell `dotnet new` nebo Bash) vytvořte pomocí `formrecognizer-quickstart`příkazu novou konzolovou aplikaci s názvem . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Změňte adresář do nově vytvořené složky aplikace. Potom vytvořte aplikaci pomocí:

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

V adresáři projektu otevřete _soubor Program.cs_ v upřednostňovaném editoru nebo rozhraní IDE. Přidejte následující příkazy `using`:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_using)]

Potom přidejte následující kód v **hlavní** metodě aplikace. Tuto asynchronní úlohu definujete později.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>Instalace klientské knihovny

V adresáři aplikace nainstalujte klientskou knihovnu nástroje pro rozpoznávání formulářů pro rozhraní .NET pomocí následujícího příkazu:

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

Pokud používáte IDE sady Visual Studio, klientská knihovna je k dispozici jako balíček NuGet ke stažení.

## <a name="object-model"></a>Objektový model

Následující třídy zpracovávají hlavní funkce sady SDK pro rozpoznávání formulářů.

|Name (Název)|Popis|
|---|---|
|[Klient FormRecognizer](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|Tato třída je potřebná pro všechny funkce nástroje pro rozpoznávání formulářů. Můžete vytvořit konkretizovat s informacemi o předplatném a použít k vytvoření instance jiných tříd.|
|[Požadavek na vlak](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| Tato třída slouží k trénování vlastního modelu rozpoznávání formulářů pomocí vlastních vstupních dat školení. |
|[Výsledek vlaku](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| Tato třída poskytuje výsledky vlastní model operace Train, včetně ID modelu, které pak můžete použít k analýze formulářů. |
|[Analyzovatvýsledek](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| Tato třída poskytuje výsledky operace Analyze vlastního modelu. Obsahuje seznam instancí **ExtractedPage.** |
|[Extrahovaná stránka](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| Tato třída představuje všechna data extrahovaná z jednoho dokumentu formuláře.|

## <a name="code-examples"></a>Příklady kódu

<!--
    Include code snippets and short descriptions for each task you list in the the bulleted list. Briefly explain each operation, but include enough clarity to explain complex or otherwise tricky operations.

    Include links to the service's reference content when introducing a class for the first time
-->

Tyto fragmenty kódu ukazují, jak provést následující úkoly s klientskou knihovnou nástroje pro rozpoznávání formulářů pro rozhraní .NET:

* [Ověření klienta](#authenticate-the-client)
* [Trénování vlastního modelu nástroje pro rozpoznávání formulářů](#train-a-custom-model)
* [Získání seznamu extrahovaných klíčů](#get-a-list-of-extracted-keys)
* [Analýza formulářů pomocí vlastního modelu](#analyze-forms-with-a-custom-model)
* [Získání seznamu vlastních modelů](#get-a-list-of-custom-models)
* [Odstranění vlastního modelu](#delete-a-custom-model)

## <a name="define-variables"></a>Definování proměnných

Před definováním metod přidejte následující definice proměnných na začátek třídy **Program.** Některé proměnné budete muset vyplnit sami. 

* Pokud chcete načíst adresu URL SAS pro trénovací data, otevřete Průzkumníka úložiště Microsoft Azure, klikněte pravým tlačítkem myši na kontejner a vyberte **Získat sdílený přístupový podpis**. Zkontrolujte, zda jsou zaškrtnuta oprávnění **Číst** a **seznam,** a klepněte na **tlačítko Vytvořit**. Pak zkopírujte hodnotu v části **ADRESA URL.** Měl by mít `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`formu: .
* Pokud potřebujete ukázkový formulář k analýze, můžete použít jeden ze souborů ve složce **Test** [ukázkové datové sady](https://go.microsoft.com/fwlink/?linkid=2090451). Tato příručka používá pouze formuláře PDF.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

## <a name="authenticate-the-client"></a>Ověření klienta

Pod `Main` metodou definujte úkol, na `Main`který odkazuje v aplikaci . Zde ověříte objekt klienta pomocí proměnných odběru, které jste definovali výše. Další metody definujete později.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

## <a name="train-a-custom-model"></a>Trénování vlastního modelu

Následující metoda používá objekt klienta nástroje pro rozpoznávání formulářů k trénování nového modelu rozpoznávání v dokumentech uložených v kontejneru objektů blob Azure. Používá pomocnou metodu k zobrazení informací o nově trénovaný model (reprezentované [ModelResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview) objektu) a vrátí ID modelu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

Následující pomocná metoda zobrazuje informace o modelu nástroje pro rozpoznávání formulářů.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

## <a name="get-a-list-of-extracted-keys"></a>Získání seznamu extrahovaných klíčů

Po dokončení školení, vlastní model bude uchovávat seznam klíčů, které má extrahované z dokumentů školení. Očekává, že budoucí formuláře dokumenty obsahují tyto klíče a extrahuje jejich odpovídající hodnoty v operaci Analyzovat. Pomocí následující metody načtěte seznam extrahovaných klíčů a vytiskněte jej do konzoly. To je dobrý způsob, jak ověřit, že proces školení byl účinný.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getkeys)]

## <a name="analyze-forms-with-a-custom-model"></a>Analýza formulářů pomocí vlastního modelu

Tato metoda používá klienta nástroje pro rozpoznávání formulářů a ID modelu k analýze dokumentu formuláře PDF a extrahování dat klíč/hodnota. Používá pomocnou metodu k zobrazení výsledků (reprezentované [Objektem AnalyzeResult).](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)

> [!NOTE]
> Následující metoda analyzuje formulář PDF. Podobné metody, které analyzují formuláře JPEG a PNG, naleznete v úplném ukázkovém kódu na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

Následující pomocná metoda zobrazuje informace o operaci Analyzovat.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

## <a name="get-a-list-of-custom-models"></a>Získání seznamu vlastních modelů

Můžete vrátit seznam všech trénovaných modelů, které patří do vašeho účtu, a můžete načíst informace o tom, kdy byly vytvořeny. Seznam modelů je reprezentován [Objektem ModelsResult.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="delete-a-custom-model"></a>Odstranění vlastního modelu

Pokud chcete odstranit vlastní model ze svého účtu, použijte následující metodu:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_deletemodel)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci `dotnet run` voláním příkazu z adresáře aplikace.

```console
dotnet run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Navíc pokud jste vycvičili vlastní model, který chcete odstranit ze svého účtu, spusťte metodu v [odstranit vlastní model](#delete-a-custom-model).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili knihovnu klienta Nástroje pro rozpoznávání formulářů .NET k trénování vlastního modelu a analýze formulářů. Dále se naučte tipy k vytvoření lepší sady dat školení a vytváření přesnějších modelů.

> [!div class="nextstepaction"]
>[Vytvoření trénovací sady dat](../build-training-data-set.md)

* [Co je služba Rozpoznávání formulářů?](../overview.md)
* Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).
