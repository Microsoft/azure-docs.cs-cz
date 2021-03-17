---
title: Analýza videí téměř v reálném čase – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Naučte se provádět analýzy téměř v reálném čase na snímcích, které jsou pořízeny z živého streamu videa, pomocí rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: c86b06ff46f1ddc8d22d2ab7ec4bc8620a8c862f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88933167"
---
# <a name="analyze-videos-in-near-real-time"></a>Analýza videí téměř v reálném čase

Tento článek ukazuje, jak provádět analýzu téměř v reálném čase na snímcích, které jsou pořízeny z živého streamu videa pomocí rozhraní API pro počítačové zpracování obrazu. Základní prvky této analýzy jsou:

- Získávají se snímky ze zdroje videa.
- Výběr snímků, které se mají analyzovat
- Odeslání těchto rámců do rozhraní API.
- Spotřebovává se každý výsledek analýzy, který se vrátí z volání rozhraní API.

Ukázky v tomto článku jsou napsány v jazyce C#. Chcete-li získat přístup k kódu, přejděte na stránku [Ukázka analýzy snímků videa](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) na GitHubu.

## <a name="approaches-to-running-near-real-time-analysis"></a>Přístupy ke spouštění analýz téměř v reálném čase

Můžete vyřešit problém spuštění analýzy téměř v reálném čase v datových proudech videa pomocí různých přístupů. Tento článek podrobněji popisuje tři z nich ve zvýšení úrovně sofistikovanější.

### <a name="design-an-infinite-loop"></a>Návrh nekonečné smyčky

Nejjednodušší návrh pro analýzu téměř v reálném čase je nekonečná smyčka. V každé iteraci této smyčky snímek, analyzujete a potom Využijte výsledek:

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

Pokud by vaše analýza byla tvořena odlehčeným algoritmem na straně klienta, tento přístup by byl vhodný. Pokud však dojde k analýze v cloudu, výsledná latence znamená, že volání rozhraní API může trvat několik sekund. Během této doby nezachycujete image a vaše vlákno v podstatě neprovede nic. Maximální frekvence snímků je omezená latencí volání rozhraní API.

### <a name="allow-the-api-calls-to-run-in-parallel"></a>Povolení paralelního spouštění volání rozhraní API

I když jednoduchá smyčka s jedním vláknem dává smysl pro odlehčený algoritmus na straně klienta, není dobře vhodný pro latenci volání cloudového rozhraní API. Řešením tohoto problému je, aby bylo možné spouštět dlouhotrvající volání rozhraní API paralelně s využitím rámců. V jazyce C# to můžete provést pomocí paralelismu založených na úlohách. Můžete například spustit následující kód:

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

S tímto přístupem spustíte každou analýzu v samostatné úloze. Úkol může běžet na pozadí, zatímco budete pokračovat v přecyklování nových snímků. Přístup zabraňuje blokování hlavního vlákna při čekání na vrácení volání rozhraní API. Přístup ale může představovat určité nevýhody:
* Stojí za vás některé záruky, které poskytuje jednoduchá verze. To znamená, že může dojít k paralelnímu volání rozhraní API a výsledky se můžou vrátit v nesprávném pořadí. 
* Může také dojít k tomu, že více vláken může současně zadat funkci ConsumeResult (), což může být nebezpečné, pokud funkce není bezpečná pro přístup z více vláken. 
* A nakonec tento jednoduchý kód nesleduje úlohy, které se vytvoří, takže výjimky budou tiše zmizet. Proto je třeba přidat vlákno "příjemce", které sleduje úlohy analýzy, vyvolává výjimky, ukončuje dlouhotrvající úlohy a zajistí, že se výsledky budou spotřebovat ve správném pořadí, v jednom okamžiku.

### <a name="design-a-producer-consumer-system"></a>Návrh zákaznického systému pro producenty

Pro svůj konečný přístup si můžete navrhnout systém "producent – spotřebitel" a vytvořit vlákno producenta, které vypadá podobně jako vaše dřív zmíněná nekonečná smyčka. Místo toho, aby se výsledky analýzy využívaly, jakmile jsou k dispozici, výrobce jednoduše umístí úlohy do fronty, aby je udržely.

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

Vytvoříte také uživatelské vlákno, které přebírá úlohy mimo frontu, čeká na jejich dokončení a buď zobrazí výsledek, nebo vyvolá výjimku, která byla vyvolána. Pomocí fronty můžete zaručit, že se výsledky budou spotřebovávat po jednom, ve správném pořadí bez omezení maximální frekvence snímků systému.

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Wait until the task is completed.
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

## <a name="implement-the-solution"></a>Implementace řešení

### <a name="get-started-quickly"></a>Začínáme rychle

Abychom vám pomohli co nejrychleji začít pracovat s vaší aplikací, implementovali jsme systém, který je popsaný v předchozí části. Má být dostatečně flexibilní, aby vyhovovalo mnoha scénářům, a přitom je snadné ho používat. Chcete-li získat přístup k kódu, přejděte na stránku [Ukázka analýzy snímků videa](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) na GitHubu.

Knihovna obsahuje `FrameGrabber` třídu, která implementuje dříve popisovaný systém pro zákazníka ke zpracování snímků videa z webové kamery. Uživatelé mohou zadat přesný tvar volání rozhraní API a třída používá události, aby mohl volající kód zjistit, kdy se získá nový rámec, nebo když je k dispozici nový výsledek analýzy.

Abychom se seznámili s některými možnostmi, poskytovali jsme dvě ukázkové aplikace, které knihovnu používají. 

První ukázková aplikace je jednoduchá Konzolová aplikace, která přikládá snímky z výchozí webové kamery a pak je odesílá do služby obličeje pro rozpoznávání tváře. Zjednodušená verze aplikace je reprodukována v následujícím kódu:

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
using VideoFrameAnalyzer;

namespace BasicConsoleSample
{
    internal class Program
    {
        const string ApiKey = "<your API key>";
        const string Endpoint = "https://<your API region>.api.cognitive.microsoft.com";

        private static async Task Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up a Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face service.
                return (await faceClient.Face.DetectWithStreamAsync(frame.Image.ToMemoryStream(".jpg"))).ToArray();
            };

            // Set up a listener for when we receive a new result from an API call.
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.TimedOut)
                    Console.WriteLine("API call timed out.");
                else if (e.Exception != null)
                    Console.WriteLine("API call threw an exception.");
                else
                    Console.WriteLine($"New result received for frame acquired at {e.Frame.Metadata.Timestamp}. {e.Analysis.Length} faces detected");
            };

            // Tell grabber when to call the API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            await grabber.StartProcessingCameraAsync();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            await grabber.StopProcessingAsync();
        }
    }
}
```

Druhá ukázková aplikace je trochu zajímavější. Umožňuje zvolit rozhraní API, které se má volat na snímky videa. Na levé straně aplikace zobrazuje náhled živého videa. Napravo překrývá poslední výsledek rozhraní API na odpovídajícím snímku.

Ve většině režimů je viditelná prodleva mezi živým videem vlevo a vizuální analýzou na pravé straně. Toto zpoždění je čas potřebný k provedení volání rozhraní API. Výjimka je v režimu "EmotionsWithClientFaceDetect", který provádí rozpoznávání tváře místně na klientském počítači pomocí OpenCV před odesláním jakýchkoli imagí do Azure Cognitive Services. 

Pomocí tohoto přístupu můžete vizualizovat zjištěnou plošku okamžitě. Až se volání rozhraní API vrátí, můžete emoce aktualizovat později. To ukazuje možnost hybridního přístupu. To znamená, že v klientovi lze provést některé jednoduché zpracování a pak rozhraní API služeb Cognitive Services lze použít k rozšíření tohoto zpracování s pokročilejší analýzou, pokud je to nutné.

![Aplikace LiveCameraSample zobrazující obrázek pomocí značek](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>Integrace ukázek do vašeho základu kódu

Chcete-li začít s touto ukázkou, postupujte následovně:

1. Vytvořte [účet Azure](https://azure.microsoft.com/free/cognitive-services/). Pokud ho už máte, můžete přejít k dalšímu kroku.
2. Vytvořte prostředky pro Počítačové zpracování obrazu a tvář v Azure Portal, abyste získali svůj klíč a koncový bod. Při instalaci nezapomeňte vybrat bezplatnou úroveň (F0).
   - [Počítačové zpracování obrazu](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
   - [Tvář](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) Po nasazení prostředků klikněte na **Přejít k prostředku** a Shromážděte svůj klíč a koncový bod pro každý prostředek. 
3. Naklonujte úložiště GitHub [-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub.
4. Otevřete ukázku v aplikaci Visual Studio 2015 nebo novější a poté Sestavte a spusťte ukázkové aplikace:
    - V případě BasicConsoleSample je klíč obličeje pevně zakódovaný přímo v [BasicConsoleSample/program. cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - V případě LiveCameraSample zadejte klíče v podokně **Nastavení** aplikace. Klíče se chovají napříč relacemi jako uživatelská data.

Až budete připraveni na integraci ukázek, odkazujte na knihovnu VideoFrameAnalyzer ze svých vlastních projektů.

Funkce VideoFrameAnalyzer s využitím obrázků, hlasu, videa a porozumění textu v využívají Azure Cognitive Services. Microsoft obdrží obrázky, zvuk, video a další data, která nahráváte (prostřednictvím této aplikace), a může je používat pro účely zlepšování služby. Žádáme vás o pomoc při ochraně uživatelů, jejichž data vaše aplikace odesílá do služeb Azure Cognitive Services.

## <a name="summary"></a>Shrnutí

V tomto článku jste zjistili, jak spustit analýzu téměř v reálném čase u streamů videa s využitím obličeje a Počítačové zpracování obrazu služeb. Zjistili jste také, jak můžete začít používat náš vzorový kód.

V [úložišti GitHubu](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)můžete poskytnout zpětnou vazbu a návrhy. Pokud chcete poskytnout širší názor na rozhraní API, přečtěte si náš [Web UserVoice](https://cognitive.uservoice.com/).

