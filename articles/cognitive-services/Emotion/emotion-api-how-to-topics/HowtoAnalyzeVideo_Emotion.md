---
title: Video analýzy v reálném čase s rozhraním API pro rozpoznávání emocí úrovně | Microsoft Docs
description: Pomocí rozhraní API pro rozpoznávání emocí úrovně v kognitivní služby k provedení analýzy téměř v reálném čase na rámce převzaty z živý datový proud videa.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/25/2017
ms.author: anroth
ms.openlocfilehash: 3a809e729e3b697b92d9fc59351a200748bcb884
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342721"
---
# <a name="how-to-analyze-videos-in-real-time"></a>Jak analyzovat videa v reálném čase

> [!IMPORTANT]
> 30. října 2017 ukončen video Preview rozhraní API. Vyzkoušet nový [Preview rozhraní API Indexer Video](https://azure.microsoft.com/services/cognitive-services/video-indexer/) k snadno rozbalte statistiky z videa a vylepšení možnosti zjišťování obsahu, jako je například výsledky hledání, pomocí zjišťování mluvené slovo, řezy, znaků a emoce. [Další informace](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Tato příručka popisuje, jak provádět analýzy téměř v reálném čase na rámce převzaty z živý datový proud videa. Tento systém základní komponenty jsou:
- Získat rámce z zdroj videa
- Určete, které snímky k analýze
- Odeslání tyto snímky do rozhraní API
- Využívat každý výsledek analýzy, která je vrácena z volání rozhraní API

Tyto ukázky jsou napsané v C# a kód naleznete na webu GitHub zde: [ https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis ](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>Přístupu
K vyřešení problému spuštění téměř v reálném čase analýza datové proudy videa z několika způsoby. Spustíme seznamu vytvořit tři přístupů zvyšující se úroveň vyspělosti.

### <a name="a-simple-approach"></a>Jednoduchý přístup
Nejjednodušší návrhu pro systém analysis téměř v reálném čase je nekonečnou smyčku, kde v každé iteraci jsme získat rámečku, analyzovat a pak využívat výsledek:
```CSharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```
Pokud se Naše analýza lightweight algoritmem straně klienta, bude vhodné tento přístup. Však při analýze se děje v cloudu, latence související se situací znamená, že volání rozhraní API může trvat několik sekund, během které doby jsme nejsou zaznamenání bitové kopie a naše vlákno je to v podstatě nic. Naše Maximální obnovovací frekvence je omezena latencí volání rozhraní API.

### <a name="parallelizing-api-calls"></a>Paralelního volání rozhraní API
Při jednoduché smyčky jednovláknové smysl pro prosté algoritmus straně klienta, nevejdou s latencí zahrnutých v cloudu API volání. Řešení tohoto problému je umožnit volání dlouho běžící rozhraní API provést paralelně s metodou rámce. V jazyce C# jsme může dosáhnout pomocí založený na úlohách paralelismu, například:
```CSharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () => 
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```
Spustí každý analýzy v samostatných úkolu, který můžete spustit na pozadí, zatímco abychom mohli pokračovat metodou nové rámce. Tím je zabráněno blokování hlavní vlákno při čekání na volání rozhraní API vrátit, ale nemůžeme došlo ke ztrátě některých záruky, jednoduché verze dodané – více volání rozhraní API může dojít paralelně a může získat vráceny výsledky v nesprávném pořadí. To může také způsobit více vláken současně, zadejte ConsumeResult() funkce, které může být nebezpečný, pokud funkce není bezpečné pro přístup z více vláken. Nakonec tohoto jednoduchého kódu není sledovat určité úlohy, které jsou vytvářeny, tak výjimky bezobslužně zmizí. Poslední přísady nemůžeme přidat tedy vlákno "příjemce", který bude sledování úkolů analysis vyvolávání výjimek, kill dlouhotrvající úlohy a ujistěte se, že výsledky získat využívat ve správném pořadí, po jednom.

### <a name="a-producer-consumer-design"></a>Návrh producent – příjemce
V našem systému konečné "producent – příjemce" máme producent vlákno, které velmi podobná naše předchozí nekonečná smyčka. Však místo použití výsledky analýzy, jakmile jsou k dispozici, Autor jednoduše umístí úlohy do fronty ke sledování je.
```CSharp
// Queue that will contain the API call tasks. 
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();
     
// Producer thread. 
while (true)
{
    // Grab a frame. 
    Frame f = GrabFrame();
 
    // Decide whether to analyze the frame. 
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread. 
        var analysisTask = Task.Run(async () => 
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }
        
        // Push the task onto the queue. 
        taskQueue.Add(analysisTask);
    }
}
```
Máme také vlákno příjemce, který trvá úlohy vypnout fronty, čeká na jejich dokončení, a zobrazení výsledek nebo vyvolá výjimku, která byla vyvolána. Pomocí fronty můžeme zaručit, že výsledky získat spotřebované jeden po druhém, ve správném pořadí, bez omezení maximální obnovovací frekvence systému.
```CSharp
// Consumer thread. 
while (true)
{
    // Get the oldest task. 
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed. 
    var output = await analysisTask;
     
    // Consume the exception or result. 
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implementing-the-solution"></a>Implementace řešení
### <a name="getting-started"></a>Začínáme
Získat aplikaci nahoru a systémem co nejrychleji, jsme byla implementována výše uvedeného, záměrné, že bude dostatečně flexibilní, aby implementovat mnoho scénářů, aniž by byly snadno použitelné. Chcete-li získat přístup k kód, přejděte na [ https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis ](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

Knihovna obsahuje třídu FrameGrabber, která implementuje producent – příjemce systému výše popsané zpracovat snímky videa z webová kamera. Uživatel může určit přesnou podobu volání rozhraní API a třída používá události umožníte kód volání vědět, když je získali na nový snímek nebo nové výsledku analýzy je k dispozici.

Pro ilustraci některých možností, existují dvě ukázkových aplikací, které používá knihovnu. První je jednoduché konzolové aplikace a zjednodušenou verzi to je uvedeno níže. Získá rámce z výchozí webová kamera a odesílá je do rozhraní API vzhled pro zjišťování řez.
```CSharp
using System;
using VideoFrameAnalyzer;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;
     
namespace VideoFrameConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create grabber, with analysis type Face[]. 
            FrameGrabber<Face[]> grabber = new FrameGrabber<Face[]>();
            
            // Create Face API Client. Insert your Face API key here.
            FaceServiceClient faceClient = new FaceServiceClient("<subscription key>");

            // Set up our Face API call.
            grabber.AnalysisFunction = async frame => return await faceClient.DetectAsync(frame.Image.ToMemoryStream(".jpg"));

            // Set up a listener for when we receive a new result from an API call. 
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.Analysis != null)
                    Console.WriteLine("New result received for frame acquired at {0}. {1} faces detected", e.Frame.Metadata.Timestamp, e.Analysis.Length);
            };
            
            // Tell grabber to call the Face API every 3 seconds.
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for keypress to stop
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();
            
            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```
Druhý ukázkové aplikace je vyzkoušíte zajímavější a umožňuje zvolit, které rozhraní API pro vyvolání na snímky videa. Na levé straně aplikace zobrazuje náhled za provozu video, na pravé straně, že se zobrazí poslední výsledek rozhraní API jako překryvný obrázek na odpovídající rámečku.

Ve většině režimů budou existovat viditelné zpoždění mezi videa na levé straně a analýze vizualizovaných na pravé straně. Toto opoždění je čas potřebný k volání rozhraní API. Výjimkou je v režimu "EmotionsWithClientFaceDetect", který provádí zjišťování vzhled místně na klientském počítači pomocí OpenCV, před odesláním všechny Image kognitivní Services. Tímto způsobem, jsme můžete vizualizovat zjištěné tučné okamžitě a později aktualizovat emoce, jakmile vrátí volání rozhraní API. Tento příklad ukazuje možnost "hybridní" přístupu, kde lze provést některé jednoduché zpracování na straně klienta, a potom kognitivní rozhraní API služby lze použít k posílení to pokročilejší analýzy, pokud je to nezbytné.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrace do vaší základu kódu
Začít s tuto ukázku, postupujte takto:

1. Získání klíčů rozhraní API pro rozhraní API zpracování obrazu z [odběry](https://azure.microsoft.com/try/cognitive-services/). Pro analýzu snímek videa použít rozhraní API jsou:
    - [Rozhraní API pro počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Rozpoznávání emocí úrovně rozhraní API](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [Rozhraní API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Klon [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) úložiště GitHub

3. Otevřete ukázku v sadě Visual Studio 2015, sestavení a spuštění ukázkových aplikací:
    - Pro BasicConsoleSample, klíč rozhraní API řez je pevně zakódovaná přímo v [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Pro LiveCameraSample musí být zadán klíče do podokna nastavení aplikace. Bude se trvalé napříč relacemi jako uživatelská data.
        

Až budete připraveni k integraci, **jednoduše knihovně VideoFrameAnalyzer odkazovat z vašich vlastních projektů.** 



## <a name="developer-code-of-conduct"></a>Pravidla chování vývojáře
Jako u všech kognitivní služeb, vývoji s naše rozhraní API a ukázky vývojáři musí odpovídat "[vývojáře pravidla chování pro služby Microsoft Cognitive](https://azure.microsoft.com/support/legal/developer-code-of-conduct/)." 


Bitové kopie, hlasové, videa nebo text vysvětlení možností VideoFrameAnalyzer používá kognitivní služby společnosti Microsoft. Microsoft obdrží bitové kopie, zvuk, video a další data nahrát (přes tuto aplikaci) a může je použít pro účely zlepšování služby. Můžeme požádat za pomoc při ochraně osob, jejichž data vaše aplikace odesílá do kognitivní služby společnosti Microsoft. 


## <a name="summary"></a>Souhrn
V této příručce jste zjistili, jak spustit analýzu téměř v reálném čase na živé datové proudy videa pomocí vzhled, počítač vize a rozhraní API pro rozpoznávání emocí úrovně a jak naše ukázkový kód můžete začít pracovat.  Můžete začít používat s bezplatnou klíči rozhraní API v sestavení aplikace [stránku pro přihlášení kognitivní služby Microsoft](https://azure.microsoft.com/try/cognitive-services/). 

Prosím klidně zajistit názory a návrhy v [úložiště GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/), nebo další široký API zpětnou vazbu, na našem [UserVoice lokality](https://cognitive.uservoice.com/).

