---
title: 'Příklad: Analýza videa v reálném čase – rozhraní API pro rozpoznávání tváře'
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro rozpoznávání tváře můžete provádět analýzu snímků z živého video streamu téměř v reálném čase.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 6aac7d99e002413406022388eaa7ad1a98ae7b34
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232154"
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

Nejjednodušší návrhu systému analýzy téměř v reálném čase je nekonečná smyčka, kde každé iteraci vezme blok, analyzuje je a potom využívá výsledek:

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

Pokud analýza spočívá v jednoduchém algoritmu na straně klienta, bude tento přístup vhodný. Ale při analýze dojde v cloudu, zahrnutých latence znamená, že, že volání rozhraní API může trvat několik sekund. Během této doby zaznamenáváme imagí a naše vlákno je v podstatě nicneděláním. Maximální frekvence snímků je omezená latencí volání rozhraní API.

### <a name="parallelizing-api-calls"></a>Paralelizace volání rozhraní API

I když prostá smyčka s jedním vláknem dává smysl u jednoduchého algoritmu na straně klienta, nevyhovuje latenci cloudových volání rozhraní API. Tento problém je možné vyřešit tak, že umožníte, aby se dlouhá volání rozhraní API prováděla souběžně se zachytáváním snímků. V jazyce C# toho můžeme dosáhnout pomocí paralelismu na základě úloh, například:

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

Tento kód spouští jednotlivé analýzy v samostatných úlohách, které můžou běžet na pozadí, zatímco se nadále zachytávají nové snímky. Pomocí této metody jsme předcházet zablokování hlavní vlákno při čekání na volání rozhraní API vrátit, ale přijdeme některé záruky, které jednoduchá verze k dispozici. Několik volání rozhraní API může dojít k paralelně a může získat výsledky vrátí v nesprávném pořadí. To může také způsobit více vláken současně, zadejte ConsumeResult() funkce, které by mohly být nebezpečné, pokud funkce není bezpečná pro vlákno. Tento jednoduchý kód navíc nesleduje vytvářené úlohy, takže výjimky bez povšimnutí zmizí. Posledním krokem je proto, pokud chcete přidat vlákno "příjemce", který bude sledovat úlohy analýzy vyvolávat výjimky, ukončit dlouhotrvající úlohy a ujistěte se, že se výsledky spotřebovává ve správném pořadí.

### <a name="a-producer-consumer-design"></a>Návrh typu producent-příjemce

Ve finálním systému „producent-příjemce“ je vlákno producenta, které se podobá předchozí nekonečné smyčce. Místo toho, aby producent přijímal výsledky analýzy hned, jak jsou dostupné, jednoduše zařazuje úlohy do fronty, aby o nich měl přehled.

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

Máme také konzumního vlákna, která přebírá úlohy z fronty, čeká na výsledek jejich dokončení a buď zobrazí nebo vyvolá výjimku, která byla vyvolána. Díky frontě můžeme zaručit, že se výsledky přijímají postupně a ve správném pořadí, a to bez omezení maximální frekvence snímků systému.

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

Pokud chcete zprovoznit svou aplikaci tak rychle, bude používat flexibilní implementace systému je popsáno výše. Tento kód můžete získat na adrese [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

Knihovna obsahuje třídu FrameGrabber, která implementuje systém producent – příjemce bylo uvedeno výše pro zpracování snímky videí z webová kamera. Uživatele můžete zadat přesný formu volání rozhraní API a používá třídu události Pokud chcete, aby volající kód vědět, kdy získat nový rámec nebo je k dispozici nový výsledek analýzy.

Některé možnosti ilustrují dvě ukázkové aplikace, které tuto knihovnu používají. První je jednoduchou konzolovou aplikaci a zjednodušenou verzi je uveden níže. Zachytává snímky z výchozí webkamery a odesílá je do rozhraní API pro rozpoznávání tváře za účelem detekce obličeje.

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
            FaceServiceClient faceClient = new FaceServiceClient("<Subscription Key>");

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

Ve většině režimů bude mezi živým videem vlevo a vizualizovanou analýzou vpravo viditelné zpoždění. Toto zpoždění představuje dobu, jakou trvá volání rozhraní API. Jedinou výjimkou je režim "EmotionsWithClientFaceDetect", který provádí rozpoznávání tváře lokálně na klientském počítači pomocí OpenCV, před odesláním žádné obrázky do služeb Cognitive Services. Tímto způsobem můžeme můžete vizualizovat zjištěné rozpoznávání tváře okamžitě a pak aktualizujte rozpoznávanými po volání API vrátí. Jedná se o příklad "hybridní" přístup, kde klienta může provádět některé jednoduché zpracování, a to rozhraní API služeb Cognitive Services můžete rozšířit pomocí rozšířené analýzy, pokud je to nezbytné.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrace do základu kódu

Pokud chcete s touto ukázkou začít, postupujte takto:

1. Získejte klíče rozhraní API pro zpracování obrazu v oblasti [Předplatná](https://azure.microsoft.com/try/cognitive-services/). U analýzy snímků videa jde o tato rozhraní API:
    - [Rozhraní API pro počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Rozhraní API pro rozpoznávání emocí](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [Rozhraní API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/overview)

2. Naklonujte úložiště [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) na GitHubu.

3. Otevřete ukázku v sadě Visual Studio 2015, sestavení a spuštění ukázkové aplikace:
    - Pro BasicConsoleSample, klíč rozhraní API pro rozpoznávání tváře je pevně zakódované přímo v [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - U aplikace LiveCameraSample se klíče zadávají do panelu s nastavením aplikace. Uloží se pro následné relace jako uživatelská data.
        

Jakmile budete připraveni k integraci, **odkazovat na knihovny VideoFrameAnalyzer vaše vlastní projekty.** 

## <a name="summary"></a>Souhrn

V této příručce jste zjistili, jak spouštět analýzy téměř v reálném čase na živé datové proudy videa pomocí rozpoznávání tváře, pro počítačové zpracování obrazu a rozhraní API pro rozpoznávání Emocí a jak používat náš vzorový kód začít. Můžete začít vytvářet aplikace s rozhraním API pro bezplatné kláves [registrační stránku služeb Azure Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). 

Nebojte se poskytnout zpětnou vazbu a návrhů v [úložiště GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) nebo pro širší zpětná vazba rozhraní API na naše [webu UserVoice](https://cognitive.uservoice.com/).

## <a name="related-topics"></a>Související témata
- [Postup identifikace tváří v obrázku](HowtoIdentifyFacesinImage.md)
- [Postup rozpoznání tváří v obrázku](HowtoDetectFacesinImage.md)
