---
title: 'Příklad: Analýza videa v reálném čase – rozhraní API pro rozpoznávání Emocí'
titlesuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro rozpoznávání emocí můžete provádět analýzu snímků z živého video streamu téměř v reálném čase.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: sample
ms.date: 01/25/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: ec36193e341cac899e696c642a1d600f3f80a5f2
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224860"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>Příklad: Analyzování videí v reálném čase

> [!IMPORTANT]
> Rozhraní API pro rozpoznávání emocí se přestane používat 15. února 2019. Funkce rozpoznávání emocí je teď obecně dostupná v rámci [rozhraní API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/).

Tato příručka ukazuje, jak provádět analýzu snímků z živého video streamu téměř v reálném čase. Takový systém funguje následovně:
- Pořídí snímky ze zdroje videa.
- Vybere snímky, které se mají analyzovat.
- Odešle tyto snímky do rozhraní API.
- Přijme jednotlivé výsledky analýzy vrácené z volání rozhraní API.

Tyto ukázky jsou napsané v jazyce C# a kód najdete na GitHubu tady: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>Přístup
Problém s analýzou video streamů téměř v reálném čase se dá vyřešit několika způsoby. Začneme nastíněním tří přístupů s narůstající úrovní složitosti.

### <a name="a-simple-approach"></a>Jednoduchý přístup
Nejjednodušším návrhem systému pro analýzu téměř v reálném čase je nekonečná smyčka, kdy se v každé iteraci vezme snímek, analyzuje se a výsledek se následně přijme:
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
Pokud analýza spočívá v jednoduchém algoritmu na straně klienta, bude tento přístup vhodný. Pokud ale analýza probíhá v cloudu, může kvůli latenci trvat volání rozhraní API několik sekund, během kterých se nebudou zachytávat snímky a vlákno nebude dělat v podstatě nic. Maximální frekvence snímků je omezená latencí volání rozhraní API.

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

Tento kód spouští jednotlivé analýzy v samostatných úlohách, které můžou běžet na pozadí, zatímco se nadále zachytávají nové snímky. Zabrání to sice blokování hlavního vlákna při čekání na návrat volání rozhraní API, ale přijdeme o určité záruky, které poskytovala jednoduchá verze – k několika voláním rozhraní API může dojít souběžně a výsledky se nemusejí vracet ve správném pořadí. To může také způsobit více vláken současně, zadejte ConsumeResult() funkce, které by mohly být nebezpečné, pokud funkce není bezpečná pro vlákno. Tento jednoduchý kód navíc nesleduje vytvářené úlohy, takže výjimky bez povšimnutí zmizí. Poslední ingrediencí, kterou potřebujeme přidat, je vlákno „příjemce“, které bude sledovat úlohy analýzy, vyvolávat výjimky, ukončovat dlouho běžící úlohy a zajišťovat, aby se výsledky přijímaly postupně a ve správném pořadí.

### <a name="a-producer-consumer-design"></a>Návrh typu producent-příjemce
Ve finálním systému „producent-příjemce“ je vlákno producenta, které se hodně podobá předchozí nekonečné smyčce. Místo toho, aby producent přijímal výsledky analýzy hned, jak jsou dostupné, jednoduše zařazuje úlohy do fronty, aby o nich měl přehled.
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
Je zde rovněž vlákno příjemce, které přebírá úlohy z fronty, čeká na jejich dokončení a pak buď zobrazí výsledek, nebo vyvolá vzniklou výjimku. Díky frontě můžeme zaručit, že se výsledky přijímají postupně a ve správném pořadí, a to bez omezení maximální frekvence snímků systému.
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
Kvůli co nejrychlejšímu zprovoznění aplikace jsme implementovali výše popsaný systém, který je dostatečně flexibilní, aby vyhovoval různým situacím, ale zároveň se snadno používal. Tento kód můžete získat na adrese [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

Knihovna obsahuje třídu FrameGrabber, která implementuje systém producent – příjemce bylo uvedeno výše pro zpracování snímky videí z webová kamera. Uživatel může určit přesnou formu volání rozhraní API, přičemž tato třída informuje volající kód pomocí událostí o pořízení nového snímku nebo dostupnosti výsledku analýzy.

Některé možnosti ilustrují dvě ukázkové aplikace, které tuto knihovnu používají. První je jednoduchá konzolová aplikace, jejíž zjednodušená verze je uvedená níže. Zachytává snímky z výchozí webkamery a odesílá je do rozhraní API pro rozpoznávání tváře za účelem detekce obličeje.
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
Druhá ukázková aplikace je o něco zajímavější a umožňuje zvolit, které rozhraní API se má na snímky videa zavolat. Tato aplikace zobrazuje na levé straně náhled živého videa a na pravé straně ukazuje poslední výsledek rozhraní API překrývající odpovídající snímek.

Ve většině režimů bude mezi živým videem vlevo a vizualizovanou analýzou vpravo viditelné zpoždění. Toto zpoždění představuje dobu, jakou trvá volání rozhraní API. Výjimkou je v režimu "EmotionsWithClientFaceDetect", který provádí rozpoznávání tváře lokálně na klientském počítači pomocí OpenCV, před odesláním žádné obrázky do služeb Cognitive Services. Díky tomu můžeme detekovanou tvář vizualizovat okamžitě a emoce aktualizovat později, jakmile se vrátí volání rozhraní API. To demonstruje možnost „hybridního“ přístupu, kdy je možné určité jednoduché zpracování provést na straně klienta, a v případě potřeby je rozšířit o pokročilejší analýzu pomocí rozhraní API služeb Cognitive Services.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrace do základu kódu
Pokud chcete s touto ukázkou začít, postupujte takto:

1. Získejte klíče rozhraní API pro zpracování obrazu v oblasti [Předplatná](https://azure.microsoft.com/try/cognitive-services/). U analýzy snímků videa jde o tato rozhraní API:
    - [Rozhraní API pro počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Rozhraní API pro rozpoznávání emocí](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [Rozhraní API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Naklonujte úložiště [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) na GitHubu.

3. Otevřete tuto ukázku v sadě Visual Studio 2015 a sestavte a spusťte ukázkové aplikace:
    - Pro BasicConsoleSample, klíč rozhraní API pro rozpoznávání tváře je pevně zakódované přímo v [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - U aplikace LiveCameraSample se klíče zadávají do panelu s nastavením aplikace. Uloží se pro následné relace jako uživatelská data.


Až budete připravení integrovat, **jednoduše odkažte na knihovnu VideoFrameAnalyzer ze svých vlastních projektů**.



## <a name="developer-code-of-conduct"></a>Pravidla chování vývojáře
Jako u všech služeb Cognitive Services musí vývojáři, kteří k vývoji používají naše rozhraní API a ukázky, dodržovat [pravidla chování vývojářů pro Azure Cognitive Services](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).


Funkce knihovny VideoFrameAnalyzer pro rozpoznávání obrazu, hlasu, videa nebo textu využívají služby Azure Cognitive Services. Microsoft bude přijímat obrázky, zvuk, video a další data, která (přes tuto aplikaci) nahrajete, a může je používat pro účely vylepšování služeb. Žádáme vás o pomoc při ochraně uživatelů, jejichž data vaše aplikace odesílá do služeb Azure Cognitive Services.


## <a name="summary"></a>Souhrn
V této příručce jste se naučili analyzovat živé video streamy téměř v reálném čase pomocí rozhraní API pro rozpoznávání tváře, počítačové zpracování obrazu a rozpoznávání emocí a zjistili, jak můžete využít náš ukázkový kód.  Svou vlastní aplikaci můžete začít sestavovat s využitím bezplatných klíčů rozhraní API na [registrační stránce služby Azure Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

Můžete poskytnout zpětnou vazbu a návrhů v [úložiště GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/), nebo na další názory široké rozhraní API, na našem [webu UserVoice](https://cognitive.uservoice.com/).
