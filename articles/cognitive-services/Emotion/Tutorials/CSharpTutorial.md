---
title: 'Kurz: Rozpoznávání emocí ve výrazech tváří na obrázku – rozhraní API pro rozpoznávání emocí, C#'
titlesuffix: Azure Cognitive Services
description: Prozkoumejte základní aplikaci pro Windows umožňující rozpoznávání emocí ve výrazech tváří na obrázku.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: tutorial
ms.date: 01/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: f3a84a68718fba29e2a4b2fae057e68976119c95
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237020"
---
# <a name="tutorial-recognize-emotions-on-a-face-in-an-image"></a>Kurz: Rozpoznávání emocí ve výrazech tváří na obrázku

> [!IMPORTANT]
> Rozhraní API pro rozpoznávání emocí se přestane používat 15. února 2019. Funkce rozpoznávání emocí je teď obecně dostupná v rámci [rozhraní API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/). 

Prozkoumejte základní aplikaci pro Windows, která používá rozhraní API pro rozpoznávání emocí k rozpoznávání emocí ve výrazech tváří na obrázku. Následující příklad umožňuje odeslat adresu URL obrázku nebo místně uložený soubor. Tento open source příklad můžete použít jako šablonu pro vytvoření vlastní aplikace pro Windows s využitím rozhraní API pro rozpoznávání emocí a grafického subsystému WPF (Windows Presentation Foundation), který je součástí rozhraní .NET Framework.

## <a name="Prerequisites">Požadavky</a>
#### <a name="platform-requirements"></a>Požadavky platformy  
Následující příklad jsme vyvinuli pro .NET Framework pomocí sady [Visual Studio 2015 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).  

#### <a name="subscribe-to-emotion-api-and-get-a-subscription-key"></a>Přihlášení k odběru rozhraní API pro rozpoznávání emocí a získání klíče předplatného  
Před vytvořením příkladu musíte získat předplatné rozhraní API pro rozpoznávání emocí, které je součástí služeb Microsoft Cognitive Services. Přečtěte si téma [Předplatná](https://azure.microsoft.com/try/cognitive-services/). V tomto kurzu můžete použít primární i sekundární klíč. Nezapomeňte dodržovat osvědčené postupy pro zajištění utajení a zabezpečení vašeho klíče rozhraní API.  

#### <a name="get-the-client-library-and-example"></a>Získání klientské knihovny a příkladu  
Klientskou knihovnu rozhraní API pro rozpoznávání emocí můžete stáhnout prostřednictvím sady [SDK](https://www.github.com/microsoft/cognitive-emotion-windows). Stažený soubor ZIP je potřeba extrahovat do složky podle vašeho výběru, velká část uživatelů volí složku sady Visual Studio 2015.
## <a name="Step1">Krok 1: Otevření příkladu</a>
1.  Spusťte sadu Microsoft Visual Studio 2015, klikněte na **Soubor**, vyberte **Otevřít** a pak **Projekt nebo řešení**.
2.  Přejděte do složky, kam jste uložili stažené soubory rozhraní API pro rozpoznávání emocí. Klikněte na **Emotion**, pak na **Windows** a nakonec na složku **Sample-WPF**.
3.  Dvojím kliknutím otevřete soubor řešení sady Visual Studio 2015 (.sln) s názvem **EmotionAPI-WPF-Samples.sln**. Tím se řešení otevře v sadě Visual Studio.

## <a name="Step2">Krok 2: Sestavení příkladu</a>
1. V **Průzkumníku řešení** klikněte pravým tlačítkem na **Odkazy** a vyberte **Spravovat balíčky NuGet**.

  ![Otevření Správce balíčků NuGet](../Images/EmotionNuget.png)

2.  Otevře se okno **Správce balíčků NuGet**. Nejprve v levém horním rohu vyberte **Procházet**, pak do vyhledávacího pole zadejte Newtonsoft.Json, vyberte balíček **Newtonsoft.Json** a klikněte na **Nainstalovat**.  

  ![Přechod k balíčku NuGet](../Images/EmotionNugetBrowse.png)  

3.  Stiskněte Ctrl+Shift+B nebo klikněte na **Sestavení** na pásu karet a pak vyberte **Sestavit řešení**.

## <a name="Step3">Krok 3: Spuštění příkladu</a>
1.  Po dokončení sestavení příklad spusťte stisknutím klávesy **F5** nebo kliknutím na **Spustit** na pásu karet.
2.  Vyhledejte okno rozhraní API pro rozpoznávání emocí s **textovým polem** s textem „**Paste your subscription key here to start**“ (Začněte tím, že sem vložíte svůj klíč předplatného). Vložte do textového pole svůj klíč předplatného, jak je znázorněno na následujícím snímku obrazovky. Klíč předplatného můžete uložit na svém stolním nebo přenosném počítači, a to kliknutím na tlačítko Save Key (Uložit klíč). Pokud budete chtít odstranit klíč předplatného ze systému svého stolního nebo přenosného počítače, provedete to kliknutím na Delete Key (Odstranit klíč).

  ![Rozhraní funkcí Rozpoznávání emocí](../Images/EmotionKey.png)

3.  V části **Select Scenario** (Výběr scénáře) klikněte na jeden ze dvou scénářů, který chcete použít (**Detect emotion using a stream** (Rozpoznávání emocí s použitím streamu) nebo **Detect emotion using a URL** (Rozpoznávání emocí s použitím adresy URL)), a pak postupujte podle pokynů na obrazovce. Microsoft přijme obrázky, které nahrajete, a může je použít k vylepšení rozhraní API pro rozpoznávání emocí a souvisejících služeb. Odesláním obrázku potvrzujete, že jste dodržovali naše [pravidla chování vývojářů](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).
4.  Pro použití s touto ukázkovou aplikací jsou k dispozici i ukázkové obrázky. Tyto obrázky najdete v [úložišti rozhraní API pro rozpoznávání tváře na GitHubu](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) ve složce **Data**. Mějte na paměti, že používání těchto obrázků je licencováno v rámci smlouvy o spravedlivém používání. To znamená, že je můžete použít k testování tohoto příkladu, ale nesmíte je znovu publikovat.

## <a name="Review">Kontrola a poznatky</a>
Když teď máte spuštěnou aplikaci, můžeme se podívat, jak se tato ukázková aplikace integruje se službami Microsoft Cognitive Services. Usnadníte si tím další vývoj této aplikace nebo vlastní aplikace s využitím rozhraní API Microsoftu pro rozpoznávání emocí.

Tato ukázková aplikace využívá klientskou knihovnu rozhraní API pro rozpoznávání emocí, což je tenká obálka klienta jazyka C# pro rozhraní API Microsoftu pro rozpoznávání emocí. Při vytváření ukázkové aplikace, jak je popsáno výše, jste klientskou knihovnu získali z balíčku NuGet. Zdrojový kód klientské knihovny si můžete prohlédnout ve složce [Client Library](https://github.com/Microsoft/Cognitive-Emotion-Windows/tree/master/ClientLibrary) v umístění **Emotion** > **Windows** > **Client Library**. Tato složka je součástí staženého úložiště souborů, jak je uvedeno výše v části [Požadavky](#Prerequisites).

Použití kódu klientské knihovny si můžete prohlédnout také v **Průzkumníku řešení**: V řešení **EmotionAPI-WPF_Samples** rozbalte složku **DetectEmotionUsingStreamPage.xaml** a vyhledejte soubor **DetectEmotionUsingStreamPage.xaml.cs**, který se používá pro přechod k místě uloženému souboru, nebo rozbalte složku **DetectEmotionUsingURLPage.xaml**, kde najdete soubor **DetectEmotionUsingURLPage.xaml.cs**, který se používá při nahrávání adresy URL obrázku. Dvojím kliknutím na soubory .xaml.cs je otevřete v nových oknech sady Visual Studio.

V rámci kontroly, jak se klientská knihovna rozhraní API pro rozpoznávání emocí používá v naší ukázkové aplikaci, se podívejme na dva fragmenty kódu ze souborů **DetectEmotionUsingStreamPage.xaml.cs** a **DetectEmotionUsingURLPage.xaml.cs**. Oba soubory obsahují komentáře ke kódu „KEY SAMPLE CODE STARTS HERE“ (Začátek klíčového vzorového kódu) a „KEY SAMPLE CODE ENDS HERE“ (Konec klíčového vzorového kódu), které vám pomůžou vyhledat níže uvedené fragmenty kódu.

Rozhraní API pro rozpoznávání emocí dokáže pracovat se vstupem v podobě adresy URL obrázku nebo binárních dat obrázku (ve formátu octet-stream). Tyto dvě možnosti jsou popsané níže. V obou případech nejprve vyhledáte direktivu using, která vám umožní používat klientskou knihovnu rozhraní API pro rozpoznávání emocí.
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

Tento fragment kódu ukazuje použití klientské knihovny k odeslání klíče předplatného a adresy URL fotky do služby rozhraní API pro rozpoznávání emocí.

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

Následující příklad ukazuje odeslání klíče předplatného a místně uloženého obrázku do rozhraní API pro rozpoznávání emocí.


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
