---
title: 'Ukázka: zkoumání aplikace pro zpracování obrázků vC#'
titleSuffix: Azure Cognitive Services
description: Prozkoumejte základní aplikaci pro Windows, která používá rozhraní API pro počítačové zpracování obrazu ve službě Azure Cognitive Services. Provádějte optické rozpoznávání znaků (OCR), vytvářejte miniatury a pracujte s vizuálními funkcemi obrázku.
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: b492d8e3bdcf6d9a41df3eb79ef159985cc715cf
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76168881"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>Ukázka: Prozkoumejte aplikaci pro zpracování obrázkůC#

Prozkoumejte základní aplikaci pro Windows, která používá Počítačové zpracování obrazu k provádění optického rozpoznávání znaků (OCR), vytváření miniatur inteligentních oříznutí a navíc k detekci, kategorizaci, označení a popisu vizuálních funkcí, včetně ploch, v obrázku. Následující příklad umožňuje odeslat adresu URL obrázku nebo místně uložený soubor. Tento příklad Open Source můžete použít jako šablonu pro vytvoření vlastní aplikace pro Windows pomocí rozhraní API pro počítačové zpracování obrazu a Windows Presentation Foundation (WPF), což je součást .NET Framework.

> [!div class="checklist"]
> * Získat ukázkovou aplikaci z GitHubu
> * Otevření a sestavení ukázkové aplikace v aplikaci Visual Studio
> * Spusťte ukázkovou aplikaci a pracujte s ní, abyste mohli provádět různé scénáře.
> * Prozkoumejte různé scénáře, které jsou součástí ukázkové aplikace.

## <a name="prerequisites"></a>Požadavky

Před zkoumáním ukázkové aplikace se ujistěte, že jste splnili následující požadavky:

* Musíte mít [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) nebo novější.
* Musíte mít klíč předplatného pro počítačové zpracování obrazu. Bezplatný zkušební klíč si můžete [vyzkoušet Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Případně postupujte podle pokynů v části [Vytvoření účtu Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pro přihlášení k odběru počítačové zpracování obrazu a získání klíče. Poznamenejte si taky adresu URL koncového bodu služby.

## <a name="get-the-sample-app"></a>Získání ukázkové aplikace

Ukázková aplikace Počítačové zpracování obrazu je k dispozici na GitHubu z úložiště `Microsoft/Cognitive-Vision-Windows`. Toto úložiště zahrnuje také úložiště `Microsoft/Cognitive-Common-Windows` jako dílčí modul Git. Toto úložiště, včetně dílčího modulu, můžete rekurzivně klonovat, a to buď pomocí příkazu `git clone --recurse-submodules` z příkazového řádku, nebo pomocí aplikace GitHub Desktop.

Chcete-li například rekurzivně klonovat úložiště pro ukázkovou aplikaci Počítačové zpracování obrazu z příkazového řádku, spusťte následující příkaz:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Toto úložiště nestahujte jako soubor ZIP. Git nezahrnuje dílčí moduly při stahování úložiště jako souboru ZIP.

### <a name="get-optional-sample-images"></a>Získat volitelné ukázkové obrázky

Volitelně můžete použít ukázkové image dodávané s ukázkovou aplikací pro [tvář](../../Face/Overview.md) , které jsou k dispozici na GitHubu z `Microsoft/Cognitive-Face-Windows`ho úložiště. Tato ukázková aplikace zahrnuje složku, `/Data`, která obsahuje více imagí lidí. Toto úložiště můžete rekurzivně klonovat i pomocí metod popsaných pro ukázkovou aplikaci Počítačové zpracování obrazu.

Chcete-li například rekurzivně klonovat úložiště pro ukázkovou aplikaci na ploše z příkazového řádku, spusťte následující příkaz:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Otevření a sestavení ukázkové aplikace v aplikaci Visual Studio

Nejprve musíte vytvořit ukázkovou aplikaci, aby mohla aplikace Visual Studio vyřešit závislosti, než můžete spustit nebo prozkoumat ukázkovou aplikaci. Chcete-li otevřít a sestavit ukázkovou aplikaci, proveďte následující kroky:

1. Otevřete soubor řešení sady Visual Studio, `/Sample-WPF/VisionAPI-WPF-Samples.sln`v aplikaci Visual Studio.
1. Ujistěte se, že řešení sady Visual Studio obsahuje dva projekty:  

   * SampleUserControlLibrary
   * VisionAPI-WPF – ukázky  

   Pokud není projekt SampleUserControlLibrary k dispozici, potvrďte, že jste rekurzivní klonování úložiště `Microsoft/Cognitive-Vision-Windows`.
1. V aplikaci Visual Studio stiskněte kombinaci kláves CTRL + SHIFT + B nebo zvolte **sestavit** z nabídky pásu karet a pak zvolte **Sestavit řešení** a sestavte řešení.

## <a name="run-and-interact-with-the-sample-app"></a>Spuštění a interakce s ukázkovou aplikací

Ukázkovou aplikaci můžete spustit, abyste viděli, jak komunikuje s vámi a s knihovnou klienta Počítačové zpracování obrazu při provádění různých úloh, jako je například generování miniatur nebo označování obrázků. Pokud chcete spustit ukázkovou aplikaci a pracovat s ní, proveďte následující kroky:

1. Po dokončení sestavení stiskněte klávesu **F5** nebo zvolte možnost **ladění** z nabídky pásu karet a pak zvolte možnost **Spustit ladění** a spusťte ukázkovou aplikaci.
1. Když se zobrazí ukázková aplikace, v navigačním podokně vyberte **Správa klíčů předplatného** . zobrazí se stránka Správa klíčů předplatného.
   Stránka správy klíčů předplatného ![](../Images/Vision_UI_Subscription.PNG)  
1. Do **klíče předplatného**zadejte svůj klíč předplatného.
1. Do **koncového bodu**zadejte adresu URL koncového bodu.  
   Pokud například používáte klíč předplatného z Počítačové zpracování obrazu bezplatné zkušební verze, zadejte následující adresu URL koncového bodu: `https://westcentralus.api.cognitive.microsoft.com`  
   [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Pokud nechcete zadat klíč předplatného a adresu URL koncového bodu při příštím spuštění ukázkové aplikace, klikněte na **Uložit nastavení** a uložte klíč předplatného a adresu URL koncového bodu do vašeho počítače. Pokud chcete odstranit dřív uložený klíč předplatného a adresu URL koncového bodu, vyberte **Odstranit nastavení**.

   > [!NOTE]
   > Ukázková aplikace používá izolované úložiště a `System.IO.IsolatedStorage`k uložení klíče předplatného a adresy URL koncového bodu.

1. V části **Vybrat scénář** v navigačním podokně vyberte jeden z aktuálně zahrnutých scénářů v ukázkové aplikaci:  

   | Scénář | Popis |
   |----------|-------------|
   |Analyzovat obrázek | Pomocí operace [analyzovat obrázek](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) analyzuje místní nebo vzdálenou bitovou kopii. Můžete zvolit vizuální funkce a jazyk pro analýzu a zobrazit obraz i výsledky.  |
   |Analyzovat Image pomocí doménového modelu | Pomocí operace [vypsat modely specifické pro doménu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) zobrazí seznam doménových modelů, ze kterých můžete vybírat, a operaci [rozpoznávání obsahu specifického pro doménu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) k analýze místní nebo vzdálené Image pomocí vybraného doménového modelu. Můžete také zvolit jazyk pro analýzu. |
   |Popsat obrázek | Pomocí operace [popsat obrázek](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) vytvoří uživatelsky čitelný popis místní nebo vzdálené image. Můžete také zvolit jazyk pro popis. |
   |Generovat značky | Používá operaci [Image značek](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) k označení vizuálních funkcí místní nebo vzdálené image. Můžete také zvolit jazyk používaný pro značky. |
   |Rozpoznávání textu (OCR) | Pomocí operace [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) rozpozná a extrahuje vytištěný text z obrázku. Můžete buď zvolit jazyk, který se má použít, nebo nechat Počítačové zpracování obrazu automaticky rozpoznat jazyk. |
   |Rozpoznávání textu v2 (angličtina) | Používá operace [rozpoznávání textu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) a [získání výsledků operací rozpoznávání textu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) k asynchronnímu rozpoznání a extrakci vytištěného a přepsaného textu z obrázku. |
   |Získat miniaturu | Vygeneruje miniaturu pro místní nebo vzdálenou bitovou kopii pomocí operace [získat miniaturu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) . |

   Následující snímek obrazovky znázorňuje stránku poskytnutou pro scénář analýzy obrázku po analýze ukázkové image.
   ![snímku stránky analyzovat obrázek](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>Prozkoumejte ukázkovou aplikaci

Řešení sady Visual Studio pro ukázkovou aplikaci Počítačové zpracování obrazu obsahuje dva projekty:

* SampleUserControlLibrary  
  Projekt SampleUserControlLibrary poskytuje funkce sdílené více ukázkami Cognitive Services. Projekt obsahuje následující:
  * SampleScenarios  
    Prvek UserControl, který poskytuje standardizovanou prezentaci, jako je záhlaví, navigační podokno a podokno obsahu, pro ukázky. Ukázková aplikace Počítačové zpracování obrazu používá tento ovládací prvek v okně MainWindow. XAML pro zobrazení stránek scénáře a přístup k informacím sdíleným ve scénářích, jako je klíč předplatného a adresa URL koncového bodu.
  * SubscriptionKeyPage  
    Stránka, která poskytuje standardizované rozložení pro zadání klíče předplatného a adresy URL koncového bodu pro ukázkovou aplikaci. Ukázková aplikace Počítačové zpracování obrazu používá tuto stránku ke správě klíče předplatného a adresy URL koncového bodu, které používají stránky scénáře.
  * VideoResultControl  
    UserControl, který poskytuje standardizovanou prezentaci pro informace o videu. Ukázková aplikace Počítačové zpracování obrazu nepoužívá tento ovládací prvek.
* VisionAPI-WPF – ukázky  
  Hlavní projekt pro ukázkovou aplikaci Počítačové zpracování obrazu obsahuje tento projekt všechny zajímavé funkce pro Počítačové zpracování obrazu. Projekt obsahuje následující:
  * AnalyzeInDomainPage. XAML  
    Stránka scénáře pro scénář analyzovat image s doménovým modelem
  * AnalyzeImage. XAML  
    Stránka scénáře pro scénář analýza obrázku
  * DescribePage. XAML  
    Stránka scénáře pro scénář popis obrázku
  * ImageScenarioPage.cs  
    Třída ImageScenarioPage, ze které jsou odvozeny všechny stránky scénáře v ukázkové aplikaci. Tato třída spravuje funkce, jako je například poskytování přihlašovacích údajů a formátování výstupu, sdílené všemi stránkami scénáře.
  * MainWindow.xaml  
    Hlavní okno pro ukázkovou aplikaci používá ovládací prvek SampleScenarios, který prezentuje stránky SubscriptionKeyPage a scénářů.
  * OCRPage. XAML  
    Stránka scénáře pro scénář Rozpoznávání textu (OCR).
  * RecognizeLanguage.cs  
    Třída RecognizeLanguage, která poskytuje informace o jazycích podporovaných různými metodami v ukázkové aplikaci.
  * TagsPage. XAML  
    Stránka scénáře pro scénář generovat značky
  * TextRecognitionPage. XAML  
    Stránka scénáře pro scénář Rozpoznávání textu v2 (anglicky)
  * ThumbnailPage. XAML  
    Stránka scénáře pro scénář získat miniaturu

### <a name="explore-the-sample-code"></a>Prozkoumat vzorový kód

Klíčové části vzorového kódu jsou orámovány s bloky komentáře, které začínají `KEY SAMPLE CODE STARTS HERE` a končí `KEY SAMPLE CODE ENDS HERE`, aby bylo snazší prozkoumat ukázkovou aplikaci. Tyto klíčové části ukázkového kódu obsahují kód, který je nejrelevantnější pro učení, jak pomocí klientské knihovny rozhraní API pro počítačové zpracování obrazu provádět různé úlohy. V aplikaci Visual Studio můžete hledat `KEY SAMPLE CODE STARTS HERE` pro přesun mezi nejrelevantnějšími oddíly kódu v ukázkové aplikaci Počítačové zpracování obrazu. 

Například metoda `UploadAndAnalyzeImageAsync`, uvedená níže a obsažena v souboru AnalyzePage. XAML, ukazuje, jak použít knihovnu klienta k analýze místní image vyvoláním metody `ComputerVisionClient.AnalyzeImageInStreamAsync`.

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

### <a name="explore-the-client-library"></a>Prozkoumat klientskou knihovnu

Tato ukázková aplikace používá klientskou knihovnu rozhraní API pro počítačové zpracování obrazu, tenkých C# klientských obálk pro rozhraní API pro počítačové zpracování obrazu ve službě Azure Cognitive Services. Knihovna klienta je dostupná ze sady NuGet v balíčku [Microsoft. Azure. cognitiveservices Account. Vision. ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) . Při sestavování aplikace sady Visual Studio jste načetli knihovnu klienta z odpovídajícího balíčku NuGet. Zdrojový kód klientské knihovny můžete zobrazit také ve složce `/ClientLibrary` úložiště `Microsoft/Cognitive-Vision-Windows`.

Funkce klientské knihovny vycentruje kolem `ComputerVisionClient` třídy v oboru názvů `Microsoft.Azure.CognitiveServices.Vision.ComputerVision`, zatímco při interakci s Počítačové zpracování obrazu jsou v oboru názvů `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` nalezeny modely používané třídou `ComputerVisionClient`. V různých stránkách scénáře XAML, které jsou součástí ukázkové aplikace, najdete následující direktivy `using` pro tyto obory názvů:

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

Další informace o různých metodách, které jsou součástí `ComputerVisionClient` třídy, se dozvíte při zkoumání scénářů, které jsou součástí ukázkové aplikace Počítačové zpracování obrazu.

## <a name="explore-the-analyze-image-scenario"></a>Prozkoumat scénář analýzy obrázku

Tento scénář je spravován stránkou AnalyzePage. XAML. Můžete zvolit vizuální funkce a jazyk pro analýzu a zobrazit obraz i výsledky. Na stránce scénář se používá jedna z následujících metod v závislosti na zdroji obrázku:

* UploadAndAnalyzeImageAsync  
  Tato metoda se používá pro místní image, ve kterých musí být bitová kopie kódována jako `Stream` a odeslána do Počítačové zpracování obrazu voláním metody `ComputerVisionClient.AnalyzeImageInStreamAsync`.
* AnalyzeUrlAsync  
  Tato metoda se používá pro vzdálené image, ve které se adresa URL obrázku pošle Počítačové zpracování obrazu zavoláním metody `ComputerVisionClient.AnalyzeImageAsync`.

Metoda `UploadAndAnalyzeImageAsync` vytvoří novou instanci `ComputerVisionClient` pomocí zadaného klíče předplatného a adresy URL koncového bodu. Vzhledem k tomu, že ukázková aplikace analyzuje místní bitovou kopii, musí odeslat obsah této image do Počítačové zpracování obrazu. Otevře místní soubor zadaný v `imageFilePath` pro čtení jako `Stream`a pak získá vizuální funkce a jazyk vybraný na stránce scénář. Volá metodu `ComputerVisionClient.AnalyzeImageInStreamAsync`, předává `Stream` souboru, funkce vizuálu a jazyk a následně vrátí výsledek jako instanci `ImageAnalysis`. Metody zděděné z třídy `ImageScenarioPage` prezentují vrácené výsledky na stránce scénáře.

Metoda `AnalyzeUrlAsync` vytvoří novou instanci `ComputerVisionClient` pomocí zadaného klíče předplatného a adresy URL koncového bodu. Získá vizuální funkce a jazyk vybraný na stránce scénář. Volá metodu `ComputerVisionClient.AnalyzeImageInStreamAsync`, předává adresu URL obrázku, vizuální funkce a jazyk a potom vrátí výsledek jako instanci `ImageAnalysis`. Metody zděděné z třídy `ImageScenarioPage` prezentují vrácené výsledky na stránce scénáře.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Prozkoumat scénář analyzovat Image pomocí doménového modelu

Tento scénář je spravován stránkou AnalyzeInDomainPage. XAML. Můžete zvolit doménový model, například `celebrities` nebo `landmarks`, a jazyk, který provede analýzu konkrétní domény, a zobrazit obraz i výsledky. Stránka scénář používá následující metody v závislosti na zdroji obrázku:

* GetAvailableDomainModelsAsync  
  Tato metoda získá seznam dostupných doménových modelů z Počítačové zpracování obrazu a naplní `_domainModelComboBox` ovládací prvek ComboBox na stránce pomocí metody `ComputerVisionClient.ListModelsAsync`.
* UploadAndAnalyzeInDomainImageAsync  
  Tato metoda se používá pro místní image, ve kterých musí být bitová kopie kódována jako `Stream` a odeslána do Počítačové zpracování obrazu voláním metody `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync`.
* AnalyzeInDomainUrlAsync  
  Tato metoda se používá pro vzdálené image, ve které se adresa URL obrázku pošle Počítačové zpracování obrazu zavoláním metody `ComputerVisionClient.AnalyzeImageByDomainAsync`.

Metoda `UploadAndAnalyzeInDomainImageAsync` vytvoří novou instanci `ComputerVisionClient` pomocí zadaného klíče předplatného a adresy URL koncového bodu. Vzhledem k tomu, že ukázková aplikace analyzuje místní bitovou kopii, musí odeslat obsah této image do Počítačové zpracování obrazu. Otevře místní soubor zadaný v `imageFilePath` pro čtení jako `Stream`a pak získá jazyk vybraný na stránce scénář. Volá metodu `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync`, předává `Stream` souboru, název doménového modelu a jazyk a vrátí výsledek jako instanci `DomainModelResults`. Metody zděděné z třídy `ImageScenarioPage` prezentují vrácené výsledky na stránce scénáře.

Metoda `AnalyzeInDomainUrlAsync` vytvoří novou instanci `ComputerVisionClient` pomocí zadaného klíče předplatného a adresy URL koncového bodu. Získá jazyk vybraný na stránce scénář. Volá metodu `ComputerVisionClient.AnalyzeImageByDomainAsync`, předává adresu URL obrázku, vizuální funkce a jazyk a potom vrátí výsledek jako instanci `DomainModelResults`. Metody zděděné z třídy `ImageScenarioPage` prezentují vrácené výsledky na stránce scénáře.

## <a name="explore-the-describe-image-scenario"></a>Prozkoumejte scénář popisující obrázek

Tento scénář je spravován stránkou DescribePage. XAML. Můžete zvolit jazyk pro vytvoření uživatelsky čitelného popisu obrázku a zobrazit obraz i výsledky. Stránka scénář používá následující metody v závislosti na zdroji obrázku:

* UploadAndDescribeImageAsync  
  Tato metoda se používá pro místní image, ve kterých musí být bitová kopie kódována jako `Stream` a odeslána do Počítačové zpracování obrazu voláním metody `ComputerVisionClient.DescribeImageInStreamAsync`.
* DescribeUrlAsync  
  Tato metoda se používá pro vzdálené image, ve které se adresa URL obrázku pošle Počítačové zpracování obrazu zavoláním metody `ComputerVisionClient.DescribeImageAsync`.

Metoda `UploadAndDescribeImageAsync` vytvoří novou instanci `ComputerVisionClient` pomocí zadaného klíče předplatného a adresy URL koncového bodu. Vzhledem k tomu, že ukázková aplikace analyzuje místní bitovou kopii, musí odeslat obsah této image do Počítačové zpracování obrazu. Otevře místní soubor zadaný v `imageFilePath` pro čtení jako `Stream`a pak získá jazyk vybraný na stránce scénář. Volá metodu `ComputerVisionClient.DescribeImageInStreamAsync`, předává `Stream` souboru, maximální počet kandidátů (v tomto případě 3) a jazyk a vrátí výsledek jako instanci `ImageDescription`. Metody zděděné z třídy `ImageScenarioPage` prezentují vrácené výsledky na stránce scénáře.

Metoda `DescribeUrlAsync` vytvoří novou instanci `ComputerVisionClient` pomocí zadaného klíče předplatného a adresy URL koncového bodu. Získá jazyk vybraný na stránce scénář. Volá metodu `ComputerVisionClient.DescribeImageAsync`, předává adresu URL obrázku, maximální počet kandidátů (v tomto případě 3) a jazyk a vrátí výsledek jako instanci `ImageDescription`. Metody zděděné z třídy `ImageScenarioPage` prezentují vrácené výsledky na stránce scénáře.

## <a name="explore-the-generate-tags-scenario"></a>Prozkoumat scénář generovat značky

Tento scénář je spravován stránkou TagsPage. XAML. Můžete zvolit jazyk k označení vizuálních funkcí obrázku a zobrazit obraz i výsledky. Stránka scénář používá následující metody v závislosti na zdroji obrázku:

* UploadAndGetTagsForImageAsync  
  Tato metoda se používá pro místní image, ve kterých musí být bitová kopie kódována jako `Stream` a odeslána do Počítačové zpracování obrazu voláním metody `ComputerVisionClient.TagImageInStreamAsync`.
* GenerateTagsForUrlAsync  
  Tato metoda se používá pro vzdálené image, ve které se adresa URL obrázku pošle Počítačové zpracování obrazu zavoláním metody `ComputerVisionClient.TagImageAsync`.

Metoda `UploadAndGetTagsForImageAsync` vytvoří novou instanci `ComputerVisionClient` pomocí zadaného klíče předplatného a adresy URL koncového bodu. Vzhledem k tomu, že ukázková aplikace analyzuje místní bitovou kopii, musí odeslat obsah této image do Počítačové zpracování obrazu. Otevře místní soubor zadaný v `imageFilePath` pro čtení jako `Stream`a pak získá jazyk vybraný na stránce scénář. Volá metodu `ComputerVisionClient.TagImageInStreamAsync`, předává `Stream` souboru a jazyku a potom vrátí výsledek jako instanci `TagResult`. Metody zděděné z třídy `ImageScenarioPage` prezentují vrácené výsledky na stránce scénáře.

Metoda `GenerateTagsForUrlAsync` vytvoří novou instanci `ComputerVisionClient` pomocí zadaného klíče předplatného a adresy URL koncového bodu. Získá jazyk vybraný na stránce scénář. Volá metodu `ComputerVisionClient.TagImageAsync`, projde adresu URL obrázku a jazyk a vrátí výsledek jako instanci `TagResult`. Metody zděděné z třídy `ImageScenarioPage` prezentují vrácené výsledky na stránce scénáře.

## <a name="explore-the-recognize-text-ocr-scenario"></a>Prozkoumat scénář Rozpoznávání textu (OCR)

Tento scénář je spravován stránkou OCRPage. XAML. Můžete zvolit jazyk pro rozpoznání a extrakci vytištěného textu z obrázku a zobrazení obrázku i výsledků. Stránka scénář používá následující metody v závislosti na zdroji obrázku:

* UploadAndRecognizeImageAsync  
  Tato metoda se používá pro místní image, ve kterých musí být bitová kopie kódována jako `Stream` a odeslána do Počítačové zpracování obrazu voláním metody `ComputerVisionClient.RecognizePrintedTextInStreamAsync`.
* RecognizeUrlAsync  
  Tato metoda se používá pro vzdálené image, ve které se adresa URL obrázku pošle Počítačové zpracování obrazu zavoláním metody `ComputerVisionClient.RecognizePrintedTextAsync`.

Metoda `UploadAndRecognizeImageAsync` vytvoří novou instanci `ComputerVisionClient` pomocí zadaného klíče předplatného a adresy URL koncového bodu. Vzhledem k tomu, že ukázková aplikace analyzuje místní bitovou kopii, musí odeslat obsah této image do Počítačové zpracování obrazu. Otevře místní soubor zadaný v `imageFilePath` pro čtení jako `Stream`a pak získá jazyk vybraný na stránce scénář. Volá metodu `ComputerVisionClient.RecognizePrintedTextInStreamAsync`, což značí, že se nezjistila orientace a předává `Stream` souboru a jazyku, a pak výsledek vrátí jako instanci `OcrResult`. Metody zděděné z třídy `ImageScenarioPage` prezentují vrácené výsledky na stránce scénáře.

Metoda `RecognizeUrlAsync` vytvoří novou instanci `ComputerVisionClient` pomocí zadaného klíče předplatného a adresy URL koncového bodu. Získá jazyk vybraný na stránce scénář. Volá metodu `ComputerVisionClient.RecognizePrintedTextAsync`, což značí, že se nezjistila orientace a předává se adresa URL obrázku a jazyk, a potom vrátí výsledek jako instanci `OcrResult`. Metody zděděné z třídy `ImageScenarioPage` prezentují vrácené výsledky na stránce scénáře.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Prozkoumat scénář Rozpoznávání textu v2 (anglicky)

Tento scénář je spravován stránkou TextRecognitionPage. XAML. Můžete zvolit režim rozpoznávání a jazyk pro asynchronní rozpoznávání a extrakci vytištěného nebo rukopisného textu z obrázku a zobrazení obrázku i výsledků. Stránka scénář používá následující metody v závislosti na zdroji obrázku:

* UploadAndRecognizeImageAsync  
  Tato metoda se používá pro místní image, ve kterých musí být bitová kopie kódována jako `Stream` a odeslána do Počítačové zpracování obrazu voláním metody `RecognizeAsync` a předáním parametrizovaného delegáta pro metodu `ComputerVisionClient.RecognizeTextInStreamAsync`.
* RecognizeUrlAsync  
  Tato metoda se používá pro vzdálené image, ve kterých se adresa URL obrázku pošle Počítačové zpracování obrazu zavoláním metody `RecognizeAsync` a předáním parametrizovaného delegáta pro metodu `ComputerVisionClient.RecognizeTextAsync`.
* RecognizeAsync Tato metoda zpracovává asynchronní volání metod `UploadAndRecognizeImageAsync` i `RecognizeUrlAsync` a také cyklické dotazování na výsledky voláním metody `ComputerVisionClient.GetTextOperationResultAsync`.

Na rozdíl od ostatních scénářů, které jsou součástí ukázkové aplikace Počítačové zpracování obrazu, je tento scénář asynchronní, v takovém případě je volána jedna metoda pro spuštění procesu, ale je volána jiná metoda, která zkontroluje stav a vrátí výsledky tohoto procesu. Logický tok v tomto scénáři se trochu liší od v ostatních scénářích.

Metoda `UploadAndRecognizeImageAsync` otevře místní soubor určený v `imageFilePath` pro čtení jako `Stream`a pak zavolá metodu `RecognizeAsync` a projde:

* Lambda výraz pro parametrizovaný asynchronní delegát metody `ComputerVisionClient.RecognizeTextInStreamAsync`, s `Stream` pro soubor a režimem rozpoznávání jako parametry v `GetHeadersAsyncFunc`.
* Výraz lambda pro delegáta, který získá hodnotu hlavičky `Operation-Location` Response, v `GetOperationUrlFunc`.

Metoda `RecognizeUrlAsync` volá metodu `RecognizeAsync`, která předává:

* Výraz lambda pro parametrizovaný asynchronní delegát metody `ComputerVisionClient.RecognizeTextAsync`, s adresou URL vzdálené image a režimem rozpoznávání jako parametry v `GetHeadersAsyncFunc`.
* Výraz lambda pro delegáta, který získá hodnotu hlavičky `Operation-Location` Response, v `GetOperationUrlFunc`.

Po dokončení metody `RecognizeAsync` vrátí výsledek jako instanci `TextOperationResult` metody `UploadAndRecognizeImageAsync` a `RecognizeUrlAsync`. Metody zděděné z třídy `ImageScenarioPage` prezentují vrácené výsledky na stránce scénáře.

Metoda `RecognizeAsync` volá parametrizovaný delegát pro metodu `ComputerVisionClient.RecognizeTextInStreamAsync` nebo `ComputerVisionClient.RecognizeTextAsync` předanou `GetHeadersAsyncFunc` a čeká na odpověď. Metoda pak zavolá delegáta předaného `GetOperationUrlFunc` pro získání hodnoty hlavičky `Operation-Location` odpovědi z odpovědi. Tato hodnota je adresa URL, která slouží k načtení výsledků metody předané `GetHeadersAsyncFunc` z Počítačové zpracování obrazu.

Metoda `RecognizeAsync` pak zavolá metodu `ComputerVisionClient.GetTextOperationResultAsync`, která předá adresu URL načtenou z hlavičky odpovědi `Operation-Location`, aby získal stav a výsledek metody předané v `GetHeadersAsyncFunc`. Pokud stav neindikuje, že metoda byla dokončena, úspěšně nebo neúspěšně, `RecognizeAsync` metoda volá `ComputerVisionClient.GetTextOperationResultAsync` 3 vícekrát a čeká 3 sekundy mezi voláními. Metoda `RecognizeAsync` vrátí výsledky do metody, která ji volala.

## <a name="explore-the-get-thumbnail-scenario"></a>Prozkoumat scénář získat miniatury

Tento scénář je spravován stránkou ThumbnailPage. XAML. Můžete určit, zda se má používat Inteligentní oříznutí, určit požadovanou výšku a šířku, vygenerovat miniaturu z obrázku a zobrazit obraz i výsledky. Stránka scénář používá následující metody v závislosti na zdroji obrázku:

* UploadAndThumbnailImageAsync  
  Tato metoda se používá pro místní image, ve kterých musí být bitová kopie kódována jako `Stream` a odeslána do Počítačové zpracování obrazu voláním metody `ComputerVisionClient.GenerateThumbnailInStreamAsync`.
* ThumbnailUrlAsync  
  Tato metoda se používá pro vzdálené image, ve které se adresa URL obrázku pošle Počítačové zpracování obrazu zavoláním metody `ComputerVisionClient.GenerateThumbnailAsync`.

Metoda `UploadAndThumbnailImageAsync` vytvoří novou instanci `ComputerVisionClient` pomocí zadaného klíče předplatného a adresy URL koncového bodu. Vzhledem k tomu, že ukázková aplikace analyzuje místní bitovou kopii, musí odeslat obsah této image do Počítačové zpracování obrazu. Otevře místní soubor zadaný v `imageFilePath` pro čtení jako `Stream`. Volá metodu `ComputerVisionClient.GenerateThumbnailInStreamAsync`, předává šířku, výšku, `Stream` souboru a určuje, jestli se má použít inteligentní ořezávání, a pak výsledek vrátí jako `Stream`. Metody zděděné z třídy `ImageScenarioPage` prezentují vrácené výsledky na stránce scénáře.

Metoda `RecognizeUrlAsync` vytvoří novou instanci `ComputerVisionClient` pomocí zadaného klíče předplatného a adresy URL koncového bodu. Volá metodu `ComputerVisionClient.GenerateThumbnailAsync`, předává šířku, výšku, adresu URL obrázku a určuje, jestli se má použít inteligentní oříznutí, a výsledek vrátí jako `Stream`. Metody zděděné z třídy `ImageScenarioPage` prezentují vrácené výsledky na stránce scénáře.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte složku, do které jste naklonováni `Microsoft/Cognitive-Vision-Windows` úložiště. Pokud jste se rozhodli použít ukázkové image, odstraňte také složku, do které jste naklonováni `Microsoft/Cognitive-Face-Windows` úložiště.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme se službou Face](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
