---
title: 'Příklad: Analýza videa v reálném čase – Face API'
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro rozpoznávání tváře můžete provádět analýzu snímků z živého video streamu téměř v reálném čase.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: e2166354fb45d24e117156e917f4da726ee8406f
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114347"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>Příklad: Analyzování videí v reálném čase

Tato příručka ukazuje, jak provádět analýzu snímků z živého video streamu v téměř reálném čase. Takový systém funguje následovně:

- Pořídí snímky ze zdroje videa.
- Vybere snímky, které se mají analyzovat.
- Odešle tyto snímky do rozhraní API.
- Přijme jednotlivé výsledky analýzy vrácené z volání rozhraní API.

Tyto ukázky jsou napsané v jazyce C# a kód najdete na GitHubu tady: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>Přístup

Problém s analýzou video streamů téměř v reálném čase se dá vyřešit několika způsoby. Začneme nastíněním tří přístupů s narůstající úrovní složitosti.

### <a name="a-simple-approach"></a>Jednoduchý přístup

Nejjednodušší návrh pro systém analýzy téměř v reálném čase je nekonečná smyčka, kde každá iterace přivede rámec, analyzuje je a potom spotřebuje výsledek:

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

Pokud analýza spočívá v jednoduchém algoritmu na straně klienta, bude tento přístup vhodný. Když ale dojde k analýze v cloudu, znamená to, že volání rozhraní API může trvat několik sekund. Během této doby nezachytávajíme image a naše vlákno v podstatě neprovede nic. Maximální frekvence snímků je omezená latencí volání rozhraní API.

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

Tento kód spouští jednotlivé analýzy v samostatných úlohách, které můžou běžet na pozadí, zatímco se nadále zachytávají nové snímky. Pomocí této metody se vyhnete zablokování hlavního vlákna při čekání na vrácení volání rozhraní API, ale ztratili jsme některé záruky, které poskytuje jednoduchá verze. Paralelní může probíhat více volání rozhraní API a výsledky se můžou vrátit v nesprávném pořadí. To může také způsobit, že více vláken má současně zadat funkci ConsumeResult (), což může být nebezpečné, pokud funkce není bezpečná pro přístup z více vláken. Tento jednoduchý kód navíc nesleduje vytvářené úlohy, takže výjimky bez povšimnutí zmizí. Posledním krokem je proto přidat vlákno "příjemce", které bude sledovat úlohy analýzy, vyvolat výjimky, ukončit dlouhotrvající úlohy a zajistit, aby výsledky byly spotřebovány ve správném pořadí.

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

Máme také příjemce vlákna, který přebírá úlohy mimo frontu, čeká na jejich dokončení a buď zobrazí výsledek, nebo vyvolá výjimku, která byla vyvolána. Díky frontě můžeme zaručit, že se výsledky přijímají postupně a ve správném pořadí, a to bez omezení maximální frekvence snímků systému.

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

Pokud chcete svou aplikaci začít používat co nejrychleji, budete používat flexibilní implementaci systému popsanou výše. Tento kód můžete získat na adrese [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

Knihovna obsahuje třídu FrameGrabber, která implementuje výše popsaný systém od producenta a zpracovává snímky videa z webové kamery. Uživatel může zadat přesný tvar volání rozhraní API a třída použije události k tomu, aby volající kód věděl, kdy se získá nový rámec, nebo je k dispozici nový výsledek analýzy.

Některé možnosti ilustrují dvě ukázkové aplikace, které tuto knihovnu používají. První je jednoduchá Konzolová aplikace a zjednodušená verze je reprodukována níže. Zachytává snímky z výchozí webkamery a odesílá je do rozhraní API pro rozpoznávání tváře za účelem detekce obličeje.

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
            
            // Create Face API Client. Insert your Face API key here.
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

Ve většině režimů bude mezi živým videem vlevo a vizualizovanou analýzou vpravo viditelné zpoždění. Toto zpoždění představuje dobu, jakou trvá volání rozhraní API. Jedinou výjimkou je režim "EmotionsWithClientFaceDetect", který provádí detekci tváře místně na klientském počítači pomocí OpenCV před odesláním jakýchkoli imagí Cognitive Services. Tímto způsobem můžeme vizualizovat zjištěnou plošku hned a následně aktualizovat emoce po návratu volání rozhraní API. Toto je příklad "hybridního" přístupu, kde může klient provádět určité jednoduché zpracování a rozhraní API služeb Cognitive Services může toto rozšíření rozšířit o pokročilejší analýzu v případě potřeby.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrace do základu kódu

Pokud chcete s touto ukázkou začít, postupujte takto:

1. Získejte klíče rozhraní API pro zpracování obrazu v oblasti [Předplatná](https://azure.microsoft.com/try/cognitive-services/). U analýzy snímků videa jde o tato rozhraní API:
    - [Rozhraní API pro počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Rozhraní API pro rozpoznávání emocí](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [Rozhraní API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/overview)

2. Naklonujte úložiště [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) na GitHubu.

3. Otevřete ukázku v aplikaci Visual Studio 2015 a sestavte a spusťte ukázkové aplikace:
    - V případě BasicConsoleSample je klíč Face API pevně zakódovaný přímo v [BasicConsoleSample/program. cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - U aplikace LiveCameraSample se klíče zadávají do panelu s nastavením aplikace. Uloží se pro následné relace jako uživatelská data.
        

Až budete připraveni na integraci, **odkazujte na knihovnu VideoFrameAnalyzer ze svých vlastních projektů.** 

## <a name="summary"></a>Souhrn

V této příručce jste zjistili, jak spustit analýzu v reálném čase v reálném čase pomocí rozhraní API pro obličeje, Počítačové zpracování obrazu a emoce a jak začít používat náš vzorový kód. Sestavování aplikace pomocí bezplatných klíčů rozhraní API můžete začít na [stránce pro registraci v Azure Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). 

V [úložišti GitHubu](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) můžete poskytnout zpětnou vazbu a návrhy nebo pro širší názory na rozhraní API na [webu UserVoice](https://cognitive.uservoice.com/).

## <a name="related-topics"></a>Související témata
- [Postup identifikace tváří v obrázku](HowtoIdentifyFacesinImage.md)
- [Postup rozpoznání tváří v obrázku](HowtoDetectFacesinImage.md)
