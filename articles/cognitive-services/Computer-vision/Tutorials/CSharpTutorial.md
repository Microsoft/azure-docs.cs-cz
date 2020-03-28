---
title: 'Ukázka: Prozkoumejte aplikaci pro zpracování obrazu v C #'
titleSuffix: Azure Cognitive Services
description: Prozkoumejte základní aplikaci pro Windows, která používá rozhraní API pro počítačové zpracování obrazu ve službách Azure Cognitive Services. Provádějte optické rozpoznávání znaků (OCR), vytvářejte miniatury a pracujte s vizuálními funkcemi obrázku.
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76168881"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>Ukázka: Prozkoumejte aplikaci pro zpracování obrazu pomocí jazyka C #

Prozkoumejte základní aplikaci systému Windows, která používá počítačové vidění k provádění optického rozpoznávání znaků (OCR), vytváření inteligentních oříznutých miniatur a detekci, kategorizaci, označování a popisu vizuálních prvků, včetně tváří, v obraze. Následující příklad umožňuje odeslat adresu URL obrázku nebo místně uložený soubor. Tento příklad open source můžete použít jako šablonu pro vytváření vlastní aplikace pro Windows pomocí rozhraní Api pro počítačové zpracování obrazu a Windows Presentation Foundation (WPF), která je součástí rozhraní .NET Framework.

> [!div class="checklist"]
> * Získání ukázkové aplikace z GitHubu
> * Otevření a sestavení ukázkové aplikace ve Visual Studiu
> * Spuštění ukázkové aplikace a interakce s ní k provedení různých scénářů
> * Prozkoumejte různé scénáře zahrnuté v ukázkové aplikaci

## <a name="prerequisites"></a>Požadavky

Než prozkoumáte ukázkovou aplikaci, ujistěte se, že jste splnili následující požadavky:

* Musíte mít [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) nebo novější.
* Musíte mít klíč předplatného pro počítačové zpracování obrazu. Můžete získat bezplatný zkušební klíč od [try cognitive services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Nebo postupujte podle pokynů v [tématu Vytvoření účtu služeb Cognitive Services,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) abyste se přihlásili k odběru počítačového vidění a získali klíč. Poznamenejte si také adresu URL koncového bodu služby.

## <a name="get-the-sample-app"></a>Získání ukázkové aplikace

Ukázková aplikace Computer Vision je k `Microsoft/Cognitive-Vision-Windows` dispozici na GitHubu z úložiště. Toto úložiště také `Microsoft/Cognitive-Common-Windows` obsahuje úložiště jako podmodul Git. Toto úložiště můžete znovu naklonovat, včetně podmodulu, buď `git clone --recurse-submodules` pomocí příkazu z příkazového řádku, nebo pomocí githubu Desktop.

Chcete-li například rekurzivně klonovat úložiště ukázkové aplikace Computer Vision z příkazového řádku, spusťte následující příkaz:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Nestahujte toto úložiště jako ZIP. Git neobsahuje submoduly při stahování úložiště jako ZIP.

### <a name="get-optional-sample-images"></a>Získání volitelných ukázkových obrázků

Volitelně můžete použít ukázkové obrázky, které jsou součástí ukázkové aplikace [Face,](../../Face/Overview.md) které jsou dostupné na GitHubu `Microsoft/Cognitive-Face-Windows` z úložiště. Tato ukázková aplikace `/Data`obsahuje složku , která obsahuje více obrázků lidí. Můžete rekurzivně klonovat toto úložiště, také pomocí metod popsaných pro ukázkovou aplikaci Computer Vision.

Chcete-li například rekurzivně klonovat úložiště ukázkové aplikace Face z příkazového řádku, spusťte následující příkaz:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Otevření a sestavení ukázkové aplikace ve Visual Studiu

Před spuštěním nebo prozkoumáním ukázkové aplikace je nutné nejprve vytvořit ukázkovou aplikaci, aby visual studio bylo možné vyřešit závislosti. Chcete-li ukázkovou aplikaci otevřít a vytvořit, postupujte takto:

1. Otevřete soubor řešení `/Sample-WPF/VisionAPI-WPF-Samples.sln`Visual Studio , v sadě Visual Studio.
1. Ujistěte se, že řešení Sady Visual Studio obsahuje dva projekty:  

   * SampleUserControlLibrary
   * Vzorky VisionAPI-WPF  

   Pokud sampleUserControlLibrary projekt není k dispozici, potvrďte, že `Microsoft/Cognitive-Vision-Windows` jste rekurzivně klonovat úložiště.
1. V Visual Studiu stiskněte ctrl+shift+b nebo zvolte **Sestavení** z nabídky pásu karet a pak zvolte **Sestavit řešení** pro sestavení řešení.

## <a name="run-and-interact-with-the-sample-app"></a>Spuštění a interakce s ukázkovou aplikací

Ukázkovou aplikaci můžete spustit a zjistit, jak spolupracuje s vámi a s klientskou knihovnou Počítačové vidění při provádění různých úloh, jako je generování miniatur nebo označování obrázků. Chcete-li spustit ukázkovou aplikaci a pracovat s ní, postupujte takto:

1. Po dokončení sestavení stiskněte **klávesu F5** nebo zvolte **Ladění** z nabídky pásu karet a pak zvolte **Spustit ladění** pro spuštění ukázkové aplikace.
1. Po zobrazení ukázkové aplikace zvolte **Správa klíčů předplatného** z navigačního podokna a zobrazte stránku Správa klíčů předplatného.
   ![Stránka Správa klíčů předplatného](../Images/Vision_UI_Subscription.PNG)  
1. Zadejte klíč předplatného v **klíči předplatného**.
1. Zadejte adresu URL koncového bodu do **koncového bodu**.  
   Pokud například používáte klíč předplatného z bezplatné zkušební verze Počítačového vidění, zadejte následující adresu URL koncového bodu:`https://westcentralus.api.cognitive.microsoft.com`  
   [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Pokud nechcete při příštím spuštění ukázkové aplikace zadávat klíč předplatného a adresu URL koncového bodu, zvolte **Uložit nastavení,** chcete-li do počítače uložit klíč předplatného a adresu URL koncového bodu. Pokud chcete odstranit dříve uložený klíč předplatného a adresu URL koncového bodu, zvolte **Odstranit nastavení**.

   > [!NOTE]
   > Ukázková aplikace používá izolované `System.IO.IsolatedStorage`úložiště a , k uložení klíče předplatného a adresy URL koncového bodu.

1. V části **Vybrat scénář** v navigačním podokně vyberte jeden ze scénářů, které jsou aktuálně součástí ukázkové aplikace:  

   | Scénář | Popis |
   |----------|-------------|
   |Analyzovat obrázek | Používá operaci [Analyzovat obraz](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) k analýze místní nebo vzdálené bitové kopie. Můžete zvolit vizuální funkce a jazyk pro analýzu a zobrazit obrázek i výsledky.  |
   |Analyzovat obrázek pomocí modelu domény | Pomocí operace [Seznam modelů specifických pro doménu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) zobrazí seznam modelů domény, ze kterých můžete vybrat, a operaci [Rozpoznat obsah specifický pro doménu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) k analýze místní nebo vzdálené bitové kopie pomocí vybraného modelu domény. Můžete také zvolit jazyk pro analýzu. |
   |Popsat obrázek | Pomocí operace [Popsat obrázek](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) vytvoří popis místní nebo vzdálené bitové kopie čitelný pro člověka. Můžete také zvolit jazyk popisu. |
   |Generovat značky | Používá operaci [Označit obrázek](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) k označení vizuálních prvků místního nebo vzdáleného obrazu. Můžete také zvolit jazyk použitý pro značky. |
   |Rozpoznat text (OCR) | Používá operaci [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) k rozpoznání a extrahování tištěného textu z obrazu. Můžete buď zvolit jazyk, který chcete použít, nebo nechat program Počítačové vidění jazyk automaticky rozpoznat. |
   |Rozpoznat text V2 (anglicky) | Používá [operace Rozpoznat text](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) a [získat rozpoznávání výsledků operace textu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) k asynchronnímu rozpoznání a extrahování tištěného nebo ručně psaného textu z obrazu. |
   |Získat miniaturu | Pomocí operace [Získat miniaturu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) vygeneruje miniaturu pro místní nebo vzdálený obraz. |

   Následující snímek obrazovky ilustruje stránku poskytnutou pro scénář Analyzovat obrázek po analýze ukázkového obrázku.
   ![Snímek obrazovky se stránkou Analyzovat obrázek](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>Prozkoumejte ukázkovou aplikaci

Řešení Visual Studio pro ukázkovou aplikaci Computer Vision obsahuje dva projekty:

* SampleUserControlLibrary  
  Projekt SampleUserControlLibrary poskytuje funkce sdílené více ukázkami služeb Cognitive Services. Projekt obsahuje následující:
  * Ukázkové scénáře  
    UserControl, který poskytuje standardizované prezentace, jako je například záhlaví, navigační podokno a podokno obsahu pro ukázky. Ukázková aplikace Computer Vision používá tento ovládací prvek v okně MainWindow.xaml k zobrazení stránek scénáře a přístupu k informacím sdíleným napříč scénáři, jako je klíč předplatného a adresa URL koncového bodu.
  * SubscriptionKeyPage  
    Stránka, která poskytuje standardizované rozložení pro zadání klíče předplatného a adresy URL koncového bodu pro ukázkovou aplikaci. Ukázková aplikace Computer Vision používá tuto stránku ke správě klíče předplatného a adresy URL koncového bodu používanéstránkami scénáře.
  * VideoResultControl  
    A UserControl, který poskytuje standardizovanou prezentaci pro informace o videu. Ukázková aplikace Computer Vision nepoužívá tento ovládací prvek.
* Vzorky VisionAPI-WPF  
  Hlavní projekt pro ukázkovou aplikaci Computer Vision, tento projekt obsahuje všechny zajímavé funkce pro počítačové vidění. Projekt obsahuje následující:
  * AnalyzovatSoubor XAml aplikace AnalyzeInDomainPage  
    Stránka scénáře pro scénář Analyzovat obraz s modelem domény.
  * AnalyzovatObráz.xaml  
    Stránka scénáře pro scénář Analyzovat obrázek.
  * Soubor DescribePage.xaml  
    Stránka scénáře pro scénář Popsat obrázek.
  * ImageScenarioPage.cs  
    Třída ImageScenarioPage, ze které jsou odvozeny všechny stránky scénáře v ukázkové aplikaci. Tato třída spravuje funkce, jako je například poskytování pověření a formátování výstupu, sdílené všechny stránky scénáře.
  * MainWindow.xaml  
    Hlavní okno pro ukázkovou aplikaci, používá SampleScenarios ovládací prvek k prezentaci SubscriptionKeyPage a scénář stránky.
  * Soubor OCRPage.xaml  
    Stránka scénáře pro scénář Rozpoznat text (OCR).
  * RecognizeLanguage.cs  
    RecognizeLanguage třída, která poskytuje informace o jazyky podporované různými metodami v ukázkové aplikaci.
  * TagsPage.xaml  
    Stránka scénáře pro scénář Generovat značky.
  * TextRecognitionPage.xaml  
    Stránka scénáře pro scénář Rozpoznat text V2 (angličtina).
  * MiniaturaPage.xaml  
    Stránka scénáře pro scénář Získat miniaturu.

### <a name="explore-the-sample-code"></a>Prozkoumejte ukázkový kód

Klíčové části ukázkového kódu jsou orámovány `KEY SAMPLE CODE STARTS HERE` bloky `KEY SAMPLE CODE ENDS HERE`komentářů, které začínají a končí , abyste usnadnili prozkoumání ukázkové aplikace. Tyto klíčové části ukázkového kódu obsahují kód, který je nejdůležitější pro učení, jak používat klientskou knihovnu rozhraní API pro počítačové zpracování obrazu k různým úkolům. Můžete vyhledat `KEY SAMPLE CODE STARTS HERE` v sadě Visual Studio přejít mezi nejrelevantnější části kódu v aplikaci Ukázkové počítačové vidění. 

Například `UploadAndAnalyzeImageAsync` metoda zobrazená následující a zahrnutá v souboru AnalyzePage.xaml ukazuje, jak pomocí klientské `ComputerVisionClient.AnalyzeImageInStreamAsync` knihovny analyzovat místní bitovou kopii vyvoláním metody.

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

### <a name="explore-the-client-library"></a>Prozkoumejte klientskou knihovnu

Tato ukázková aplikace používá klientskou knihovnu rozhraní API pro počítačové zpracování obrazu, což je tenký obálka klienta C# pro rozhraní API pro počítačové zpracování obrazu ve službě Azure Cognitive Services. Klientská knihovna je k dispozici od NuGet v balíčku [Microsoft.Azure.CognitiveServices.Vision.ComputerVision.](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) Při návrhu aplikace Visual Studio jste načetli klientskou knihovnu z odpovídajícího balíčku NuGet. Můžete také zobrazit zdrojový kód klientské `/ClientLibrary` knihovny `Microsoft/Cognitive-Vision-Windows` ve složce úložiště.

Funkce klientské knihovny se `ComputerVisionClient` soustředí kolem `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` třídy v oboru názvů, `ComputerVisionClient` zatímco modely používané třídou při `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` interakci s programem Computer Vision se nacházejí v oboru názvů. V různých stránkách scénáře XAML, které jsou součástí ukázkové aplikace, najdete následující `using` direktivy pro tyto obory názvů:

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

Další informace o různých metodách, `ComputerVisionClient` které jsou součástí třídy, se dozvíte při zkoumání scénářů zahrnutých v ukázkové aplikaci Computer Vision.

## <a name="explore-the-analyze-image-scenario"></a>Prozkoumejte scénář Analyzovat obrázek

Tento scénář je spravován stránkou AnalyzePage.xaml. Můžete zvolit vizuální funkce a jazyk pro analýzu a zobrazit obrázek i výsledky. Stránka scénáře to provádí pomocí jedné z následujících metod v závislosti na zdroji obrázku:

* UploadAndAnalyzeImageAsync  
  Tato metoda se používá pro místní obrázky, ve kterých musí být `Stream` obrázek `ComputerVisionClient.AnalyzeImageInStreamAsync` zakódován jako a odeslán do počítačového vidění voláním metody.
* AnalyzovatUrlAsync  
  Tato metoda se používá pro vzdálené obrázky, ve kterém je adresa `ComputerVisionClient.AnalyzeImageAsync` URL obrázku odeslána do počítačového vidění voláním metody.

Metoda `UploadAndAnalyzeImageAsync` vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Vzhledem k tomu, že ukázková aplikace analyzuje místní bitovou kopii, musí odeslat obsah tohoto obrázku počítačovému vidění. Otevře místní soubor zadaný v `imageFilePath` `Stream`pro čtení jako , pak získá vizuální funkce a jazyk vybraný na stránce scénáře. Volá metodu, `ComputerVisionClient.AnalyzeImageInStreamAsync` předávání `Stream` pro soubor, vizuální funkce a jazyk, pak vrátí `ImageAnalysis` výsledek jako instance. Metody zděděné `ImageScenarioPage` z třídy představují vrácené výsledky na stránce scénáře.

Metoda `AnalyzeUrlAsync` vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Získá vizuální funkce a jazyk vybraný na stránce scénáře. Volá metodu, `ComputerVisionClient.AnalyzeImageInStreamAsync` předávání adresy URL obrázku, vizuální funkce a jazyk, `ImageAnalysis` pak vrátí výsledek jako instance. Metody zděděné `ImageScenarioPage` z třídy představují vrácené výsledky na stránce scénáře.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Prozkoumejte scénář Analyzovat obrázek pomocí modelu domény

Tento scénář je spravován stránkou AnalyzeInDomainPage.xaml. Můžete zvolit model domény, `celebrities` například nebo `landmarks`, a jazyk pro provedení analýzy obrazu specifické pro doménu a zobrazení obrázku i výsledků. Stránka scénáře používá následující metody v závislosti na zdroji obrázku:

* GetAvailableDomainModelsAsync  
  Tato metoda získá seznam dostupných modelů domény z `_domainModelComboBox` počítačového vidění a naplní `ComputerVisionClient.ListModelsAsync` Ovládací prvek ComboBox na stránce pomocí metody.
* UploadAndAnalyzeInDomainImageAsync  
  Tato metoda se používá pro místní obrázky, ve kterých musí být `Stream` obrázek `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` zakódován jako a odeslán do počítačového vidění voláním metody.
* AnalyzovatInDomainUrlAsync  
  Tato metoda se používá pro vzdálené obrázky, ve kterém je adresa `ComputerVisionClient.AnalyzeImageByDomainAsync` URL obrázku odeslána do počítačového vidění voláním metody.

Metoda `UploadAndAnalyzeInDomainImageAsync` vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Vzhledem k tomu, že ukázková aplikace analyzuje místní bitovou kopii, musí odeslat obsah tohoto obrázku počítačovému vidění. Otevře místní soubor zadaný v `imageFilePath` `Stream`pro čtení jako , pak získá jazyk vybraný na stránce scénáře. Volá metodu, `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` předávání `Stream` pro soubor, název modelu domény a jazyk, pak vrátí `DomainModelResults` výsledek jako instance. Metody zděděné `ImageScenarioPage` z třídy představují vrácené výsledky na stránce scénáře.

Metoda `AnalyzeInDomainUrlAsync` vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Získá jazyk vybraný na stránce scénáře. Volá metodu, `ComputerVisionClient.AnalyzeImageByDomainAsync` předávání adresy URL obrázku, vizuální funkce a jazyk, `DomainModelResults` pak vrátí výsledek jako instance. Metody zděděné `ImageScenarioPage` z třídy představují vrácené výsledky na stránce scénáře.

## <a name="explore-the-describe-image-scenario"></a>Prozkoumejte scénář Popsat obrázek

Tento scénář je spravován stránkou DescribePage.xaml. Můžete zvolit jazyk pro vytvoření popisu obrázku čitelného pro člověka a zobrazit obrázek i výsledky. Stránka scénáře používá následující metody v závislosti na zdroji obrázku:

* UploadAndDescribeImageAsync  
  Tato metoda se používá pro místní obrázky, ve kterých musí být `Stream` obrázek `ComputerVisionClient.DescribeImageInStreamAsync` zakódován jako a odeslán do počítačového vidění voláním metody.
* PopisovatUrlAsync  
  Tato metoda se používá pro vzdálené obrázky, ve kterém je adresa `ComputerVisionClient.DescribeImageAsync` URL obrázku odeslána do počítačového vidění voláním metody.

Metoda `UploadAndDescribeImageAsync` vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Vzhledem k tomu, že ukázková aplikace analyzuje místní bitovou kopii, musí odeslat obsah tohoto obrázku počítačovému vidění. Otevře místní soubor zadaný v `imageFilePath` `Stream`pro čtení jako , pak získá jazyk vybraný na stránce scénáře. Volá metodu, `ComputerVisionClient.DescribeImageInStreamAsync` předávání `Stream` pro soubor, maximální počet kandidátů (v tomto případě 3) a jazyk, `ImageDescription` pak vrátí výsledek jako instance. Metody zděděné `ImageScenarioPage` z třídy představují vrácené výsledky na stránce scénáře.

Metoda `DescribeUrlAsync` vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Získá jazyk vybraný na stránce scénáře. Volá metodu, `ComputerVisionClient.DescribeImageAsync` předávání adresu URL obrázku, maximální počet kandidátů (v tomto případě 3) `ImageDescription` a jazyk, pak vrátí výsledek jako instance. Metody zděděné `ImageScenarioPage` z třídy představují vrácené výsledky na stránce scénáře.

## <a name="explore-the-generate-tags-scenario"></a>Prozkoumejte scénář Generovat značky

Tento scénář je spravován stránkou TagsPage.xaml. Můžete zvolit jazyk pro označení vizuálních prvků obrazu a zobrazit obrázek i výsledky. Stránka scénáře používá následující metody v závislosti na zdroji obrázku:

* UploadAndGetTagyForImageAsync  
  Tato metoda se používá pro místní obrázky, ve kterých musí být `Stream` obrázek `ComputerVisionClient.TagImageInStreamAsync` zakódován jako a odeslán do počítačového vidění voláním metody.
* Generovat tagyForurlAsync  
  Tato metoda se používá pro vzdálené obrázky, ve kterém je adresa `ComputerVisionClient.TagImageAsync` URL obrázku odeslána do počítačového vidění voláním metody.

Metoda `UploadAndGetTagsForImageAsync` vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Vzhledem k tomu, že ukázková aplikace analyzuje místní bitovou kopii, musí odeslat obsah tohoto obrázku počítačovému vidění. Otevře místní soubor zadaný v `imageFilePath` `Stream`pro čtení jako , pak získá jazyk vybraný na stránce scénáře. Volá metodu, `ComputerVisionClient.TagImageInStreamAsync` předávání `Stream` pro soubor a jazyk, pak vrátí `TagResult` výsledek jako instance. Metody zděděné `ImageScenarioPage` z třídy představují vrácené výsledky na stránce scénáře.

Metoda `GenerateTagsForUrlAsync` vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Získá jazyk vybraný na stránce scénáře. Volá metodu, `ComputerVisionClient.TagImageAsync` předávání url obrázku a jazyk, pak `TagResult` vrátí výsledek jako instance. Metody zděděné `ImageScenarioPage` z třídy představují vrácené výsledky na stránce scénáře.

## <a name="explore-the-recognize-text-ocr-scenario"></a>Prozkoumejte scénář Rozpoznat text (OCR)

Tento scénář je spravován stránkou OCRPage.xaml. Můžete zvolit jazyk, který rozpoznává a extrahuje tištěný text z obrazu, a zobrazit obraz i výsledky. Stránka scénáře používá následující metody v závislosti na zdroji obrázku:

* UploadAndRecognizeImageAsync  
  Tato metoda se používá pro místní obrázky, ve kterých musí být `Stream` obrázek `ComputerVisionClient.RecognizePrintedTextInStreamAsync` zakódován jako a odeslán do počítačového vidění voláním metody.
* RozpoznatUrlAsync  
  Tato metoda se používá pro vzdálené obrázky, ve kterém je adresa `ComputerVisionClient.RecognizePrintedTextAsync` URL obrázku odeslána do počítačového vidění voláním metody.

Metoda `UploadAndRecognizeImageAsync` vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Vzhledem k tomu, že ukázková aplikace analyzuje místní bitovou kopii, musí odeslat obsah tohoto obrázku počítačovému vidění. Otevře místní soubor zadaný v `imageFilePath` `Stream`pro čtení jako , pak získá jazyk vybraný na stránce scénáře. Volá metodu, `ComputerVisionClient.RecognizePrintedTextInStreamAsync` označující, že orientace není `Stream` zjištěna a předání pro soubor a `OcrResult` jazyk, pak vrátí výsledek jako instance. Metody zděděné `ImageScenarioPage` z třídy představují vrácené výsledky na stránce scénáře.

Metoda `RecognizeUrlAsync` vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Získá jazyk vybraný na stránce scénáře. Volá metodu, `ComputerVisionClient.RecognizePrintedTextAsync` označující, že orientace není zjištěna a předání adresy URL `OcrResult` obrázku a jazyka, pak vrátí výsledek jako instance. Metody zděděné `ImageScenarioPage` z třídy představují vrácené výsledky na stránce scénáře.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Prozkoumejte scénář Rozpoznat text V2 (angličtina)

Tento scénář je spravován textrecognitionpage.xaml stránky. Můžete zvolit režim rozpoznávání a jazyk, aby asynchronně rozpoznala a extrahovala tištěný nebo ručně psaný text z obrazu a viděla obrázek i výsledky. Stránka scénáře používá následující metody v závislosti na zdroji obrázku:

* UploadAndRecognizeImageAsync  
  Tato metoda se používá pro místní bitové kopie, ve `Stream` kterém musí být obrázek zakódován jako a odeslán do počítačového vidění voláním `RecognizeAsync` metody a předáním parametrizovaného delegáta pro metodu. `ComputerVisionClient.RecognizeTextInStreamAsync`
* RozpoznatUrlAsync  
  Tato metoda se používá pro vzdálené obrázky, ve kterých je `RecognizeAsync` adresa URL obrázku odeslána `ComputerVisionClient.RecognizeTextAsync` do počítačového vidění voláním metody a předáním parametrizovaného delegáta pro metodu.
* RecognizeAsync Tato metoda zpracovává asynchronní volání `UploadAndRecognizeImageAsync` pro `RecognizeUrlAsync` metody a, stejně jako `ComputerVisionClient.GetTextOperationResultAsync` dotazování pro výsledky voláním metody.

Na rozdíl od jiných scénářů zahrnutých v ukázkové aplikaci Computer Vision je tento scénář asynchronní, protože jedna metoda je volána ke spuštění procesu, ale je volána jiná metoda ke kontrole stavu a vrácení výsledků tohoto procesu. Logický tok v tomto scénáři se poněkud liší od v jiných scénářích.

Metoda `UploadAndRecognizeImageAsync` otevře místní soubor `imageFilePath` zadaný v `Stream`pro čtení `RecognizeAsync` jako , pak volá metodu, předávání:

* Výraz lambda pro parametrizovaného asynchronního `ComputerVisionClient.RecognizeTextInStreamAsync` delegáta `Stream` metody s pro soubor a režim `GetHeadersAsyncFunc`rozpoznávání jako parametry v .
* Výraz lambda pro delegáta `Operation-Location` získat hodnotu `GetOperationUrlFunc`hlavičky odpovědi v .

Metoda `RecognizeUrlAsync` volá `RecognizeAsync` metodu, předávání:

* Výraz lambda pro parametrizovaného asynchronního `ComputerVisionClient.RecognizeTextAsync` delegáta metody s adresou URL vzdáleného obrázku `GetHeadersAsyncFunc`a režimem rozpoznávání jako parametry v .
* Výraz lambda pro delegáta `Operation-Location` získat hodnotu `GetOperationUrlFunc`hlavičky odpovědi v .

Po `RecognizeAsync` dokončení metody obě `UploadAndRecognizeImageAsync` `RecognizeUrlAsync` metody a metody `TextOperationResult` vrátit výsledek jako instance. Metody zděděné `ImageScenarioPage` z třídy představují vrácené výsledky na stránce scénáře.

Metoda `RecognizeAsync` volá parametrizovaný delegát a `ComputerVisionClient.RecognizeTextInStreamAsync` `ComputerVisionClient.RecognizeTextAsync` předávané metody nebo `GetHeadersAsyncFunc` a čeká na odpověď. Metoda pak volá delegáta `GetOperationUrlFunc` předán `Operation-Location` získat hodnotu hlavičky odpovědi z odpovědi. Tato hodnota je adresa URL použitá k `GetHeadersAsyncFunc` načtení výsledků metody předané z počítačového vidění.

Metoda `RecognizeAsync` pak volá `ComputerVisionClient.GetTextOperationResultAsync` metodu, předání adresy `Operation-Location` URL načtené z hlavičky odpovědi, chcete-li získat stav a výsledek metody předané v `GetHeadersAsyncFunc`. Pokud stav neznamená, že metoda byla dokončena, úspěšně `RecognizeAsync` nebo `ComputerVisionClient.GetTextOperationResultAsync` neúspěšně, metoda volá ještě 3 krát a mezi voláními čeká 3 sekundy. Metoda `RecognizeAsync` vrátí výsledky metodě, která ji volala.

## <a name="explore-the-get-thumbnail-scenario"></a>Prozkoumejte scénář Získat miniaturu

Tento scénář je spravován stránkou ThumbnailPage.xaml. Můžete určit, zda se má použít inteligentní oříznutí, a určit požadovanou výšku a šířku, chcete-li z obrazu vygenerovat miniaturu a zobrazit obrázek i výsledky. Stránka scénáře používá následující metody v závislosti na zdroji obrázku:

* UploadAndThumbnailImageAsync  
  Tato metoda se používá pro místní obrázky, ve kterých musí být `Stream` obrázek `ComputerVisionClient.GenerateThumbnailInStreamAsync` zakódován jako a odeslán do počítačového vidění voláním metody.
* ThumbnailUrlAsync  
  Tato metoda se používá pro vzdálené obrázky, ve kterém je adresa `ComputerVisionClient.GenerateThumbnailAsync` URL obrázku odeslána do počítačového vidění voláním metody.

Metoda `UploadAndThumbnailImageAsync` vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Vzhledem k tomu, že ukázková aplikace analyzuje místní bitovou kopii, musí odeslat obsah tohoto obrázku počítačovému vidění. Otevře místní soubor zadaný v `imageFilePath` `Stream`pro čtení jako . Volá metodu, `ComputerVisionClient.GenerateThumbnailInStreamAsync` předávání šířku, `Stream` výšku, pro soubor a zda použít inteligentní oříznutí, `Stream`pak vrátí výsledek jako . Metody zděděné `ImageScenarioPage` z třídy představují vrácené výsledky na stránce scénáře.

Metoda `RecognizeUrlAsync` vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Volá metodu, `ComputerVisionClient.GenerateThumbnailAsync` předávání šířku, výšku, URL pro obrázek a zda použít inteligentní oříznutí, pak vrátí výsledek jako `Stream`. Metody zděděné `ImageScenarioPage` z třídy představují vrácené výsledky na stránce scénáře.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, odstraňte složku, `Microsoft/Cognitive-Vision-Windows` do které jste naklonovali úložiště. Pokud jste se rozhodli použít ukázkové obrázky, odstraňte `Microsoft/Cognitive-Face-Windows` také složku, do které jste naklonovali úložiště.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme se službou Face](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
