---
title: 'Ukázka: Prozkoumejte aplikace zpracování bitové kopie vC#'
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Prozkoumejte základní aplikaci Windows, který používá rozhraní API pro počítačové zpracování obrazu ve službě Microsoft Cognitive Services. Provádějte optické rozpoznávání znaků (OCR), vytvářejte miniatury a pracujte s vizuálními funkcemi obrázku.
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a23e45cb1e75305f41c81077c90282352327b72d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310937"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>Ukázka: Prozkoumejte zpracování aplikace pomocí bitové kopieC#

Prozkoumejte základní aplikace Windows, která používá pro počítačové zpracování obrazu k provedení optické rozpoznávání znaků (OCR), vytváření miniatur výtisky oříznout smart plus detekovat, kategorií, značky a popisují funkce visual, včetně tváří, v obraze. Následující příklad umožňuje odeslat adresu URL obrázku nebo místně uložený soubor. V tomto příkladu opensourcových lze použít jako šablonu pro vytvoření vlastní aplikace pro Windows pomocí rozhraní API pro počítačové zpracování obrazu a Windows Presentation Foundation (WPF), součástí rozhraní .NET Framework.

> [!div class="checklist"]
> * Získání ukázkové aplikace z Githubu
> * Otevření a sestavení ukázkové aplikace v sadě Visual Studio
> * Spuštění ukázkové aplikace a pracovat s ním provádět různé scénáře
> * Prozkoumejte různé scénáře, které jsou součástí ukázkové aplikace

## <a name="prerequisites"></a>Požadavky

Prozkoumáte ukázkovou aplikaci, ujistěte se, že splnění následujících požadavků:

* Musíte mít [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) nebo novější.
* Musíte mít klíč předplatného pro počítačové zpracování obrazu. Abyste získali klíč předplatného, přejděte k tématu [Jak získat klíče předplatného](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="get-the-sample-app"></a>Získání ukázkové aplikace

Ukázková aplikace pro počítačové zpracování obrazu je k dispozici na Githubu z `Microsoft/Cognitive-Vision-Windows` úložiště. Toto úložiště obsahuje také `Microsoft/Cognitive-Common-Windows` úložiště jako dílčí modul Git. Můžete rekurzivně klonovat toto úložiště, včetně dílčího modulu, buď pomocí `git clone --recurse-submodules` příkazu z příkazového řádku nebo pomocí GitHub Desktop.

Například pro rekurzivní klonované úložiště ukázkové aplikace pro počítačové zpracování obrazu z příkazového řádku, spusťte následující příkaz:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Nestahovat toto úložiště jako ZIP. Git neobsahuje dílčí moduly při stahování úložiště jako ZIP.

### <a name="get-optional-sample-images"></a>Načítat volitelné vzorové obrázky

Volitelně můžete použít ukázkové obrázky součástí [pro rozpoznávání tváře](../../Face/Overview.md) ukázkovou aplikaci, která je k dispozici na Githubu z `Microsoft/Cognitive-Face-Windows` úložiště. Tato ukázková aplikace obsahuje složku, `/Data`, který obsahuje více bitových kopií lidí. Je možné rekurzivní klonované toto úložiště, metodami popsanými pro ukázkovou aplikaci pro počítačové zpracování obrazu.

Například pro rekurzivní klonované úložiště ukázkové aplikace pro rozpoznávání tváře z příkazového řádku, spusťte následující příkaz:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Otevření a sestavení ukázkové aplikace v sadě Visual Studio

Ukázkovou aplikaci musí nejdřív sestavení tak, aby Visual Studio můžete vyřešit závislosti, než můžete spustit nebo prozkoumat ukázkové aplikace. K otevření a sestavení ukázkové aplikace, proveďte následující kroky:

1. Otevřete soubor řešení sady Visual Studio `/Sample-WPF/VisionAPI-WPF-Samples.sln`, v sadě Visual Studio.
1. Ujistěte se, že řešení sady Visual Studio obsahuje dva projekty:  

   * SampleUserControlLibrary
   * Ukázky WPF VisionAPI  

   Pokud projekt SampleUserControlLibrary není k dispozici, ověřte, že jste naklonovali rekurzivně `Microsoft/Cognitive-Vision-Windows` úložiště.
1. V sadě Visual Studio, stiskněte kombinaci kláves Ctrl + Shift + B nebo zvolte **sestavení** z pásu karet a klikněte na tlačítko **sestavit řešení** k sestavení řešení.

## <a name="run-and-interact-with-the-sample-app"></a>Spuštění a interakci s ukázkovou aplikací

Můžete spustit ukázkovou aplikaci, pokud chcete zobrazit, jak komunikuje se s vámi a s klientskou knihovnou pro počítačové zpracování obrazu při provádění různých úloh, jako je například generování miniatur nebo označování imagí. Chcete-li spustit a interakci s ukázkovou aplikací, proveďte následující kroky:

1. Po dokončení sestavení je buď stiskněte **F5** nebo zvolte **ladění** z pásu karet a klikněte na tlačítko **spustit ladění** na spuštění ukázkové aplikace.
1. Až se zobrazí ukázkovou aplikaci, zvolte **Správa klíčů předplatného** v navigačním podokně zobrazte stránku pro správu klíč předplatného.
   ![Stránka Správa klíčů předplatného](../Images/Vision_UI_Subscription.PNG)  
1. Zadejte klíč předplatného. v **klíč předplatného**.
1. Zadejte adresu URL koncového bodu vynechání `/vision/v1.0`, prostředku pro počítačové zpracování obrazu pro váš klíč předplatného v **koncový bod**.  
   Například pokud používáte klíč předplatného z bezplatné zkušební verze pro počítačové zpracování obrazu, zadejte následující adresu URL koncového bodu pro oblast západní střed USA Azure: `https://westcentralus.api.cognitive.microsoft.com`
1. Pokud nechcete zadat klíč předplatného a koncový bod adresy URL při příštím spuštění ukázkové aplikace, zvolte **uložit nastavení** uložit předplatné key a koncového bodu adresy URL k vašemu počítači. Pokud chcete odstranit vaše předplatné dříve uložený klíč a koncový bod adresy URL, zvolte **odstranit nastavení**.

   > [!NOTE]
   > Ukázková aplikace používá izolovaného úložiště, a `System.IO.IsolatedStorage`, ukládat vaše předplatné key a koncového bodu adresy URL.

1. V části **výběru scénáře** v navigačním podokně vyberte jednu z scénáře aktuálně součástí ukázkové aplikace:  

   | Scénář | Popis |
   |----------|-------------|
   |Analýza obrázku | Používá [analyzovat Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) operace analyzovat místní nebo vzdálené image. Můžete vybrat vizuální funkce a jazyk pro analýzy a vidět na obrázku a výsledky.  |
   |Analýza Image pomocí doménového modelu | Používá [seznamu doménově specifické modely](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) operaci do seznamu doménových modelů, které můžete vybrat, a [rozpoznat domény konkrétní obsahu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) operace analyzovat místní nebo vzdálené image pomocí model vybrané domény. Můžete také zvolit jazyk pro analýzu. |
   |Popis bitové kopie | Používá [popisují Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) operaci vytvoření čitelný popis bitové kopie místního nebo vzdáleného. Můžete také zvolit jazyk pro popis. |
   |Generovat značky | Používá [značka obrázku](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) operace k označení vizuální funkce obrázků místního nebo vzdáleného. Můžete také zvolit jazyk používaný pro značky. |
   |Rozpoznání textu (OCR) | Používá [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) operace rozpoznat a extrahovat vytisknout text z obrázku. Můžete zvolte jazyk, který chcete použít, nebo nechat pro počítačové zpracování obrazu automatické rozpoznání jazyka. |
   |Rozpoznávání textu V2 (v angličtině) | Používá [rozpoznat Text](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) a [získat rozpoznat výsledek operace Text](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) operace asynchronně rozpoznat a extrahovat tištěné nebo rukou psaný text z obrázku. |
   |Získat miniaturu | Používá [získat miniaturu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) operaci pro vygenerování miniaturu pro místní nebo vzdálené image. |

   Na následujícím snímku obrazovky ukazuje na stránku pro scénář analyzovat bitové kopie k dispozici po analýze Ukázkový obrázek.
   ![Snímek obrazovky stránky analyzovat image](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>Prozkoumat ukázkové aplikace

Řešení sady Visual Studio pro ukázkovou aplikaci pro počítačové zpracování obrazu obsahuje dva projekty:

* SampleUserControlLibrary  
  Projekt SampleUserControlLibrary poskytuje funkce, které sdílí několik ukázek služby Cognitive Services. Projekt obsahuje následující:
  * SampleScenarios  
    Ovládací prvek UserControl, který poskytuje standardizované prezentací, jako je například záhlaví, navigačním podokně a podokně obsahu pro ukázky. Pro počítačové zpracování obrazu ukázková aplikace používá tento ovládací prvek v souboru MainWindow.xaml okně zobrazit stránky scénář a přístup k informacím, které jsou sdíleny napříč scénáře, jako je adresa URL pro klíč a koncový bod předplatného.
  * SubscriptionKeyPage  
    Stránka, která poskytuje standardizované rozložení pro zadáním předplatné key a koncového bodu adresy URL pro ukázkovou aplikaci. Ukázkovou aplikaci pro počítačové zpracování obrazu používá tuto stránku pro správu klíč předplatného a adresu URL koncového bodu používá na stránkách scénář.
  * VideoResultControl  
    Ovládací prvek UserControl, který poskytuje standardizované prezentaci pro informace o videu. Ukázková aplikace pro počítačové zpracování obrazu nepoužívá tento ovládací prvek.
* Ukázky WPF VisionAPI  
  Hlavní projekt pro ukázkovou aplikaci pro počítačové zpracování obrazu, tento projekt obsahuje všechny zajímavé funkce pro počítačové zpracování obrazu. Projekt obsahuje následující:
  * AnalyzeInDomainPage.xaml  
    Na stránce scénář pro analyzovat Image se scénářem doménový Model.
  * AnalyzeImage.xaml  
    Na stránce scénář pro scénář analyzovat Image.
  * DescribePage.xaml  
    Na stránce scénář pro scénář popis Image.
  * ImageScenarioPage.cs  
    Třída ImageScenarioPage, z nichž všechny stránky scénář v ukázkové aplikaci odvozen. Tato třída slouží ke správě funkce, jako je zadání přihlašovacích údajů a formátování výstupu, sdílí všechny stránky scénář.
  * MainWindow.xaml  
    Hlavního okna pro ukázkovou aplikaci používá ovládací prvek SampleScenarios prezentovat na stránkách SubscriptionKeyPage a scénář.
  * OCRPage.xaml  
    Na stránce scénář pro scénář rozpoznat Text (OCR).
  * RecognizeLanguage.cs  
    Třída RecognizeLanguage, která poskytuje informace o jazycích podporovaných různé metody v ukázkové aplikaci.
  * TagsPage.xaml  
    Na stránce scénář pro scénář generovat značky.
  * TextRecognitionPage.xaml  
    Na stránce scénář pro scénář rozpoznat V2 Text (v angličtině).
  * ThumbnailPage.xaml  
    Na stránce scénář pro scénář získat miniaturu.

### <a name="explore-the-sample-code"></a>Prozkoumejte vzorový kód

Nejdůležitější části Ukázky kódu jsou uvedeny s bloky komentáře, které začínají `KEY SAMPLE CODE STARTS HERE` a na konci `KEY SAMPLE CODE ENDS HERE`, aby bylo snazší zkoumat ukázkovou aplikaci. Tyto nejdůležitějších částí ukázek kódu obsahovat kód/jí nejvíce hodilo učit, jak pomocí klientské knihovny rozhraní API pro počítačové zpracování obrazu můžete provádět různé úlohy. Můžete vyhledat `KEY SAMPLE CODE STARTS HERE` v sadě Visual Studio pro přesun mezi nejdůležitější části kódu v ukázkové aplikaci pro počítačové zpracování obrazu. 

Například `UploadAndAnalyzeImageAsync` uvedená metoda následující a součástí AnalyzePage.xaml, ukazuje, jak použít knihovnu klienta k analýze místní image vyvoláním `ComputerVisionClient.AnalyzeImageInStreamAsync` metody.

```csharp
private async Task<ImageAnalysis> UploadAndAnalyzeImageAsync(string imageFilePath)
{
    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE STARTS HERE
    // -----------------------------------------------------------------------

    //
    // Create Cognitive Services Vision API Service client.
    //
    using (var client = new ComputerVisionClient(Credentials) { Endpoint = Endpoint })
    {
        Log("ComputerVisionClient is created");

        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features.
            //
            Log("Calling ComputerVisionClient.AnalyzeImageInStreamAsync()...");
            VisualFeatureTypes[] visualFeatures = GetSelectedVisualFeatures();
            string language = (_language.SelectedItem as RecognizeLanguage).ShortCode;
            ImageAnalysis analysisResult = await client.AnalyzeImageInStreamAsync(imageFileStream, visualFeatures, null, language);
            return analysisResult;
        }
    }

    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE ENDS HERE
    // -----------------------------------------------------------------------
}
```

### <a name="explore-the-client-library"></a>Prozkoumejte knihovnu klienta

Tato ukázková aplikace používá klientské knihovny rozhraní API pro počítačové zpracování obrazu, dynamického zajišťování C# klienta obálku pro rozhraní API pro počítačové zpracování obrazu ve službě Azure Cognitive Services. Klientské knihovny je dostupný z Nugetu ve [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) balíčku. Když jste vytvořili aplikaci Visual Studio, jste získali z jeho odpovídajícího balíčku NuGet klientské knihovny. Můžete také zobrazit zdrojový kód klientské knihovny v `/ClientLibrary` složky `Microsoft/Cognitive-Vision-Windows` úložiště.

Klientská knihovna funkcí se soustředí kolem `ComputerVisionClient` třídy v `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` obor názvů, při modely používané `ComputerVisionClient` třídy při interakci s pro počítačové zpracování obrazu se nacházejí v `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` oboru názvů. V různých stránkách scénář XAML zahrnuty s ukázkovou aplikací, najdete následující `using` direktivy pro obory názvů:

```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// Use the following namespace for ComputerVisionClient.
// -----------------------------------------------------------------------
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```

Získáte další informace o různých metodách, které jsou součástí `ComputerVisionClient` třídu prozkoumejte scénáře součástí ukázkové aplikace pro počítačové zpracování obrazu.

## <a name="explore-the-analyze-image-scenario"></a>Prozkoumejte scénáře Analýza obrázku

Tento scénář se spravuje přes AnalyzePage.xaml stránky. Můžete vybrat vizuální funkce a jazyk pro analýzy a vidět na obrázku a výsledky. Na stránce scénář to provádí pomocí jedné z následujících metod, v závislosti na zdroj obrázku:

* UploadAndAnalyzeImageAsync  
  Tato metoda se používá pro místní Image, ve kterých musí kódováním bitovou kopii `Stream` a odesílat pro počítačové zpracování obrazu voláním `ComputerVisionClient.AnalyzeImageInStreamAsync` metody.
* AnalyzeUrlAsync  
  Tato metoda se používá pro vzdálené bitové kopie, ve kterých je adresa URL obrázku odeslané pro počítačové zpracování obrazu pomocí volání `ComputerVisionClient.AnalyzeImageAsync` metody.

`UploadAndAnalyzeImageAsync` Metoda vytvoří nový `ComputerVisionClient` instance, pomocí zadané předplatné key a koncového bodu adresy URL. Protože ukázková aplikace je analýza místní image, má k odeslání obsahu této bitové kopie pro počítačové zpracování obrazu. Otevře místní soubor zadaný v `imageFilePath` pro čtení `Stream`, potom získá vizuální funkce a jazyk vybraná na stránce scénář. Volá `ComputerVisionClient.AnalyzeImageInStreamAsync` metodu `Stream` pro soubor, vizuální funkce a jazyk, pak vrátí výsledek jako `ImageAnalysis` instance. Metody zděděné z `ImageScenarioPage` třídy prezentovat vrácené výsledky na stránce scénář.

`AnalyzeUrlAsync` Metoda vytvoří nový `ComputerVisionClient` instance, pomocí zadané předplatné key a koncového bodu adresy URL. Získá vizuální funkce a jazyk vybraná na stránce scénář. Volá `ComputerVisionClient.AnalyzeImageInStreamAsync` předejte adresu URL obrázku, vizuální funkce a jazyk, pak vrátí výsledek jako `ImageAnalysis` instance. Metody zděděné z `ImageScenarioPage` třídy prezentovat vrácené výsledky na stránce scénář.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Prozkoumejte analyzovat Image se scénářem doménový Model

Tento scénář se spravuje přes AnalyzeInDomainPage.xaml stránky. Můžete použít doménový model, jako například `celebrities` nebo `landmarks`a jazyk, který chcete provést analýzu specifického pro doménu bitové kopie a podívejte se na obrázku a výsledky. Na stránce scénář používá následující metody, v závislosti na zdroj obrázku:

* GetAvailableDomainModelsAsync  
  Tato metoda načte seznam dostupných doménových modelů z pro počítačové zpracování obrazu a naplní `_domainModelComboBox` ComboBox – ovládací prvek na stránce pomocí `ComputerVisionClient.ListModelsAsync` metody.
* UploadAndAnalyzeInDomainImageAsync  
  Tato metoda se používá pro místní Image, ve kterých musí kódováním bitovou kopii `Stream` a odesílat pro počítačové zpracování obrazu voláním `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` metody.
* AnalyzeInDomainUrlAsync  
  Tato metoda se používá pro vzdálené bitové kopie, ve kterých je adresa URL obrázku odeslané pro počítačové zpracování obrazu pomocí volání `ComputerVisionClient.AnalyzeImageByDomainAsync` metody.

`UploadAndAnalyzeInDomainImageAsync` Metoda vytvoří nový `ComputerVisionClient` instance, pomocí zadané předplatné key a koncového bodu adresy URL. Protože ukázková aplikace je analýza místní image, má k odeslání obsahu této bitové kopie pro počítačové zpracování obrazu. Otevře místní soubor zadaný v `imageFilePath` pro čtení `Stream`, pak získá jazyk vybraná na stránce scénář. Volá `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` metodu `Stream` k souboru, název doménový model a jazyk, pak vrátí výsledek jako `DomainModelResults` instance. Metody zděděné z `ImageScenarioPage` třídy prezentovat vrácené výsledky na stránce scénář.

`AnalyzeInDomainUrlAsync` Metoda vytvoří nový `ComputerVisionClient` instance, pomocí zadané předplatné key a koncového bodu adresy URL. Získá jazyk vybraná na stránce scénář. Volá `ComputerVisionClient.AnalyzeImageByDomainAsync` předejte adresu URL obrázku, vizuální funkce a jazyk, pak vrátí výsledek jako `DomainModelResults` instance. Metody zděděné z `ImageScenarioPage` třídy prezentovat vrácené výsledky na stránce scénář.

## <a name="explore-the-describe-image-scenario"></a>Prozkoumejte scénáře popisují Image

Tento scénář se spravuje přes DescribePage.xaml stránky. Můžete zvolit jazyk k vytvoření čitelný popis bitové kopie a vidět na obrázku a výsledky. Na stránce scénář používá následující metody, v závislosti na zdroj obrázku:

* UploadAndDescribeImageAsync  
  Tato metoda se používá pro místní Image, ve kterých musí kódováním bitovou kopii `Stream` a odesílat pro počítačové zpracování obrazu voláním `ComputerVisionClient.DescribeImageInStreamAsync` metody.
* DescribeUrlAsync  
  Tato metoda se používá pro vzdálené bitové kopie, ve kterých je adresa URL obrázku odeslané pro počítačové zpracování obrazu pomocí volání `ComputerVisionClient.DescribeImageAsync` metody.

`UploadAndDescribeImageAsync` Metoda vytvoří nový `ComputerVisionClient` instance, pomocí zadané předplatné key a koncového bodu adresy URL. Protože ukázková aplikace je analýza místní image, má k odeslání obsahu této bitové kopie pro počítačové zpracování obrazu. Otevře místní soubor zadaný v `imageFilePath` pro čtení `Stream`, pak získá jazyk vybraná na stránce scénář. Volá `ComputerVisionClient.DescribeImageInStreamAsync` metodu `Stream` pro soubor, maximální počet zájemců (v tomto případě 3) a jazyk, pak vrátí výsledek jako `ImageDescription` instance. Metody zděděné z `ImageScenarioPage` třídy prezentovat vrácené výsledky na stránce scénář.

`DescribeUrlAsync` Metoda vytvoří nový `ComputerVisionClient` instance, pomocí zadané předplatné key a koncového bodu adresy URL. Získá jazyk vybraná na stránce scénář. Volá `ComputerVisionClient.DescribeImageAsync` předejte adresu URL obrázku, maximální počet zájemců (v tomto případě 3) a jazyk, pak vrátí výsledek jako `ImageDescription` instance. Metody zděděné z `ImageScenarioPage` třídy prezentovat vrácené výsledky na stránce scénář.

## <a name="explore-the-generate-tags-scenario"></a>Prozkoumejte scénáře generovat značky

Tento scénář se spravuje přes TagsPage.xaml stránky. Můžete zvolit jazyk k označení vizuální funkce bitovou kopii a vidět na obrázku a výsledky. Na stránce scénář používá následující metody, v závislosti na zdroj obrázku:

* UploadAndGetTagsForImageAsync  
  Tato metoda se používá pro místní Image, ve kterých musí kódováním bitovou kopii `Stream` a odesílat pro počítačové zpracování obrazu voláním `ComputerVisionClient.TagImageInStreamAsync` metody.
* GenerateTagsForUrlAsync  
  Tato metoda se používá pro vzdálené bitové kopie, ve kterých je adresa URL obrázku odeslané pro počítačové zpracování obrazu pomocí volání `ComputerVisionClient.TagImageAsync` metody.

`UploadAndGetTagsForImageAsync` Metoda vytvoří nový `ComputerVisionClient` instance, pomocí zadané předplatné key a koncového bodu adresy URL. Protože ukázková aplikace je analýza místní image, má k odeslání obsahu této bitové kopie pro počítačové zpracování obrazu. Otevře místní soubor zadaný v `imageFilePath` pro čtení `Stream`, pak získá jazyk vybraná na stránce scénář. Volá `ComputerVisionClient.TagImageInStreamAsync` metodu `Stream` soubor a jazyk, pak vrátí výsledek jako `TagResult` instance. Metody zděděné z `ImageScenarioPage` třídy prezentovat vrácené výsledky na stránce scénář.

`GenerateTagsForUrlAsync` Metoda vytvoří nový `ComputerVisionClient` instance, pomocí zadané předplatné key a koncového bodu adresy URL. Získá jazyk vybraná na stránce scénář. Volá `ComputerVisionClient.TagImageAsync` předejte adresu URL obrázku a jazyk, pak vrátí výsledek jako `TagResult` instance. Metody zděděné z `ImageScenarioPage` třídy prezentovat vrácené výsledky na stránce scénář.

## <a name="explore-the-recognize-text-ocr-scenario"></a>Prozkoumejte scénáře rozpoznat Text (OCR)

Tento scénář se spravuje přes OCRPage.xaml stránky. Můžete zvolit jazyk rozpoznat a extrahovat tištěný text z obrázku a vidět na obrázku a výsledky. Na stránce scénář používá následující metody, v závislosti na zdroj obrázku:

* UploadAndRecognizeImageAsync  
  Tato metoda se používá pro místní Image, ve kterých musí kódováním bitovou kopii `Stream` a odesílat pro počítačové zpracování obrazu voláním `ComputerVisionClient.RecognizePrintedTextInStreamAsync` metody.
* RecognizeUrlAsync  
  Tato metoda se používá pro vzdálené bitové kopie, ve kterých je adresa URL obrázku odeslané pro počítačové zpracování obrazu pomocí volání `ComputerVisionClient.RecognizePrintedTextAsync` metody.

`UploadAndRecognizeImageAsync` Metoda vytvoří nový `ComputerVisionClient` instance, pomocí zadané předplatné key a koncového bodu adresy URL. Protože ukázková aplikace je analýza místní image, má k odeslání obsahu této bitové kopie pro počítačové zpracování obrazu. Otevře místní soubor zadaný v `imageFilePath` pro čtení `Stream`, pak získá jazyk vybraná na stránce scénář. Volá `ComputerVisionClient.RecognizePrintedTextInStreamAsync` metoda označující, že není zjištěna orientaci a předání `Stream` soubor a jazyk, pak vrátí výsledek jako `OcrResult` instance. Metody zděděné z `ImageScenarioPage` třídy prezentovat vrácené výsledky na stránce scénář.

`RecognizeUrlAsync` Metoda vytvoří nový `ComputerVisionClient` instance, pomocí zadané předplatné key a koncového bodu adresy URL. Získá jazyk vybraná na stránce scénář. Volá `ComputerVisionClient.RecognizePrintedTextAsync` metoda označující, že není zjištěna orientaci a předá adresu URL obrázku a jazyk, pak vrátí výsledek jako `OcrResult` instance. Metody zděděné z `ImageScenarioPage` třídy prezentovat vrácené výsledky na stránce scénář.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Prozkoumejte scénář rozpoznat V2 Text (v angličtině)

Tento scénář se spravuje přes TextRecognitionPage.xaml stránky. Můžete zvolit režim rozpoznávání a jazyk, který se asynchronně rozpoznat a extrahovat tištěné nebo rukou psaný text z obrázku a vidět na obrázku a výsledky. Na stránce scénář používá následující metody, v závislosti na zdroj obrázku:

* UploadAndRecognizeImageAsync  
  Tato metoda se používá pro místní Image, ve kterých musí kódováním bitovou kopii `Stream` a odesílat pro počítačové zpracování obrazu pomocí volání `RecognizeAsync` metoda a předáním parametrizované delegáta `ComputerVisionClient.RecognizeTextInStreamAsync` metoda.
* RecognizeUrlAsync  
  Tato metoda se používá pro vzdálené bitové kopie, ve kterých je adresa URL obrázku odeslané pro počítačové zpracování obrazu voláním `RecognizeAsync` metoda a předání parametrizované delegáta `ComputerVisionClient.RecognizeTextAsync` metoda.
* RecognizeAsync tato metoda zpracovává asynchronní volání pro obě `UploadAndRecognizeImageAsync` a `RecognizeUrlAsync` metody, jakož i dotazování na výsledky pomocí volání `ComputerVisionClient.GetTextOperationResultAsync` metoda.

Na rozdíl od jiné scénáře zahrnuté v ukázkové aplikaci pro počítačové zpracování obrazu tento scénář je asynchronní, jedna metoda je volána pro spuštění procesu, ale jiné metody je volána k zkontrolovat stav a vrátí výsledky tohoto procesu. Logický tok v tomto scénáři se poněkud liší od v jiných scénářích.

`UploadAndRecognizeImageAsync` Metoda otevře místní soubor zadaný v `imageFilePath` pro čtení `Stream`, pak zavolá `RecognizeAsync` metodu:

* Výraz lambda pro parametrizované asynchronní delegáta `ComputerVisionClient.RecognizeTextInStreamAsync` metody s `Stream` režim rozpoznávání jako parametry, a souboru v `GetHeadersAsyncFunc`.
* Výraz lambda pro delegáta, kterého získat `Operation-Location` hodnota hlavičky odpovědi, `GetOperationUrlFunc`.

`RecognizeUrlAsync` Volání metod `RecognizeAsync` metodu:

* Výraz lambda pro parametrizované asynchronní delegáta `ComputerVisionClient.RecognizeTextAsync` metoda s adresou URL vzdáleného bitové kopie a rozpoznávání režimu jako parametry v `GetHeadersAsyncFunc`.
* Výraz lambda pro delegáta, kterého získat `Operation-Location` hodnota hlavičky odpovědi, `GetOperationUrlFunc`.

Když `RecognizeAsync` dokončení metody i `UploadAndRecognizeImageAsync` a `RecognizeUrlAsync` metody vrátí výsledek v podobě `TextOperationResult` instance. Metody zděděné z `ImageScenarioPage` třídy prezentovat vrácené výsledky na stránce scénář.

`RecognizeAsync` Metoda volání parametrizovaného delegáta pro buď `ComputerVisionClient.RecognizeTextInStreamAsync` nebo `ComputerVisionClient.RecognizeTextAsync` předaný metodě `GetHeadersAsyncFunc` a čeká odpověď. Metoda pak zavolá delegát předaný `GetOperationUrlFunc` zobrazíte `Operation-Location` hodnota hlavičky odpovědi z odpovědi. Tato hodnota je adresa URL sloužící k načtení výsledků metoda předaný `GetHeadersAsyncFunc` z pro počítačové zpracování obrazu.

`RecognizeAsync` Pak zavolá metodu `ComputerVisionClient.GetTextOperationResultAsync` předejte adresu URL získaných `Operation-Location` hlavičku odpovědi k získání stavu a výsledku metody předaný `GetHeadersAsyncFunc`. Pokud stav není vyplývá, že metoda nedokončí, úspěšně nebo neúspěšně, `RecognizeAsync` volání metody `ComputerVisionClient.GetTextOperationResultAsync` 3 vícekrát, 3 sekund mezi voláními. `RecognizeAsync` Metoda vrátí výsledky v metodě, která ji zavolala.

## <a name="explore-the-get-thumbnail-scenario"></a>Prozkoumejte scénáře získat miniaturu

Tento scénář se spravuje přes ThumbnailPage.xaml stránky. Můžete určit, jestli chcete používat inteligentní oříznutí a zadejte požadovanou výšku a šířku, k vytvoření miniatury pomocí bitové kopie a vidět na obrázku a výsledky. Na stránce scénář používá následující metody, v závislosti na zdroj obrázku:

* UploadAndThumbnailImageAsync  
  Tato metoda se používá pro místní Image, ve kterých musí kódováním bitovou kopii `Stream` a odesílat pro počítačové zpracování obrazu voláním `ComputerVisionClient.GenerateThumbnailInStreamAsync` metody.
* ThumbnailUrlAsync  
  Tato metoda se používá pro vzdálené bitové kopie, ve kterých je adresa URL obrázku odeslané pro počítačové zpracování obrazu pomocí volání `ComputerVisionClient.GenerateThumbnailAsync` metody.

`UploadAndThumbnailImageAsync` Metoda vytvoří nový `ComputerVisionClient` instance, pomocí zadané předplatné key a koncového bodu adresy URL. Protože ukázková aplikace je analýza místní image, má k odeslání obsahu této bitové kopie pro počítačové zpracování obrazu. Otevře se místní soubor zadaný v `imageFilePath` pro čtení `Stream`. Volá `ComputerVisionClient.GenerateThumbnailInStreamAsync` předejte šířky, výšky `Stream` pro soubor a jestli se má použít inteligentní oříznutí, pak vrátí výsledek jako `Stream`. Metody zděděné z `ImageScenarioPage` třídy prezentovat vrácené výsledky na stránce scénář.

`RecognizeUrlAsync` Metoda vytvoří nový `ComputerVisionClient` instance, pomocí zadané předplatné key a koncového bodu adresy URL. Volá `ComputerVisionClient.GenerateThumbnailAsync` předejte šířku, výšku, adresa URL obrázku a, jestli chcete používat inteligentní oříznutí, pak vrátí výsledek jako `Stream`. Metody zděděné z `ImageScenarioPage` třídy prezentovat vrácené výsledky na stránce scénář.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte složku, do které jste naklonovali `Microsoft/Cognitive-Vision-Windows` úložiště. Pokud jste se rozhodli použít ukázkové obrázky, také odstranit složku, do které jste naklonovali `Microsoft/Cognitive-Face-Windows` úložiště.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Začínáme s rozhraním API pro rozpoznávání tváře](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
