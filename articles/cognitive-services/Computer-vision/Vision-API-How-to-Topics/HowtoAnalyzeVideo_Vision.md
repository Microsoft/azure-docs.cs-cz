---
title: Analyzujte videa téměř v reálném čase - Počítačové vidění
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak provádět analýzy v blízkosti v reálném čase na snímcích, které jsou převzaty z živého datového proudu videa pomocí rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 18b158b7a4881619b93ab404de67f7bb25f92b6a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166833"
---
# <a name="analyze-videos-in-near-real-time"></a>Analyzujte videa téměř v reálném čase

Tento článek ukazuje, jak provádět analýzu téměř v reálném čase na snímcích, které jsou převzaty z živého datového proudu videa pomocí rozhraní API pro počítačové zpracování obrazu. Základními prvky takové analýzy jsou:

- Získávání snímků ze zdroje videa.
- Výběr snímků, které chcete analyzovat.
- Odeslání těchto rámců do rozhraní API.
- Spotřebovávají každý výsledek analýzy, který je vrácen z volání rozhraní API.

Ukázky v tomto článku jsou napsány v c#. Chcete-li získat přístup ke kódu, přejděte na [stránku ukázkové analýzy snímků videa](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) na GitHubu.

## <a name="approaches-to-running-near-real-time-analysis"></a>Přístupy k téměř analýze v reálném čase

Můžete vyřešit problém spuštění téměř v reálném čase analýzy na video streamy pomocí různých přístupů. Tento článek nastiňuje tři z nich, ve zvyšování úrovně propracovanosti.

### <a name="design-an-infinite-loop"></a>Navrhněte nekonečnou smyčku

Nejjednodušší návrh pro analýzu téměř v reálném čase je nekonečná smyčka. V každé iteraci této smyčky uchopíte snímek, analyzujete jej a pak spotřebujete výsledek:

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

Pokud by se vaše analýza skládala z odlehčeného algoritmu na straně klienta, byl by tento přístup vhodný. Však při analýze dojde v cloudu, výsledná latence znamená, že volání rozhraní API může trvat několik sekund. Během této doby nejste zachytávání obrázků, a vaše vlákno je v podstatě nedělá nic. Maximální kmitočet snímků je omezen latencí volání rozhraní API.

### <a name="allow-the-api-calls-to-run-in-parallel"></a>Povolit paralelní spouštění volání rozhraní API

Přestože jednoduchá smyčka s jedním vláknem má smysl pro odlehčený algoritmus na straně klienta, nezapadá dobře s latencí volání cloudového rozhraní API. Řešením tohoto problému je umožnit dlouhotrvající volání rozhraní API spustit paralelně s snímek popadat. V c#, můžete to provést pomocí paralelismu založeného na úlohách. Můžete například spustit následující kód:

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

S tímto přístupem spustíte každou analýzu v samostatném úkolu. Úloha může být spuštěna na pozadí, zatímco budete pokračovat v posunutí nových snímků. Přístup zabraňuje blokování hlavní vlákno při čekání na volání rozhraní API vrátit. Tento přístup však může představovat určité nevýhody:
* To vás stojí některé záruky, že jednoduchá verze k dispozici. To znamená, že více volání rozhraní API může dojít paralelně a výsledky mohou získat vráceny v nesprávném pořadí. 
* Může také způsobit více vláken zadat ConsumeResult() funkce současně, což může být nebezpečné, pokud funkce není bezpečné pro přístup z více vláken. 
* Nakonec tento jednoduchý kód nesleduje úkoly, které se vytvářejí, takže výjimky tiše zmizí. Proto je třeba přidat vlákno "příjemce", které sleduje úkoly analýzy, vyvolává výjimky, zabíjí dlouhotrvající úkoly a zajišťuje, že výsledky získat spotřebovány ve správném pořadí, jeden po druhém.

### <a name="design-a-producer-consumer-system"></a>Navrhnout systém výrobce a spotřebitele

Pro váš konečný přístup, navrhování "výrobce-spotřebitel" systém, můžete vytvořit výrobce vlákno, které vypadá podobně jako vaše výše uvedené nekonečné smyčky. Namísto konzumace výsledků analýzy, jakmile jsou k dispozici, však výrobce jednoduše umístí úkoly do fronty, aby je mohl sledovat.

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

Můžete také vytvořit vlákno příjemce, který bere úkoly z fronty, čeká na jejich dokončení a buď zobrazí výsledek nebo vyvolá výjimku, která byla vyvolána. Pomocí fronty můžete zaručit, že výsledky se spotřebovávají jeden po druhém, ve správném pořadí, bez omezení maximální kmitočet snímků systému.

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

### <a name="get-started-quickly"></a>Rychlé zahájení

Abychom aplikaci zprovozňovali co nejrychleji, implementovali jsme systém popsaný v předchozí části. Má být dostatečně flexibilní, aby vyhovoval mnoha scénářům a zároveň byl snadno použitelný. Chcete-li získat přístup ke kódu, přejděte na [stránku ukázkové analýzy snímků videa](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) na GitHubu.

Knihovna obsahuje `FrameGrabber` třídu, která implementuje dříve diskutovaný systém výrobce a spotřebitele pro zpracování video snímků z webové kamery. Uživatelé mohou určit přesnou formu volání rozhraní API a třída používá události, aby volající kód věděl, kdy je získán nový rámec nebo když je k dispozici nový výsledek analýzy.

Pro ilustraci některých možností jsme poskytli dvě ukázkové aplikace, které používají knihovnu. 

První ukázková aplikace je jednoduchá konzolová aplikace, která popadne snímky z výchozí webové kamery a poté je odešle službě Face pro detekci obličeje. Zjednodušená verze aplikace je reprodukována v následujícím kódu:

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

Druhá ukázková aplikace je o něco zajímavější. Umožňuje zvolit, které rozhraní API se má na snímcích videa volat. Na levé straně aplikace zobrazuje náhled živého videa. Na pravé straně překryje nejnovější výsledek rozhraní API na odpovídající snímek.

Ve většině režimů je viditelná prodleva mezi živým videem vlevo a vizualizovanou analýzou vpravo. Toto zpoždění je čas potřebný k volání rozhraní API. Výjimkou je režim "EmotionsWithClientFaceDetect", který provádí detekci obličeje místně v klientském počítači pomocí OpenCV před odesláním jakékoli image do služby Azure Cognitive Services. 

Pomocí tohoto přístupu můžete okamžitě vizualizovat detekované plochy. Potom můžete aktualizovat emoce později, po vrátí volání rozhraní API. To ukazuje možnost "hybridního" přístupu. To znamená, že některé jednoduché zpracování lze provést na straně klienta a pak cognitive services API lze rozšířit toto zpracování s pokročilejší analýzy v případě potřeby.

![Aplikace LiveCameraSample zobrazující obraz se značkami](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>Integrace vzorků do základu kódu

Chcete-li začít s touto ukázkou, postupujte takto:

1. Získejte klíče rozhraní API pro zpracování obrazu v oblasti [Předplatná](https://azure.microsoft.com/try/cognitive-services/). Pro analýzu snímků videa jsou příslušnými službami:
    - [Počítačové vidění](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [Tvář](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Klonujte úložiště [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub.

3. Otevřete ukázku v Sadě Visual Studio 2015 nebo novější a pak vytvořte a spusťte ukázkové aplikace:
    - Pro BasicConsoleSample je klávesa Face pevně zakódována přímo v [souboru BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - V části LiveCameraSample zadejte klávesy do podokna **Nastavení** aplikace. Klíče jsou trvalé napříč relacemi jako uživatelská data.

Až budete připraveni k integraci ukázek, odkaz na knihovnu VideoFrameAnalyzer z vlastních projektů.

Funkce videoframeanalyzeru pro rozpoznávání obrázků, hlasu, videa a textu využívají služby Azure Cognitive Services. Společnost Microsoft přijímá obrázky, zvuk, video a další data, která nahrajete (prostřednictvím této aplikace), a může je používat pro účely zlepšování služeb. Žádáme vás o pomoc při ochraně uživatelů, jejichž data vaše aplikace odesílá do služeb Azure Cognitive Services.

## <a name="summary"></a>Souhrn

V tomto článku jste se naučili, jak spustit analýzu v reálném čase na živých video streamech pomocí služeb Face a Computer Vision. Také jste se dozvěděli, jak můžete použít náš ukázkový kód, abyste mohli začít. Pokud chcete začít vytvářet aplikaci pomocí bezplatných klíčů rozhraní API, přejděte na [registrační stránku Služby Azure Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

Neváhejte a poskytněte zpětnou vazbu a návrhy v [úložišti GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/). Chcete-li poskytnout širší zpětnou vazbu od rozhraní API, přejděte na náš [web UserVoice](https://cognitive.uservoice.com/).

