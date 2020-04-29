---
title: 'Rychlý Start: Klientská knihovna pro rozpoznávání formulářů pro .NET'
description: Začínáme s klientskou knihovnou pro rozpoznávání formulářů pro .NET pro výuku, extrakci, analýzu a získání strukturovaného výstupu dat pomocí tohoto rychlého startu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 8d05f5ad07f9b3affba9442d6caa6e63162b813c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81399420"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>Rychlý Start: Klientská knihovna pro rozpoznávání formulářů pro .NET

Začínáme s klientskou knihovnou pro rozpoznávání formulářů pro .NET Nástroj pro rozpoznávání formulářů je výtahová služba, která pomocí technologie strojového učení identifikuje a extrahuje páry klíč/hodnota a tabulková data z dokumentů formuláře. Jejím výstupem jsou strukturovaná data, která zahrnují relace z původního souboru. Pomocí těchto kroků nainstalujete balíček sady SDK a vyzkoušíte ukázkový kód pro základní úlohy.

Použijte klientskou knihovnu pro rozpoznávání formulářů pro .NET pro:

* [Výuka modelu rozpoznávání vlastního formuláře](#train-a-custom-model)
* [Získat seznam extrahovaných klíčů](#get-a-list-of-extracted-keys)
* [Analýza formulářů pomocí vlastního modelu](#analyze-forms-with-a-custom-model)
* [Získat seznam vlastních modelů](#get-a-list-of-custom-models)
* [Odstranění vlastního modelu](#delete-a-custom-model)

[Dokumentace k referenční dokumentaci](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | balíčku[zdrojového kódu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/FormRecognizer) | knihovny[(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).
* Objekt blob Azure Storage, který obsahuje sadu školicích dat. Tipy a možnosti pro sestavení vašich školicích dat najdete v tématu [Vytvoření školicích dat sady pro vlastní model](../build-training-data-set.md) . Pro účely tohoto rychlého startu můžete použít soubory ve složce **výuka** [ukázkové sady dat](https://go.microsoft.com/fwlink/?linkid=2090451).
* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-form-recognizer-azure-resource"></a>Vytvoření prostředku Azure pro rozpoznávání formulářů

[!INCLUDE [create resource](../includes/create-resource.md)]

Po získání klíče a koncového bodu [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíč a koncový bod s názvem `FORM_RECOGNIZER_KEY` a `FORM_RECOGNIZER_ENDPOINT`v uvedeném pořadí.

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace v C#

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem. `formrecognizer-quickstart` Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: _program.cs_. 

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

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_using)]

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

|Název|Popis|
|---|---|
|[FormRecognizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|Tato třída je potřebná pro všechny funkce nástroje pro rozpoznávání formulářů. Vytvoří se jeho instance s informacemi o předplatném a Vy ho použijete k vytvoření instancí jiných tříd.|
|[TrainRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| Tuto třídu použijete k výuce vlastního modelu rozpoznávání formulářů pomocí vlastního školicího vstupního data. |
|[TrainResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| Tato třída poskytuje výsledky vlastní operace vlakového modelu, včetně ID modelu, které pak můžete použít k analýze formulářů. |
|[AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| Tato třída poskytuje výsledky operace analýzy vlastního modelu. Obsahuje seznam instancí **ExtractedPage** . |
|[ExtractedPage](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| Tato třída reprezentuje všechna data extrahovaná z jednoho dokumentu formuláře.|

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny pro rozpoznávání formulářů pro .NET:

* [Ověření klienta](#authenticate-the-client)
* [Výuka modelu rozpoznávání vlastního formuláře](#train-a-custom-model)
* [Získat seznam extrahovaných klíčů](#get-a-list-of-extracted-keys)
* [Analýza formulářů pomocí vlastního modelu](#analyze-forms-with-a-custom-model)
* [Získat seznam vlastních modelů](#get-a-list-of-custom-models)
* [Odstranění vlastního modelu](#delete-a-custom-model)

## <a name="define-variables"></a>Definování proměnných

Před definováním jakýchkoli metod přidejte do horní části třídy **programu** následující definice proměnných. Musíte vyplnit některé proměnné sami. 

* Pokud chcete načíst adresu URL SAS pro vaše školicí údaje, otevřete Průzkumník služby Microsoft Azure Storage, klikněte pravým tlačítkem na svůj kontejner a vyberte **získat sdílený přístupový podpis**. Ujistěte se, že jsou zaškrtnutá oprávnění **číst** a **Zobrazit seznam** , a klikněte na **vytvořit**. Pak zkopírujte hodnotu v části **Adresa URL** . Měla by mít tvar: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Pokud potřebujete ukázkový formulář k analýze, můžete použít jeden ze souborů ve složce **test** [sady vzorových dat](https://go.microsoft.com/fwlink/?linkid=2090451). Tato příručka používá pouze formuláře PDF.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

## <a name="authenticate-the-client"></a>Ověření klienta

Pod `Main` metodou definujte úkol, na který je odkazováno v `Main`. Tady ověříte objekt klienta pomocí proměnných předplatného, které jste definovali výše. Další metody budete definovat později.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

## <a name="train-a-custom-model"></a>Trénování vlastního modelu

Následující metoda používá klientský objekt pro rozpoznávání formulářů k učení nového modelu rozpoznávání v dokumentech uložených v kontejneru objektů BLOB v Azure. Používá pomocnou metodu k zobrazení informací o nově školených modelech (reprezentovaných objektem [ModelResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview) ) a vrací ID modelu.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

Následující pomocná metoda zobrazí informace o modelu rozpoznávání formuláře.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

## <a name="get-a-list-of-extracted-keys"></a>Získat seznam extrahovaných klíčů

Po dokončení školení si vlastní model zachová seznam klíčů, které se extrahují z školicích dokumentů. Očekává, že budoucí dokumenty formuláře budou tyto klíče obsahovat, a extrahuje jejich odpovídající hodnoty v operaci analyzovat. Pomocí následující metody načtěte seznam extrahovaných klíčů a vytiskněte ho do konzoly. To je dobrý způsob, jak ověřit, že proces školení byl účinný.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getkeys)]

## <a name="analyze-forms-with-a-custom-model"></a>Analýza formulářů pomocí vlastního modelu

Tato metoda používá klienta nástroje pro rozpoznávání formulářů a ID modelu k analýze dokumentu formuláře PDF a extrahování dat klíč/hodnota. Používá pomocnou metodu k zobrazení výsledků (reprezentovaných objektem [AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview) ).

> [!NOTE]
> Následující metoda analyzuje formulář PDF. Pro podobné metody, které analyzují formuláře JPEG a PNG, si přečtěte kompletní ukázkový kód na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

Následující pomocná metoda zobrazí informace o operaci analýzy.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

## <a name="get-a-list-of-custom-models"></a>Získat seznam vlastních modelů

Můžete vracet seznam všech vycvičených modelů, které patří k vašemu účtu, a můžete načíst informace o tom, kdy byly vytvořeny. Seznam modelů je reprezentován objektem [ModelsResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview) .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="delete-a-custom-model"></a>Odstranění vlastního modelu

Pokud chcete z účtu odstranit vlastní model, použijte následující metodu:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_deletemodel)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci voláním `dotnet run` příkazu z adresáře aplikace.

```console
dotnet run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Pokud jste navíc prohlédli vlastní model, který chcete z účtu odstranit, spusťte metodu v části [odstranění vlastního modelu](#delete-a-custom-model).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pomocí klientské knihovny rozhraní .NET pro rozpoznávání formulářů mohli naučit vlastní model a analyzovat formuláře. V dalším kroku se dozvíte tipy, jak vytvořit lepší školicí sadu dat a vytvářet přesnější modely.

> [!div class="nextstepaction"]
> [Vytvoření trénovací sady dat](../build-training-data-set.md)

* [Co je služba Rozpoznávání formulářů?](../overview.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).
