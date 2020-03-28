---
title: 'Příklad: Analýza videa v reálném čase - Face'
titleSuffix: Azure Cognitive Services
description: Pomocí služby Face můžete provádět analýzu snímků pořízených z živého streamu videa téměř v reálném čase.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: ab3f596000216e8555bb84d0d47aff9a6e969eeb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169902"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>Příklad: Analýza videí v reálném čase

Tato příručka ukazuje, jak provádět analýzu snímků z živého video streamu v téměř reálném čase. Takový systém funguje následovně:

- Pořídí snímky ze zdroje videa.
- Vybere snímky, které se mají analyzovat.
- Odešle tyto snímky do rozhraní API.
- Přijme jednotlivé výsledky analýzy vrácené z volání rozhraní API.

Tyto ukázky jsou napsány v C# a kód [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)lze nalézt na GitHubu zde: .

## <a name="the-approach"></a>Přístup

Problém s analýzou video streamů téměř v reálném čase se dá vyřešit několika způsoby. Začneme nastíněním tří přístupů s narůstající úrovní složitosti.

### <a name="a-simple-approach"></a>Jednoduchý přístup

Nejjednodušší návrh pro téměř real-time analytický systém je nekonečná smyčka, kde každá iterace uchopí snímek, analyzuje jej a pak spotřebovává výsledek:

```csharp
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

Pokud analýza spočívá v jednoduchém algoritmu na straně klienta, bude tento přístup vhodný. Pokud však dojde k analýze v cloudu, související latence znamená, že volání rozhraní API může trvat několik sekund. Během této doby nejsme zachytit obrázky, a naše vlákno je v podstatě nic nedělá. Maximální frekvence snímků je omezená latencí volání rozhraní API.

### <a name="parallelizing-api-calls"></a>Paralelizace volání rozhraní API

I když prostá smyčka s jedním vláknem dává smysl u jednoduchého algoritmu na straně klienta, nevyhovuje latenci cloudových volání rozhraní API. Tento problém je možné vyřešit tak, že umožníte, aby se dlouhá volání rozhraní API prováděla souběžně se zachytáváním snímků. V jazyce C# toho můžeme dosáhnout pomocí paralelismu na základě úloh, například:

```csharp
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

Tento kód spouští jednotlivé analýzy v samostatných úlohách, které můžou běžet na pozadí, zatímco se nadále zachytávají nové snímky. Pomocí této metody se vyhneme blokování hlavního vlákna při čekání na návrat volání rozhraní API, ale ztratili jsme některé záruky, které byla poskytnuta v jednoduché verzi. Více volání rozhraní API může dojít paralelně a výsledky mohou získat vráceny v nesprávném pořadí. Tento přístup může navíc znamenat, že několik vláken použije funkci ConsumeResult() souběžně, což může být nebezpečné, pokud tato funkce není bezpečná pro přístup z více vláken. Tento jednoduchý kód navíc nesleduje vytvářené úlohy, takže výjimky bez povšimnutí zmizí. Proto je posledním krokem přidání podprocesu "příjemce", který bude sledovat úlohy analýzy, zvýšit výjimky, zabít dlouhotrvající úkoly a zajistit, aby výsledky získat spotřebované ve správném pořadí.

### <a name="a-producer-consumer-design"></a>Návrh typu producent-příjemce

Ve finálním systému „producent-příjemce“ je vlákno producenta, které se podobá předchozí nekonečné smyčce. Místo toho, aby producent přijímal výsledky analýzy hned, jak jsou dostupné, jednoduše zařazuje úlohy do fronty, aby o nich měl přehled.

```csharp
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

Máme také vlákno příjemce, který bere úkoly z fronty, čeká na jejich dokončení a buď zobrazí výsledek nebo vyvolá výjimku, která byla vyvolána. Díky frontě můžeme zaručit, že se výsledky přijímají postupně a ve správném pořadí, a to bez omezení maximální frekvence snímků systému.

```csharp
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

Chcete-li aplikaci zprovoznit co nejrychleji, použijete flexibilní implementaci výše popsaného systému. Chcete-li získat přístup [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis)ke kódu, přejděte na .

Tato knihovna obsahuje třídu FrameGrabber, která implementuje výše popsaný systém producent-příjemce ke zpracování snímků videa z webkamery. Uživatel může zadat přesnou formu volání rozhraní API a třída používá události, aby volající kód věděl, kdy je získán nový rámec nebo je k dispozici nový výsledek analýzy.

Některé možnosti ilustrují dvě ukázkové aplikace, které tuto knihovnu používají. První je jednoduchá konzolová aplikace a zjednodušená verze je reprodukována níže. Popadne snímky z výchozí webové kamery a odešle je službě Face pro detekci obličeje.

```csharp
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
            
            // Create Face Client. Insert your Face API key here.
            private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials("<subscription key>"),
            new System.Net.Http.DelegatingHandler[] { });

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

Druhá ukázková aplikace je o něco zajímavější a umožňuje zvolit, které rozhraní API se má na snímky videa zavolat. Tato aplikace zobrazuje na levé straně náhled živého videa a na pravé straně ukazuje poslední výsledek rozhraní API překrývající odpovídající snímek.

Ve většině režimů bude mezi živým videem vlevo a vizualizovanou analýzou vpravo viditelné zpoždění. Toto zpoždění představuje dobu, jakou trvá volání rozhraní API. Jednou výjimkou je režim "EmotionsWithClientFaceDetect", který provádí detekci obličeje místně v klientském počítači pomocí OpenCV, před odesláním všech bitových kopií do služeb Cognitive Services. Tímto způsobem můžeme vizualizovat zjištěné tváře okamžitě a potom aktualizovat emoce, jakmile se vrátí volání rozhraní API. Toto je příklad "hybridní" přístup, kde klient může provádět některé jednoduché zpracování a cognitive services API můžete rozšířit pomocí pokročilejší analýzy v případě potřeby.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrace do základu kódu

Pokud chcete s touto ukázkou začít, postupujte takto:

1. Získejte klíče rozhraní API pro zpracování obrazu v oblasti [Předplatná](https://azure.microsoft.com/try/cognitive-services/). U analýzy snímků videa jde o tato rozhraní API:
    - [Rozhraní API pro počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Rozhraní API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/overview)

2. Naklonujte úložiště [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) na GitHubu.

3. Otevřete ukázku v Visual Studiu 2015 a sestavte a spusťte ukázkové aplikace:
    - Pro BasicConsoleSample je klávesa Face pevně zakódována přímo v [souboru BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - U aplikace LiveCameraSample se klíče zadávají do panelu s nastavením aplikace. Uloží se pro následné relace jako uživatelská data.
        

Až budete připraveni k integraci, **odkazujte na knihovnu VideoFrameAnalyzer z vlastních projektů.** 

## <a name="summary"></a>Souhrn

V této příručce jste se naučili, jak spustit analýzu v téměř reálném čase na živé video streamy pomocí rozhraní API pro obličej, počítačové vidění a emoce a jak začít používat náš ukázkový kód. Aplikaci můžete začít vytvářet s bezplatnými klíči rozhraní API na [přihlašovací stránce služby Azure Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). 

Neváhejte a poskytněte zpětnou vazbu a návrhy v [úložišti GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) nebo, pro širší zpětnou vazbu api, na našem [webu UserVoice](https://cognitive.uservoice.com/).

## <a name="related-topics"></a>Související témata
- [Jak identifikovat tváře v obrázku](HowtoIdentifyFacesinImage.md)
- [Postup rozpoznání tváří v obrázku](HowtoDetectFacesinImage.md)
