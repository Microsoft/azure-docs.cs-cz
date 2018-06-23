---
title: Počítač vize rozhraní API jazyka C# – tutoriál | Microsoft Docs
description: Prozkoumejte základní aplikace pro Windows a používá rozhraní API vize počítače v kognitivní služby společnosti Microsoft. Provést rozpoznávání znaků, vytváření miniatur a pracovat s visual funkce v obraze.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 05/22/2017
ms.author: kefre
ms.openlocfilehash: f2aeb1734f8858ed8b80e5cdf48ef7e558703919
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342868"
---
# <a name="computer-vision-api-c35-tutorial"></a>Počítač vize rozhraní API C&#35; kurzu

Prozkoumejte základní aplikace Windows, která používá rozhraní API vize počítači provádět optické rozpoznávání znaků (rozpoznávání znaků), vytváření miniatur oříznout čipové plus zjišťovat, kategorií, značky a popisují visual funkcí, včetně řezy, v bitovou kopii. Následující příklad umožňuje odešlete adresu URL obrázku nebo s místně uloženým souborem. Tento příklad s otevřeným zdrojem můžete jako šablonu pro vytvoření vlastní aplikace pro systém Windows s použitím rozhraní API pro vize a WPF (Windows Presentation Foundation), součástí rozhraní .NET Framework.

### <a name="prerequisites"></a>Požadavky

#### <a name="platform-requirements"></a>Požadavky na platformu

Následujícím příkladu byla vyvinuta pro rozhraní .NET Framework pomocí [Visual Studio 2015, Community Edition](https://www.visualstudio.com/downloads/).

#### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Přihlášení k počítači vize API odběru a získat klíč předplatného 

Před vytvořením v příkladu, se musíte přihlásit k rozhraní API vize počítače, který je součástí služby kognitivní Microsoft (dříve Oxford projektu). Předplatné a správu klíčů podrobnosti najdete v tématu [odběry](https://azure.microsoft.com/try/cognitive-services/). Jak primární a sekundární klíč lze použít v tomto kurzu. 

> [!NOTE]
> Tento kurz je určen předplatné klíče v **westcentralus** oblast. Předplatné klíčů vygenerovaných používá volné záznam pro počítač vize **westcentralus** oblast, takže nebudou fungovat správně. Pokud jste vygenerovali vaše předplatné klíče pomocí účtu Azure prostřednictvím [ https://azure.microsoft.com/ ](https://azure.microsoft.com/), je nutné zadat **westcentralus** oblast. Klíče generované mimo **westcentralus** oblast nebude fungovat.

#### <a name="get-the-client-library-and-example"></a>Nainstalujte klienta knihovny a příklad

Může klonovat API vize počítači klienta knihovny a příklad aplikace do počítače prostřednictvím [SDK](https://www.github.com/microsoft/cognitive-vision-windows). Nemáte stáhnout jako ZIP.

### <a name="Step1">Krok 1: Instalace v příkladu</a>

V Githubu ploše otevřete ukázka WPF\VisionAPI WPF Samples.sln.

### <a name="Step2">Krok 2: Vytvoření příklad</a>

* Stiskněte kombinaci kláves Ctrl + Shift + B, nebo klikněte na pásu karet na sestavení a potom vyberte sestavit řešení.

### <a name="Step3">Krok 3: Spuštění ukázkového</a>

1. Po dokončení sestavení stiskněte **F5** nebo klikněte na tlačítko **spustit** na pásu karet pro spuštění příkladu.
2. Vyhledejte okna rozhraní API vize počítače uživatele s pole pro úpravy textu čtení "Vložit klíč předplatného spustit".
Můžete zachovat svůj klíč předplatného na svůj počítač nebo přenosný počítač kliknutím na tlačítko "Uložit klíč". Pokud chcete odstranit klíč předplatného ze systému, klikněte na tlačítko "Odstranit klíč" Odebrat z počítače nebo přenosný počítač.

    ![Klíč předplatného vize](../Images/Vision_UI_Subscription.PNG)

3. V části "Vyberte scénář" klikněte na tlačítko použít jednu z šesti scénáře a potom postupujte podle pokynů na obrazovce. Společnost Microsoft obdrží Image nahrát a může je použít ke zlepšení počítače vize API a související služby. Odesláním bitovou kopii, potvrďte, že jste provedli naše [vývojáře pravidla chování](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).

    ![Analýza rozhraní bitové kopie](../Images/Analyze_Image_Example.PNG)

4. Existují třeba bitové kopie pro použití s této ukázkové aplikaci. Tyto Image můžete najít na úložiště Github pro Windows vzhled rozhraní API, v [složky dat](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data). Poznámka: použití těchto bitových kopií je licencováno v rámci smlouvy [licence IMAGE](https://github.com/Microsoft/Cognitive-Face-Windows/blob/master/LICENSE-IMAGE.md).

### <a name="Review">Zkontrolujte a další informace</a>

Teď, když máte spuštěnou aplikaci, zkontrolujte dejte nám, jak tento příklad aplikace integruje s technologií kognitivní služby. To bude usnadňují pokračovat sestavení do této aplikace nebo vývoj vlastní aplikace pomocí rozhraní API služby Microsoft počítače vize.

Tento příklad aplikace využívá rozhraní API klientské počítače vize knihovny, dynamické C# klienta obálka pro rozhraní API služby Microsoft počítače vize. Pokud jste vytvořili aplikaci příklad, jak je popsáno výše, jste získali z balíčku NuGet klientské knihovny. Můžete zkontrolovat klientské knihovny zdrojového kódu ve složce s názvem "**klientské knihovny**" v části **vize**, **Windows**, **klientské knihovny**, který je součástí staženého souboru úložiště výše uvedené požadavky.

Můžete taky zjistit kód Klientská knihovna pro použití v Průzkumníku řešení: v části **VisionAPI WPF_Samples**, rozbalte položku **AnalyzePage.xaml** najít **AnalyzePage.xaml.cs**, který se používá pro odesílání bitovou kopii na koncový bod analysis bitové kopie. Dvakrát klikněte. xaml.cs soubory na jejich otevřete v nových oken v sadě Visual Studio.

Kontrola, jak získá vize klientské knihovny používají v našem příkladu aplikaci, podíváme se na dvě fragmenty kódu z **AnalyzePage.xaml.cs**. Tento soubor obsahuje komentáře kódu označující "Klíč UKÁZKOVÝ kód SPUSTÍ TADY" a "Klíč ukázka kódu KONČÍ zde" abychom vám pomohli najít kód, který fragmenty uvedeno níže.

Analýza koncový bod je možné pracovat s adresa URL obrázku nebo binární bitovou kopii dat (ve formě datového proudu octet) jako vstup. Nejdříve vyhledejte using – direktiva, což vám umožní používat vize klientské knihovny.

```
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE STARTS HERE
                // Use the following namespace for VisionServiceClient 
                // ---------------------------------------------------------------------- 
                using Microsoft.ProjectOxford.Vision; 
                using Microsoft.ProjectOxford.Vision.Contract; 
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE ENDS HERE 
                // ----------------------------------------------------------------------

```
**UploadAndAnalyzeImage(...)**  Tento fragment kódu ukazuje, jak odeslat svůj klíč předplatného a místně uložené bitovou kopii do analyzovat koncový bod služby API vize počítače pomocí klientské knihovny.

```
    private async Task<AnalysisResult> UploadAndAnalyzeImage(string imageFilePath)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------  
        //
        // Create Project Oxford Computer Vision API Service client
        //
        VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features
            //
            Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
         VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
            AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageFileStream, visualFeatures);
            return analysisResult;
        }
    
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
        }
```
**AnalyzeUrl(...)**  Tento fragment kódu ukazuje, jak odeslat svůj klíč předplatného a adresu URL fotografie analyzovat koncový bod služby API vize počítače pomocí klientské knihovny.

```
    private async Task<AnalysisResult> AnalyzeUrl(string imageUrl)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------
    
        //
        // Create Project Oxford Computer Vision API Service client
        //
     VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        //
        // Analyze the url for all visual features
        //
        Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
        VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
        AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageUrl, visualFeatures);
     return analysisResult;
    }
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
```
**Další stránky a koncové body** návod, jak pracovat s další koncové body vystavené služby API vize počítači můžete zobrazit podle dalších stránek v ukázce; pro instanci rozpoznávání znaků koncový bod se zobrazí jako součást kód obsažené v OCRPage.xaml.cs 

### <a name="Related">Související témata</a>
 * [Začínáme s rozhraním API vzhled](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
 
 


