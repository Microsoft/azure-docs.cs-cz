---
title: 'Ukázka: zkoumání aplikace pro zpracování obrázků v jazyce C #'
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
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 0a1878268beb135151a23b87d03839f3a51f4a96
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102432583"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>Ukázka: zkoumání aplikace pro zpracování obrázků pomocí jazyka C #

Prozkoumejte základní aplikaci pro Windows, která používá Počítačové zpracování obrazu k provádění optického rozpoznávání znaků (OCR), vytváření miniatur inteligentních oříznutí a navíc k detekci, kategorizaci, označení a popisu vizuálních funkcí, včetně ploch, v obrázku. Následující příklad umožňuje odeslat adresu URL obrázku nebo místně uložený soubor. Tento příklad Open Source můžete použít jako šablonu pro vytvoření vlastní aplikace pro Windows pomocí rozhraní API pro počítačové zpracování obrazu a Windows Presentation Foundation (WPF), což je součást .NET Framework.

> [!div class="checklist"]
> * Získat ukázkovou aplikaci z GitHubu
> * Otevření a sestavení ukázkové aplikace v aplikaci Visual Studio
> * Spusťte ukázkovou aplikaci a pracujte s ní, abyste mohli provádět různé scénáře.
> * Prozkoumejte různé scénáře, které jsou součástí ukázkové aplikace.

## <a name="prerequisites"></a>Předpoklady

Před zkoumáním ukázkové aplikace se ujistěte, že jste splnili následující požadavky:

* Musíte mít [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) nebo novější.
* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" vytvořte prostředek počítačové zpracování obrazu vytvoření prostředku "  target="_blank"> Počítačové zpracování obrazu </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k Počítačové zpracování obrazu službě budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="get-the-sample-app"></a>Získání ukázkové aplikace

Ukázková aplikace Počítačové zpracování obrazu je k dispozici na GitHubu z `Microsoft/Cognitive-Vision-Windows` úložiště. Toto úložiště zahrnuje i `Microsoft/Cognitive-Common-Windows` úložiště jako dílčí modul Git. Toto úložiště, včetně dílčího modulu, můžete rekurzivně klonovat, a to buď pomocí `git clone --recurse-submodules` příkazu z příkazového řádku, nebo pomocí aplikace GitHub Desktop.

Chcete-li například rekurzivně klonovat úložiště pro ukázkovou aplikaci Počítačové zpracování obrazu z příkazového řádku, spusťte následující příkaz:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Toto úložiště nestahujte jako soubor ZIP. Git nezahrnuje dílčí moduly při stahování úložiště jako souboru ZIP.

### <a name="get-optional-sample-images"></a>Získat volitelné ukázkové obrázky

Volitelně můžete použít ukázkové image dodávané s ukázkovou aplikací pro [tvář](../../Face/Overview.md) , které jsou k dispozici na GitHubu z `Microsoft/Cognitive-Face-Windows` úložiště. Tato ukázková aplikace obsahuje složku, `/Data` která obsahuje více imagí lidí. Toto úložiště můžete rekurzivně klonovat i pomocí metod popsaných pro ukázkovou aplikaci Počítačové zpracování obrazu.

Chcete-li například rekurzivně klonovat úložiště pro ukázkovou aplikaci na ploše z příkazového řádku, spusťte následující příkaz:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Otevření a sestavení ukázkové aplikace v aplikaci Visual Studio

Nejprve musíte vytvořit ukázkovou aplikaci, aby mohla aplikace Visual Studio vyřešit závislosti, než můžete spustit nebo prozkoumat ukázkovou aplikaci. Chcete-li otevřít a sestavit ukázkovou aplikaci, proveďte následující kroky:

1. Otevřete soubor řešení sady Visual Studio, `/Sample-WPF/VisionAPI-WPF-Samples.sln` v aplikaci Visual Studio.
1. Ujistěte se, že řešení sady Visual Studio obsahuje dva projekty:  

   * SampleUserControlLibrary
   * VisionAPI-WPF – ukázky  

   Pokud není projekt SampleUserControlLibrary k dispozici, potvrďte, že jste úložiště naklonoval rekurzivně `Microsoft/Cognitive-Vision-Windows` .
1. V aplikaci Visual Studio stiskněte kombinaci kláves CTRL + SHIFT + B nebo zvolte **sestavit** z nabídky pásu karet a pak zvolte **Sestavit řešení** a sestavte řešení.

## <a name="run-and-interact-with-the-sample-app"></a>Spuštění a interakce s ukázkovou aplikací

Ukázkovou aplikaci můžete spustit, abyste viděli, jak komunikuje s vámi a s knihovnou klienta Počítačové zpracování obrazu při provádění různých úloh, jako je například generování miniatur nebo označování obrázků. Pokud chcete spustit ukázkovou aplikaci a pracovat s ní, proveďte následující kroky:

1. Po dokončení sestavení stiskněte klávesu **F5** nebo zvolte možnost **ladění** z nabídky pásu karet a pak zvolte možnost **Spustit ladění** a spusťte ukázkovou aplikaci.
1. Když se zobrazí ukázková aplikace, v navigačním podokně vyberte **Správa klíčů předplatného** . zobrazí se stránka Správa klíčů předplatného.
   ![Stránka správy klíčů předplatného](../Images/Vision_UI_Subscription.PNG)  
1. Do **klíče předplatného** zadejte svůj klíč předplatného.
1. Do **koncového bodu** zadejte adresu URL koncového bodu.  
   [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Pokud nechcete zadat klíč předplatného a adresu URL koncového bodu při příštím spuštění ukázkové aplikace, klikněte na **Uložit nastavení** a uložte klíč předplatného a adresu URL koncového bodu do vašeho počítače. Pokud chcete odstranit dřív uložený klíč předplatného a adresu URL koncového bodu, vyberte **Odstranit nastavení**.

   > [!NOTE]
   > Ukázková aplikace používá izolované úložiště a `System.IO.IsolatedStorage` umožňuje uložit klíč předplatného a adresu URL koncového bodu.

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
   ![Snímek obrazovky se stránkou analyzovat obrázek](../Images/Analyze_Image_Example.PNG)

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
  * ImageScenarioPage. cs  
    Třída ImageScenarioPage, ze které jsou odvozeny všechny stránky scénáře v ukázkové aplikaci. Tato třída spravuje funkce, jako je například poskytování přihlašovacích údajů a formátování výstupu, sdílené všemi stránkami scénáře.
  * MainWindow.xaml  
    Hlavní okno pro ukázkovou aplikaci používá ovládací prvek SampleScenarios, který prezentuje stránky SubscriptionKeyPage a scénářů.
  * OCRPage. XAML  
    Stránka scénáře pro scénář Rozpoznávání textu (OCR).
  * RecognizeLanguage. cs  
    Třída RecognizeLanguage, která poskytuje informace o jazycích podporovaných různými metodami v ukázkové aplikaci.
  * TagsPage. XAML  
    Stránka scénáře pro scénář generovat značky
  * TextRecognitionPage. XAML  
    Stránka scénáře pro scénář Rozpoznávání textu v2 (anglicky)
  * ThumbnailPage. XAML  
    Stránka scénáře pro scénář získat miniaturu

### <a name="explore-the-sample-code"></a>Prozkoumat vzorový kód

Klíčové části vzorového kódu jsou orámovány s bloky komentáře, které začínají `KEY SAMPLE CODE STARTS HERE` a končí `KEY SAMPLE CODE ENDS HERE` , aby bylo snazší prozkoumat ukázkovou aplikaci. Tyto klíčové části ukázkového kódu obsahují kód, který je nejrelevantnější pro učení, jak pomocí klientské knihovny rozhraní API pro počítačové zpracování obrazu provádět různé úlohy. Můžete hledat `KEY SAMPLE CODE STARTS HERE` v aplikaci Visual Studio, abyste se mohli pohybovat mezi nejrelevantnějšími oddíly kódu v ukázkové aplikaci počítačové zpracování obrazu. 

Například `UploadAndAnalyzeImageAsync` metoda, která je znázorněna níže a obsažena v souboru AnalyzePage. XAML, ukazuje, jak použít knihovnu klienta k analýze místní image vyvoláním `ComputerVisionClient.AnalyzeImageInStreamAsync` metody.

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

Tato ukázková aplikace používá klientskou knihovnu rozhraní API pro počítačové zpracování obrazu, což je obálka dynamického klienta C# pro rozhraní API pro počítačové zpracování obrazu v Azure Cognitive Services. Knihovna klienta je dostupná ze sady NuGet v balíčku [Microsoft. Azure. cognitiveservices Account. Vision. ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) . Při sestavování aplikace sady Visual Studio jste načetli knihovnu klienta z odpovídajícího balíčku NuGet. Zdrojový kód klientské knihovny můžete zobrazit také ve `/ClientLibrary` složce `Microsoft/Cognitive-Vision-Windows` úložiště.

Funkce klientské knihovny na střed kolem `ComputerVisionClient` třídy v `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` oboru názvů, zatímco modely používané `ComputerVisionClient` třídou při interakci s počítačové zpracování obrazu se nacházejí v `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` oboru názvů. V různých stránkách scénáře XAML, které jsou součástí ukázkové aplikace, najdete `using` pro tyto obory názvů následující direktivy:

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

Další informace o různých metodách, které jsou součástí třídy, se dozvíte při `ComputerVisionClient` zkoumání scénářů, které jsou součástí počítačové zpracování obrazu ukázkové aplikace.

## <a name="explore-the-analyze-image-scenario"></a>Prozkoumat scénář analýzy obrázku

Tento scénář je spravován stránkou AnalyzePage. XAML. Můžete zvolit vizuální funkce a jazyk pro analýzu a zobrazit obraz i výsledky. Na stránce scénář se používá jedna z následujících metod v závislosti na zdroji obrázku:

* UploadAndAnalyzeImageAsync  
  Tato metoda se používá pro místní image, ve kterých musí být bitová kopie kódována jako `Stream` a odeslána do počítačové zpracování obrazu zavoláním `ComputerVisionClient.AnalyzeImageInStreamAsync` metody.
* AnalyzeUrlAsync  
  Tato metoda se používá pro vzdálené image, ve které se adresa URL obrázku pošle Počítačové zpracování obrazu zavoláním `ComputerVisionClient.AnalyzeImageAsync` metody.

`UploadAndAnalyzeImageAsync`Metoda vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Vzhledem k tomu, že ukázková aplikace analyzuje místní bitovou kopii, musí odeslat obsah této image do Počítačové zpracování obrazu. Otevře místní soubor určený v `imageFilePath` pro čtení jako a `Stream` pak získá vizuální funkce a jazyk vybraný na stránce scénář. Volá `ComputerVisionClient.AnalyzeImageInStreamAsync` metodu, projde `Stream` pro soubor, vizuální funkce a jazyk a vrátí výsledek jako `ImageAnalysis` instanci. Metody zděděné z `ImageScenarioPage` třídy prezentují vrácené výsledky na stránce scénáře.

`AnalyzeUrlAsync`Metoda vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Získá vizuální funkce a jazyk vybraný na stránce scénář. Volá `ComputerVisionClient.AnalyzeImageInStreamAsync` metodu, projde adresu URL obrázku, vizuální funkce a jazyk a potom vrátí výsledek jako `ImageAnalysis` instanci. Metody zděděné z `ImageScenarioPage` třídy prezentují vrácené výsledky na stránce scénáře.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Prozkoumat scénář analyzovat Image pomocí doménového modelu

Tento scénář je spravován stránkou AnalyzeInDomainPage. XAML. Můžete zvolit doménový model, jako je například `celebrities` nebo `landmarks` , a jazyk k provedení analýzy specifické pro doménu a zobrazit obraz i výsledky. Stránka scénář používá následující metody v závislosti na zdroji obrázku:

* GetAvailableDomainModelsAsync  
  Tato metoda získá seznam dostupných doménových modelů z Počítačové zpracování obrazu a naplní `_domainModelComboBox` ovládací prvek ComboBox na stránce pomocí `ComputerVisionClient.ListModelsAsync` metody.
* UploadAndAnalyzeInDomainImageAsync  
  Tato metoda se používá pro místní image, ve kterých musí být bitová kopie kódována jako `Stream` a odeslána do počítačové zpracování obrazu zavoláním `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` metody.
* AnalyzeInDomainUrlAsync  
  Tato metoda se používá pro vzdálené image, ve které se adresa URL obrázku pošle Počítačové zpracování obrazu zavoláním `ComputerVisionClient.AnalyzeImageByDomainAsync` metody.

`UploadAndAnalyzeInDomainImageAsync`Metoda vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Vzhledem k tomu, že ukázková aplikace analyzuje místní bitovou kopii, musí odeslat obsah této image do Počítačové zpracování obrazu. Otevře místní soubor určený v `imageFilePath` pro čtení jako a `Stream` pak získá jazyk vybraný na stránce scénář. Volá `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` metodu, předá `Stream` soubor, název doménového modelu a jazyk a potom vrátí výsledek jako `DomainModelResults` instanci. Metody zděděné z `ImageScenarioPage` třídy prezentují vrácené výsledky na stránce scénáře.

`AnalyzeInDomainUrlAsync`Metoda vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Získá jazyk vybraný na stránce scénář. Volá `ComputerVisionClient.AnalyzeImageByDomainAsync` metodu, projde adresu URL obrázku, vizuální funkce a jazyk a potom vrátí výsledek jako `DomainModelResults` instanci. Metody zděděné z `ImageScenarioPage` třídy prezentují vrácené výsledky na stránce scénáře.

## <a name="explore-the-describe-image-scenario"></a>Prozkoumejte scénář popisující obrázek

Tento scénář je spravován stránkou DescribePage. XAML. Můžete zvolit jazyk pro vytvoření uživatelsky čitelného popisu obrázku a zobrazit obraz i výsledky. Stránka scénář používá následující metody v závislosti na zdroji obrázku:

* UploadAndDescribeImageAsync  
  Tato metoda se používá pro místní image, ve kterých musí být bitová kopie kódována jako `Stream` a odeslána do počítačové zpracování obrazu zavoláním `ComputerVisionClient.DescribeImageInStreamAsync` metody.
* DescribeUrlAsync  
  Tato metoda se používá pro vzdálené image, ve které se adresa URL obrázku pošle Počítačové zpracování obrazu zavoláním `ComputerVisionClient.DescribeImageAsync` metody.

`UploadAndDescribeImageAsync`Metoda vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Vzhledem k tomu, že ukázková aplikace analyzuje místní bitovou kopii, musí odeslat obsah této image do Počítačové zpracování obrazu. Otevře místní soubor určený v `imageFilePath` pro čtení jako a `Stream` pak získá jazyk vybraný na stránce scénář. Volá `ComputerVisionClient.DescribeImageInStreamAsync` metodu, projde `Stream` pro soubor, maximální počet kandidátů (v tomto případě 3) a jazyk a vrátí výsledek jako `ImageDescription` instanci. Metody zděděné z `ImageScenarioPage` třídy prezentují vrácené výsledky na stránce scénáře.

`DescribeUrlAsync`Metoda vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Získá jazyk vybraný na stránce scénář. Volá `ComputerVisionClient.DescribeImageAsync` metodu, projde adresu URL obrázku, maximální počet kandidátů (v tomto případě 3) a jazyk a vrátí výsledek jako `ImageDescription` instanci. Metody zděděné z `ImageScenarioPage` třídy prezentují vrácené výsledky na stránce scénáře.

## <a name="explore-the-generate-tags-scenario"></a>Prozkoumat scénář generovat značky

Tento scénář je spravován stránkou TagsPage. XAML. Můžete zvolit jazyk k označení vizuálních funkcí obrázku a zobrazit obraz i výsledky. Stránka scénář používá následující metody v závislosti na zdroji obrázku:

* UploadAndGetTagsForImageAsync  
  Tato metoda se používá pro místní image, ve kterých musí být bitová kopie kódována jako `Stream` a odeslána do počítačové zpracování obrazu zavoláním `ComputerVisionClient.TagImageInStreamAsync` metody.
* GenerateTagsForUrlAsync  
  Tato metoda se používá pro vzdálené image, ve které se adresa URL obrázku pošle Počítačové zpracování obrazu zavoláním `ComputerVisionClient.TagImageAsync` metody.

`UploadAndGetTagsForImageAsync`Metoda vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Vzhledem k tomu, že ukázková aplikace analyzuje místní bitovou kopii, musí odeslat obsah této image do Počítačové zpracování obrazu. Otevře místní soubor určený v `imageFilePath` pro čtení jako a `Stream` pak získá jazyk vybraný na stránce scénář. Volá `ComputerVisionClient.TagImageInStreamAsync` metodu, předá `Stream` soubor a jazyk a potom vrátí výsledek jako `TagResult` instanci. Metody zděděné z `ImageScenarioPage` třídy prezentují vrácené výsledky na stránce scénáře.

`GenerateTagsForUrlAsync`Metoda vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Získá jazyk vybraný na stránce scénář. Volá `ComputerVisionClient.TagImageAsync` metodu, projde adresu URL obrázku a jazyk a vrátí výsledek jako `TagResult` instanci. Metody zděděné z `ImageScenarioPage` třídy prezentují vrácené výsledky na stránce scénáře.

## <a name="explore-the-recognize-text-ocr-scenario"></a>Prozkoumat scénář Rozpoznávání textu (OCR)

Tento scénář je spravován stránkou OCRPage. XAML. Můžete zvolit jazyk pro rozpoznání a extrakci vytištěného textu z obrázku a zobrazení obrázku i výsledků. Stránka scénář používá následující metody v závislosti na zdroji obrázku:

* UploadAndRecognizeImageAsync  
  Tato metoda se používá pro místní image, ve kterých musí být bitová kopie kódována jako `Stream` a odeslána do počítačové zpracování obrazu zavoláním `ComputerVisionClient.RecognizePrintedTextInStreamAsync` metody.
* RecognizeUrlAsync  
  Tato metoda se používá pro vzdálené image, ve které se adresa URL obrázku pošle Počítačové zpracování obrazu zavoláním `ComputerVisionClient.RecognizePrintedTextAsync` metody.

`UploadAndRecognizeImageAsync`Metoda vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Vzhledem k tomu, že ukázková aplikace analyzuje místní bitovou kopii, musí odeslat obsah této image do Počítačové zpracování obrazu. Otevře místní soubor určený v `imageFilePath` pro čtení jako a `Stream` pak získá jazyk vybraný na stránce scénář. Volá `ComputerVisionClient.RecognizePrintedTextInStreamAsync` metodu, která značí, že orientace není zjištěna a předává `Stream` pro soubor a jazyk, pak vrátí výsledek jako `OcrResult` instanci. Metody zděděné z `ImageScenarioPage` třídy prezentují vrácené výsledky na stránce scénáře.

`RecognizeUrlAsync`Metoda vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Získá jazyk vybraný na stránce scénář. Volá `ComputerVisionClient.RecognizePrintedTextAsync` metodu, která značí, že orientace není zjištěna a předává adresu URL obrázku a jazyk, a vrátí výsledek jako `OcrResult` instanci. Metody zděděné z `ImageScenarioPage` třídy prezentují vrácené výsledky na stránce scénáře.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Prozkoumat scénář Rozpoznávání textu v2 (anglicky)

Tento scénář je spravován stránkou TextRecognitionPage. XAML. Můžete zvolit režim rozpoznávání a jazyk pro asynchronní rozpoznávání a extrakci vytištěného nebo rukopisného textu z obrázku a zobrazení obrázku i výsledků. Stránka scénář používá následující metody v závislosti na zdroji obrázku:

* UploadAndRecognizeImageAsync  
  Tato metoda se používá pro místní image, ve kterých musí být bitová kopie kódována jako `Stream` a odeslána do počítačové zpracování obrazu zavoláním `RecognizeAsync` metody a předáním parametrizovaného delegáta pro `ComputerVisionClient.RecognizeTextInStreamAsync` metodu.
* RecognizeUrlAsync  
  Tato metoda se používá pro vzdálené image, ve které se adresa URL obrázku pošle Počítačové zpracování obrazu zavoláním `RecognizeAsync` metody a předáním parametrizovaného delegáta pro `ComputerVisionClient.RecognizeTextAsync` metodu.
* RecognizeAsync Tato metoda zpracovává asynchronní volání pro `UploadAndRecognizeImageAsync` metody a a `RecognizeUrlAsync` také cyklické dotazování na výsledky voláním `ComputerVisionClient.GetTextOperationResultAsync` metody.

Na rozdíl od ostatních scénářů, které jsou součástí ukázkové aplikace Počítačové zpracování obrazu, je tento scénář asynchronní, v takovém případě je volána jedna metoda pro spuštění procesu, ale je volána jiná metoda, která zkontroluje stav a vrátí výsledky tohoto procesu. Logický tok v tomto scénáři se trochu liší od v ostatních scénářích.

`UploadAndRecognizeImageAsync`Metoda otevře místní soubor určený v `imageFilePath` pro čtení jako a `Stream` pak zavolá `RecognizeAsync` metodu s předáním:

* Výraz lambda pro parametrizovaného asynchronního delegáta `ComputerVisionClient.RecognizeTextInStreamAsync` metody se `Stream` souborem pro soubor a režimem rozpoznávání jako parametry v `GetHeadersAsyncFunc` .
* Výraz lambda pro delegáta pro získání `Operation-Location` hodnoty hlavičky odpovědi v `GetOperationUrlFunc` .

`RecognizeUrlAsync`Metoda volá `RecognizeAsync` metodu a projde:

* Výraz lambda pro parametrizovaný asynchronní delegát `ComputerVisionClient.RecognizeTextAsync` metody s adresou URL vzdálené image a režimem rozpoznávání jako parametry v `GetHeadersAsyncFunc` .
* Výraz lambda pro delegáta pro získání `Operation-Location` hodnoty hlavičky odpovědi v `GetOperationUrlFunc` .

Když `RecognizeAsync` je metoda dokončená, `UploadAndRecognizeImageAsync` metody a `RecognizeUrlAsync` vrátí výsledek jako `TextOperationResult` instanci. Metody zděděné z `ImageScenarioPage` třídy prezentují vrácené výsledky na stránce scénáře.

`RecognizeAsync`Metoda volá parametrizovaného delegáta pro `ComputerVisionClient.RecognizeTextInStreamAsync` `ComputerVisionClient.RecognizeTextAsync` metodu nebo předanou metodou nebo `GetHeadersAsyncFunc` čeká na odpověď. Metoda pak zavolá delegáta předaný za `GetOperationUrlFunc` účelem získání `Operation-Location` hodnoty hlavičky odpovědi z odpovědi. Tato hodnota je adresa URL, která slouží k načtení výsledků metody předané `GetHeadersAsyncFunc` z počítačové zpracování obrazu.

`RecognizeAsync`Metoda pak zavolá `ComputerVisionClient.GetTextOperationResultAsync` metodu, která PŘEDÁ adresu URL načtenou z `Operation-Location` hlavičky odpovědi, aby získal stav a výsledek předané metody `GetHeadersAsyncFunc` . Pokud stav neindikuje, že metoda byla dokončena, úspěšně nebo neúspěšně, volání metody bude opakována třikrát `RecognizeAsync` `ComputerVisionClient.GetTextOperationResultAsync` a bude čekat 3 sekundy mezi voláními. `RecognizeAsync`Metoda vrátí výsledky do metody, která ji zavolala.

## <a name="explore-the-get-thumbnail-scenario"></a>Prozkoumat scénář získat miniatury

Tento scénář je spravován stránkou ThumbnailPage. XAML. Můžete určit, zda se má používat Inteligentní oříznutí, určit požadovanou výšku a šířku, vygenerovat miniaturu z obrázku a zobrazit obraz i výsledky. Stránka scénář používá následující metody v závislosti na zdroji obrázku:

* UploadAndThumbnailImageAsync  
  Tato metoda se používá pro místní image, ve kterých musí být bitová kopie kódována jako `Stream` a odeslána do počítačové zpracování obrazu zavoláním `ComputerVisionClient.GenerateThumbnailInStreamAsync` metody.
* ThumbnailUrlAsync  
  Tato metoda se používá pro vzdálené image, ve které se adresa URL obrázku pošle Počítačové zpracování obrazu zavoláním `ComputerVisionClient.GenerateThumbnailAsync` metody.

`UploadAndThumbnailImageAsync`Metoda vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Vzhledem k tomu, že ukázková aplikace analyzuje místní bitovou kopii, musí odeslat obsah této image do Počítačové zpracování obrazu. Otevře místní soubor určený v `imageFilePath` pro čtení jako `Stream` . Volá `ComputerVisionClient.GenerateThumbnailInStreamAsync` metodu, předává šířku, výšku, `Stream` soubor pro soubor a určuje, zda použít inteligentní oříznutí, a vrátí výsledek jako `Stream` . Metody zděděné z `ImageScenarioPage` třídy prezentují vrácené výsledky na stránce scénáře.

`RecognizeUrlAsync`Metoda vytvoří novou `ComputerVisionClient` instanci pomocí zadaného klíče předplatného a adresy URL koncového bodu. Volá `ComputerVisionClient.GenerateThumbnailAsync` metodu, předává šířku, výšku, adresu URL obrázku a zda má používat Inteligentní oříznutí, a vrátí výsledek jako `Stream` . Metody zděděné z `ImageScenarioPage` třídy prezentují vrácené výsledky na stránce scénáře.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte složku, do které jste naklonováni `Microsoft/Cognitive-Vision-Windows` úložiště. Pokud jste se rozhodli použít ukázkové image, odstraňte také složku, do které jste naklonováni `Microsoft/Cognitive-Face-Windows` úložiště.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme se službou Face](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
