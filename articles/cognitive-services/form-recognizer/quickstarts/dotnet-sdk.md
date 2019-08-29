---
title: 'Rychlý start: Klientská knihovna pro rozpoznávání formulářů pro .NET | Microsoft Docs'
description: Začínáme s klientskou knihovnou pro rozpoznávání formulářů pro .NET
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/12/2019
ms.author: pafarley
ms.openlocfilehash: ada570196c916a8101e8e968d284a3b280199cf3
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142809"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>Rychlý start: Klientská knihovna pro rozpoznávání formulářů pro .NET

Začínáme s klientskou knihovnou pro rozpoznávání formulářů pro .NET Nástroj pro rozpoznávání formulářů je výtahová služba, která pomocí technologie strojového učení identifikuje a extrahuje páry klíč/hodnota a tabulková data z dokumentů formuláře. Jejím výstupem jsou strukturovaná data, která zahrnují relace z původního souboru. Pomocí těchto kroků nainstalujete balíček sady SDK a vyzkoušíte ukázkový kód pro základní úlohy.

Použijte klientskou knihovnu pro rozpoznávání formulářů pro .NET pro:

* Výuka modelu rozpoznávání vlastního formuláře
* Analýza formulářů pomocí vlastního modelu
* Získat seznam vlastních modelů

[Dokumentace k referenční dokumentaci](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | balíčku[zdrojového kódu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.FormRecognizer) | knihovny[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).
* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Objekt blob Azure Storage, který obsahuje sadu školicích dat. Tipy a možnosti pro sestavení vašich školicích dat najdete v tématu [Vytvoření školicích dat sady pro vlastní model](../build-training-data-set.md) . 

## <a name="setting-up"></a>Nastavení

### <a name="create-a-form-recognizer-azure-resource"></a>Vytvoření prostředku Azure pro rozpoznávání formulářů

[!INCLUDE [create resource](../includes/create-resource.md)]

Po získání klíče ze zkušebního předplatného nebo prostředku vytvořte pro tento klíč [proměnnou prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) s názvem `FORM_RECOGNIZER_KEY`.

### <a name="create-a-new-c-application"></a>Vytvoření nové C# aplikace

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem. `formrecognizer-quickstart` Tento příkaz vytvoří jednoduchý projekt "Hello World" C# s jedním zdrojovým souborem: _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Změňte adresář na nově vytvořenou složku aplikace. Pak sestavte aplikaci pomocí:

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

V adresáři projektu otevřete soubor _program.cs_ v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Přidejte následující příkazy `using`:

```csharp
using Microsoft.Azure.CognitiveServices.FormRecognizer;
using Microsoft.Azure.CognitiveServices.FormRecognizer.Models;

using System;
using System.IO;
using System.Threading.Tasks;
```

Pak do metody **Main** aplikace přidejte následující kód. Tuto asynchronní úlohu budete definovat později.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>Instalace klientské knihovny

V adresáři aplikace nainstalujte klientskou knihovnu pro rozpoznávání formulářů pro rozhraní .NET pomocí následujícího příkazu:

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

Pokud používáte integrované vývojové prostředí (IDE) sady Visual Studio, je knihovna klienta k dispozici jako balíček NuGet ke stažení.

## <a name="object-model"></a>Objektový model

Následující třídy zpracovávají hlavní funkce sady SDK pro rozpoznávání formulářů.

|Name|Popis|
|---|---|
|[FormRecognizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|Tato třída je potřebná pro všechny funkce nástroje pro rozpoznávání formulářů. Vytvoří se jeho instance s informacemi o předplatném a Vy ho použijete k vytvoření instancí jiných tříd.|
|[TrainRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| Tuto třídu použijete k výuce vlastního modelu rozpoznávání formulářů pomocí vlastního školicího vstupního data. |
|[TrainResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| Tato třída poskytuje výsledky vlastní operace vlakového modelu, včetně ID modelu, které pak můžete použít k analýze formulářů. |
|[AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| Tato třída poskytuje výsledky operace analýzy vlastního modelu. Obsahuje seznam instancí **ExtractedPage** . |
|[ExtractedPage](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| Tato třída reprezentuje všechna data extrahovaná z jednoho dokumentu formuláře.|

## <a name="code-examples"></a>Příklady kódu

<!--
    Include code snippets and short descriptions for each task you list in the the bulleted list. Briefly explain each operation, but include enough clarity to explain complex or otherwise tricky operations.

    Include links to the service's reference content when introducing a class for the first time
-->

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny pro rozpoznávání formulářů pro .NET:

* [Ověření klienta](#authenticate-the-client)
* [Výuka modelu rozpoznávání vlastního formuláře](#train-a-custom-model)
* [Analýza formulářů pomocí vlastního modelu](#analyze-forms-with-a-custom-model)
* [Získat seznam vlastních modelů](#get-a-list-of-custom-models)

### <a name="define-variables"></a>Definování proměnných

Před definováním jakýchkoli metod přidejte do horní části třídy **programu** následující definice proměnných. Musíte vyplnit některé proměnné sami. 

* Hodnotu koncového bodu vaší služby najdete v části **Přehled** v Azure Portal. 
* Pokud chcete načíst adresu URL SAS pro vaše školicí údaje, otevřete Průzkumník služby Microsoft Azure Storage, klikněte pravým tlačítkem na svůj kontejner a vyberte **získat sdílený přístupový podpis**. Ujistěte se, že jsou zaškrtnutá oprávnění **číst** a **Zobrazit seznam** , a klikněte na **vytvořit**. Pak zkopírujte hodnotu v části **Adresa URL** . Měla by mít tvar: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

### <a name="authenticate-the-client"></a>Ověření klienta

Pod metodou definujte úkol, na který je odkazováno v `Main`. `Main` Tady ověříte objekt klienta pomocí proměnných předplatného, které jste definovali výše. Další metody budete definovat později.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

### <a name="train-a-custom-model"></a>Trénování vlastního modelu

Následující metoda používá klientský objekt pro rozpoznávání formulářů k učení nového modelu rozpoznávání v dokumentech uložených v kontejneru objektů BLOB v Azure. Používá pomocnou metodu k zobrazení informací o nově školených modelech (reprezentovaných objektem [ModelResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview) ) a vrací ID modelu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

Následující pomocná metoda zobrazí informace o modelu rozpoznávání formuláře.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

### <a name="analyze-forms-with-a-custom-model"></a>Analýza formulářů pomocí vlastního modelu

Tato metoda používá klienta nástroje pro rozpoznávání formulářů a ID modelu k analýze dokumentu formuláře PDF a extrahování dat klíč/hodnota. Používá pomocnou metodu k zobrazení výsledků (reprezentovaných objektem [AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview) ).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

Následující pomocná metoda zobrazí informace o operaci analýzy.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

### <a name="get-a-list-of-custom-models"></a>Získat seznam vlastních modelů

Můžete vracet seznam všech vycvičených modelů, které patří k vašemu účtu, a můžete načíst informace o tom, kdy byly vytvořeny. Seznam modelů je reprezentován objektem [ModelsResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci voláním `dotnet run` příkazu z adresáře aplikace.

```console
dotnet run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Pokud jste navíc prohlédli vlastní model, který chcete z účtu odstranit, použijte následující metodu:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_deletemodel)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste pomocí klientské knihovny rozhraní .NET pro rozpoznávání formulářů mohli naučit vlastní model a analyzovat formuláře. V dalším kroku se dozvíte tipy, jak vytvořit lepší školicí sadu dat a vytvářet přesnější modely.

> [!div class="nextstepaction"]
>[Sestavení sady školicích dat](../build-training-data-set.md)

* [Co je nástroj pro rozpoznávání formulářů?](../overview.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer)
