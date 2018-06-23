---
title: Rozpoznávání emocí úrovně rozhraní API jazyka C# – tutoriál | Microsoft Docs
description: Prozkoumejte základní aplikace pro Windows a používá kognitivní rozpoznávání emocí úrovně rozhraní API služby rozpoznat emoce vyjádřená tyto řezy v obraze.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/23/2017
ms.author: anroth
ms.openlocfilehash: f015e5720f65d0951a77de76ce8882a6dcdc1c3b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342724"
---
# <a name="emotion-api-in-c35-tutorial"></a>Rozpoznávání emocí úrovně rozhraní API v jazyce C&#35; kurzu

> [!IMPORTANT]
> 30. října 2017 ukončen video Preview rozhraní API. Vyzkoušet nový [Preview rozhraní API Indexer Video](https://azure.microsoft.com/services/cognitive-services/video-indexer/) k snadno rozbalte statistiky z videa a vylepšení možnosti zjišťování obsahu, jako je například výsledky hledání, pomocí zjišťování mluvené slovo, řezy, znaků a emoce. [Další informace](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Prozkoumejte základní aplikace Windows, která používá rozhraní API pro rozpoznávání emocí úrovně rozpoznat emoce vyjádřená tyto řezy v obraze. Následující příklad umožňuje odešlete adresu URL obrázku nebo s místně uloženým souborem. Tento příklad s otevřeným zdrojem můžete jako šablonu pro vytvoření vlastní aplikace pro systém Windows s použitím rozhraní API pro rozpoznávání emocí úrovně a WPF (Windows Presentation Foundation), součástí rozhraní .NET Framework.

## <a name="Prerequisites">Požadavky</a>
#### <a name="platform-requirements"></a>Požadavky na platformu  
Následujícím příkladu byla vyvinuta pro rozhraní .NET Framework pomocí [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).  

#### <a name="subscribe-to-emotion-api-and-get-a-subscription-key"></a>Přihlášení k rozhraní API pro rozpoznávání emocí úrovně odběru a získat klíč předplatného  
Před vytvořením v příkladu, se musíte přihlásit k rozpoznávání emocí úrovně rozhraní API, která je součástí kognitivní služeb společnosti Microsoft. V tématu [odběry](https://azure.microsoft.com/try/cognitive-services/). Jak primární a sekundární klíč lze použít v tomto kurzu. Dodržujte doporučené postupy pro zachování váš tajný klíč klíče rozhraní API a zabezpečení.  

#### <a name="get-the-client-library-and-example"></a>Nainstalujte klienta knihovny a příklad  
Můžete ho stáhnout klientské knihovny rozhraní API pro rozpoznávání emocí úrovně prostřednictvím [SDK](https://www.github.com/microsoft/cognitive-emotion-windows). Musí být extrahovány do složky podle svého výběru souboru zip staženého, mnoho uživatelů vyberte složku, Visual Studio 2015.
## <a name="Step1">Krok 1: Otevřete v příkladu</a>
1.  Spusťte Microsoft Visual Studio 2015 a klikněte na **soubor**, vyberte **otevřete**, pak **projekt nebo řešení**.
2.  Přejděte do složky, kam jste uložili stažené soubory rozhraní API pro rozpoznávání emocí úrovně. Klikněte na **rozpoznávání emocí úrovně**, pak **Windows**a potom **ukázka WPF** složky.
3.  Dvojitým kliknutím otevřete soubor Visual Studio 2015 řešení (.sln) s názvem **EmotionAPI. WPF Samples.sln**. Tím otevřete řešení v sadě Visual Studio.

## <a name="Step2">Krok 2: Vytvoření příklad</a>
1. V **Průzkumníku řešení**, klikněte pravým tlačítkem na **odkazy** a vyberte **spravovat balíčky NuGet**.

  ![Správce balíčků Nuget otevřít](../Images/EmotionNuget.png)

2.  **Správce balíčků NuGet** otevře se okno. Nejdřív vyberte **Procházet** v levém horním rohu a pak do pole vyhledávání zadejte "Newtonsoft.Json", vyberte **Newtonsoft.Json** balíček a klikněte na tlačítko **nainstalovat**.  

  ![Přejděte do balíčku NuGet](../Images/EmotionNugetBrowse.png)  

3.  Stiskněte kombinaci kláves Ctrl + Shift + B, nebo klikněte na tlačítko **sestavení** na pásu karet, pak vyberte **sestavit řešení**.

## <a name="Step3">Krok 3: Spuštění ukázkového</a>
1.  Po dokončení sestavení stiskněte **F5** nebo klikněte na tlačítko **spustit** na pásu karet pro spuštění příkladu.
2.  Najděte rozhraní API pro rozpoznávání emocí úrovně okno s **textového pole** čtení "**vložte klíč předplatného zahájíte**". Vložte svůj klíč předplatného do textového pole, jak je znázorněno v následující snímek obrazovky. Můžete zachovat svůj klíč předplatného na svůj počítač nebo přenosný počítač kliknutím na tlačítko "Uložit klíč". Pokud chcete odstranit klíč předplatného ze systému, klikněte na tlačítko "Odstranit klíč" Odebrat z počítače nebo přenosný počítač.
  
  ![Rozhraní pro funkce rozpoznávání emocí úrovně](../Images/EmotionKey.png)

3.  V části "**vyberte scénář**"a použít některou z těchto dvou scénářů, klikněte na tlačítko"**zjistit pomocí datového proudu rozpoznávání emocí úrovně**"nebo"**zjistit rozpoznávání emocí úrovně pomocí adresy URL**", postupujte podle pokynů na obrazovce. Společnost Microsoft obdrží Image nahrát a může je použít ke zlepšení rozpoznávání emocí úrovně rozhraní API a související služby. Odesláním bitovou kopii, potvrďte, že jste provedli naše [vývojáře pravidla chování](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).
4.  Existují třeba bitové kopie pro použití s této ukázkové aplikaci. Tyto Image můžete najít na [úložiště Github rozhraní API vzhled](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) pod **Data** složky. Upozorňujeme, že použití těchto bitových kopií je licencován správného použít smlouvy, což znamená, že jsou lze použít pro testování v tomto příkladu, ale ne pro opětovné publikování.

## <a name="Review">Zkontrolujte a další informace</a>
Teď, když máte spuštěnou aplikaci, zkontrolujte dejte nám, jak tuto aplikaci příklad spolupracuje se službou Microsoft kognitivní Services. To bude usnadňují pokračovat sestavení do této aplikace nebo vývoj vlastní aplikace pomocí rozhraní API pro rozpoznávání emocí úrovně Microsoft. 

Tento příklad aplikace využívá klientské knihovny rozpoznávání emocí úrovně rozhraní API, dynamické C# klienta obálku pro rozhraní API pro rozpoznávání emocí úrovně společnosti Microsoft. Pokud jste vytvořili aplikaci příklad, jak je popsáno výše, jste získali z balíčku NuGet klientské knihovny. Můžete zkontrolovat klientské knihovny zdrojového kódu ve složce s názvem "[klientské knihovny](https://github.com/Microsoft/Cognitive-Emotion-Windows/tree/master/ClientLibrary)" v části **rozpoznávání emocí úrovně**, **Windows**, **klientské knihovny** , který je součástí staženého souboru úložiště zmíněné v [požadavky](#Prerequisites).
 
Můžete taky zjistit používání klientské knihovny kódu v **Průzkumníku řešení**: v části **EmotionAPI WPF_Samples**, rozbalte položku **DetectEmotionUsingStreamPage.xaml** na Vyhledejte **DetectEmotionUsingStreamPage.xaml.cs**, která se používá k procházení k s místně uloženým souborem, nebo rozbalte **DetectEmotionUsingURLPage.xaml** najít  **DetectEmotionUsingURLPage.xaml.cs**, který se používá při nahrávání adresu URL obrázku. Dvakrát klikněte. xaml.cs soubory na jejich otevřete v nových oken v sadě Visual Studio. 

Kontrola, získá použití klientské knihovny rozpoznávání emocí úrovně v našem příkladu aplikaci, podíváme se na dvě fragmenty kódu z **DetectEmotionUsingStreamPage.xaml.cs** a **DetectEmotionUsingURLPage.xaml.cs**. Každý soubor obsahuje komentáře kódu označující "Klíč UKÁZKOVÝ kód SPUSTÍ TADY" a "Klíč ukázka kódu KONČÍ zde" abychom vám pomohli najít kód, který fragmenty uvedeno níže.

Rozhraní API pro rozpoznávání emocí úrovně je možné pracovat s adresa URL obrázku nebo binární bitovou kopii dat (ve formě datového proudu octet) jako vstup. Dvě možnosti jsou kontrolovány níže. V obou případech nejprve zjistíte using – direktiva, což vám umožní používat klientské knihovny rozpoznávání emocí úrovně. 
```csharp

            // ----------------------------------------------------------------------- 
            // KEY SAMPLE CODE STARTS HERE 
            // Use the following namespace for EmotionServiceClient 
            // ----------------------------------------------------------------------- 
            using Microsoft.ProjectOxford.Emotion; 
            using Microsoft.ProjectOxford.Emotion.Contract; 
            // ----------------------------------------------------------------------- 
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------- 
```
#### <a name="detectemotionusingurlpagexamlcs"></a>DetectEmotionUsingURLPage.xaml.cs 

Tento fragment kódu ukazuje, jak pomocí klientské knihovny odeslat svůj klíč předplatného a adresu URL fotografii do rozhraní API pro rozpoznávání emocí úrovně služby. 

```csharp

            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            window.Log("EmotionServiceClient is created");

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                //
                // Detect the emotions in the URL
                //
                Emotion[] emotionResult = await emotionServiceClient.RecognizeAsync(url);
                return emotionResult;
            }
            catch (Exception exception)
            {
                window.Log("Detection failed. Please make sure that you have the right subscription key and proper URL to detect.");
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
#### <a name="detectemotionusingstreampagexamlcs"></a>DetectEmotionUsingStreamPage.xaml.cs 

Vidíte níže je uveden postup odeslat svůj klíč předplatného a místně uložené bitovou kopii do rozhraní API pro rozpoznávání emocí úrovně. 


```csharp


            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                Emotion[] emotionResult;
                using (Stream imageFileStream = File.OpenRead(imageFilePath))
                {
                    //
                    // Detect the emotions in the URL
                    //
                    emotionResult = await emotionServiceClient.RecognizeAsync(imageFileStream);
                    return emotionResult;
                }
            }
            catch (Exception exception)
            {
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
<!--
## <a name="Related">Related Topics</a>
[Emotion API Overview](.)
-->
