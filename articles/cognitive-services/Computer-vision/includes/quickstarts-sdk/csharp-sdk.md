---
title: 'Úvodní příručka: Klientská knihovna počítačového vidění pro rozhraní .NET'
description: V tomto rychlém startu můžete začít s klientskou knihovnou počítačového vidění pro rozhraní .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: ce311d2deaf0eec76ef0dec3ea279a387d14fd22
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81637124"
---
<a name="HOLTop"></a>

[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet) | [Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [knihovny](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0) [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Aktuální verze rozhraní [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-computer-vision-azure-resource"></a>Vytvoření prostředku Azure pro počítačové zpracování obrazu

Azure Cognitive Services jsou reprezentované prostředky Azure, které si předplatíte. Vytvořte prostředek pro počítačové zpracování pomocí [portálu Azure nebo](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu sedmi dnů zdarma. Po registraci bude dostupná na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Zobrazení prostředků na [webu Azure Portal](https://portal.azure.com/).

Po získání klíče z zkušebního předplatného nebo prostředku [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro `COMPUTER_VISION_SUBSCRIPTION_KEY` `COMPUTER_VISION_ENDPOINT`adresu URL klíče a koncového bodu s názvem a , resp.

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace C#

Vytvořte novou aplikaci .NET Core ve vašem upřednostňovaném editoru nebo ide. 

V okně konzoly (například cmd, PowerShell `dotnet new` nebo Bash) vytvořte pomocí `computer-vision-quickstart`příkazu novou konzolovou aplikaci s názvem . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *Program.cs*.

```console
dotnet new console -n computer-vision-quickstart
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

V adresáři projektu otevřete *soubor Program.cs* v upřednostňovaném editoru nebo rozhraní IDE. Přidejte `using` následující direktivy:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_using)]

Ve třídě **Program** aplikace vytvořte proměnné pro koncový bod a klíč Azure vašeho prostředku.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_vars)]

### <a name="install-the-client-library"></a>Instalace klientské knihovny

V adresáři aplikace nainstalujte klientskou knihovnu počítačového vidění pro rozhraní .NET pomocí následujícího příkazu:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 5.0.0
```

Pokud používáte IDE sady Visual Studio, klientská knihovna je k dispozici jako balíček NuGet ke stažení.

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce sady Computer Vision .NET SDK.

|Název|Popis|
|---|---|
| [Klient ComputerVision](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) | Tato třída je potřebná pro všechny funkce počítačového vidění. Můžete vytvořit konkretizovat s informacemi o předplatném a použít jej k většině operací image.|
|[Rozšíření klienta ComputerVisionExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions?view=azure-dotnet)| Tato třída obsahuje další metody pro **ComputerVisionClient**.|
|[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)| Tento výčet definuje různé typy analýzy obrázků, které lze provést ve standardní operaci Analyzovat. V závislosti na vašich potřebách zadáte sadu hodnot VisualFeatureTypes. |

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující úkoly s klientskou knihovnou Počítačové vidění pro rozhraní .NET:

* [Ověření klienta](#authenticate-the-client)
* [Analýza obrázku](#analyze-an-image)
* [Čtení tištěného a ručně psaného textu](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Ověření klienta

> [!NOTE]
> Tento rychlý start předpokládá, že jste [vytvořili proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro `COMPUTER_VISION_SUBSCRIPTION_KEY` klíč `COMPUTER_VISION_ENDPOINT` počítačového vidění a koncový bod s názvem a v uvedeném pořadí.

V nové metodě vytvořte instanci klienta pomocí koncového bodu a klíče. Vytvořte objekt **[ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials?view=azure-dotnet)** s klíčem a použijte ho s koncovým bodem k vytvoření objektu **[ComputerVisionClient.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet)**

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_auth)]

Pravděpodobně budete chtít volat tuto metodu v metodě. `Main`

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_client)]

## <a name="analyze-an-image"></a>Analýza obrázku

Následující kód definuje metodu `AnalyzeImageUrl`, která používá objekt klienta k analýze vzdáleného obrazu a tisku výsledků. Metoda vrátí textový popis, kategorizaci, seznam značek, zjištěné tváře, příznaky obsahu pro dospělé, hlavní barvy a typ obrázku.

Přidejte volání metody `Main` do metody.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyzeinmain)]

### <a name="set-up-test-image"></a>Nastavit testovací bitovou kopii

Ve třídě **Program** uložte odkaz na adresu URL obrázku, který chcete analyzovat.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_url)]

> [!NOTE]
> Můžete také analyzovat místní obrázek. Podívejte se na ukázkový kód na [GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs) pro scénáře zahrnující místní image.

### <a name="specify-visual-features"></a>Určení vizuálních prvků

Definujte novou metodu pro analýzu obrazu. Přidejte níže uvedený kód, který určuje vizuální funkce, které chcete extrahovat v analýze. Úplný seznam naleznete v výčtu **[VisualFeatureTypes.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)**

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_visualfeatures)]

Vložte některý z následujících bloků kódu do metody **AnalyzeImageUrl** k implementaci jejich funkcí. Nezapomeňte přidat uzavírací závorku na konci.

```csharp
}
```

### <a name="analyze"></a>Analýza

Metoda **AnalyzeImageAsync** vrátí objekt **ImageAnalysis,** který obsahuje všechny extrahované informace.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_analyze_call)]

Následující části ukazují, jak podrobně analyzovat tyto informace.

### <a name="get-image-description"></a>Získat popis obrázku

Následující kód získá seznam generovaných titulků pro obrázek. Další podrobnosti [najdete v tématu Popis obrázků.](../../concept-describing-images.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_describe)]

### <a name="get-image-category"></a>Získat kategorii obrázků

Následující kód získá zjištěnou kategorii obrázku. Další [podrobnosti najdete v tématu Kategorizace obrázků.](../../concept-categorizing-images.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>Získat značky obrázků

Následující kód získá sadu zjištěných značek v obraze. Další podrobnosti najdete v [tématu Značky obsahu.](../../concept-tagging-images.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_tags)]

### <a name="detect-objects"></a>Detekce objektů

Následující kód detekuje běžné objekty v obraze a vytiskne je do konzoly. Další [podrobnosti najdete v tématu Detekce objektů.](../../concept-object-detection.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_objects)]

### <a name="detect-brands"></a>Detekce značek

Následující kód detekuje podnikové značky a loga v obrázku a vytiskne je do konzoly. Další [podrobnosti najdete v tématu Detekce značek.](../../concept-brand-detection.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_brands)]

### <a name="detect-faces"></a>Rozpoznávání tváří

Následující kód vrátí zjištěné plochy v obraze s jejich obdélníkovými souřadnicemi a vybranými atributy plochy. Další [podrobnosti najdete v tématu Detekce obličejů.](../../concept-detecting-faces.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Detekujte obsah pro dospělé, pikantní nebo krvavý

Následující kód vytiskne zjištěnou přítomnost obsahu pouze pro dospělé v obrázku. Další podrobnosti najdete [v tématu Dospělý, pikantní, krvavý obsah.](../../concept-detecting-adult-content.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Získat barevné schéma obrázku

Následující kód vytiskne zjištěné barevné atributy v obraze, například dominantní barvy a barvy zvýraznění. Další podrobnosti najdete [v části Barevná schémata.](../../concept-detecting-color-schemes.md)

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>Získání obsahu specifického pro doménu

Počítačové vidění může používat specializované modely k další analýze obrázků. Další podrobnosti najdete [v tématu Obsah specifický pro doménu.](../../concept-detecting-domain-content.md) 

Následující kód analyzuje data o zjištěných celebrit v obraze.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_celebs)]

Následující kód analyzuje data o zjištěných orientačních bodů v obraze.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>Získání typu obrázku

Následující kód vytiskne informace&mdash;o typu obrázku, ať už se jedná o klipart nebo kresbu čar.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>Čtení tištěného a ručně psaného textu

Počítačové vidění může číst viditelný text v obraze a převést jej na datový proud znaků. Kód v této části definuje `ExtractTextUrl`metodu , která používá objekt klienta k detekci a extrahování tištěného nebo ručně psaného textu v obraze.

Přidejte volání metody `Main` do metody.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttextinmain)]

### <a name="set-up-test-image"></a>Nastavit testovací bitovou kopii

Ve třídě **Program** uložte odkaz na adresu URL obrázku, ze kterého chcete extrahovat text.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extracttext_url)]

> [!NOTE]
> Můžete také extrahovat text z místního obrazu. Podívejte se na ukázkový kód na [GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs) pro scénáře zahrnující místní image.

### <a name="call-the-read-api"></a>Volání rozhraní READ API

Definujte novou metodu čtení textu. Přidejte níže uvedený kód, který volá metodu **BatchReadFileAsync** pro daný obrázek. To vrátí ID operace a spustí asynchronní proces číst obsah obrazu.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_call)]

### <a name="get-read-results"></a>Získat výsledky čtení

Dále získat ID operace vrácené z **BatchReadFileAsync** volání a použít jej k dotazování služby pro výsledky operace. Následující kód kontroluje operaci v intervalech jedné sekundy, dokud nejsou vráceny výsledky. Potom vytiskne extrahovaná textová data do konzoly.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_response)]

### <a name="display-read-results"></a>Zobrazit výsledky čtení

Přidejte následující kód, chcete-li analyzovat a zobrazit načtená textová data a dokončit definici metody.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ComputerVision/Program.cs?name=snippet_extract_display)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci z `dotnet run` adresáře aplikace pomocí příkazu.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Reference rozhraní API pro počítačové zpracování obrazu (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)

* [Co je počítačové zpracování obrazu?](../../Home.md)
* Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ComputerVision/Program.cs).
