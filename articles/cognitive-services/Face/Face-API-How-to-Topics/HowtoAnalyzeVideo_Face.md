---
title: 'Příklad: analýza videa v reálném čase – obličej'
titleSuffix: Azure Cognitive Services
description: Využijte službu obličeje k provádění analýz téměř v reálném čase u snímků pořízených z živého streamu videa.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 730946a0c581be4697c0f45c8bdeb1d38f0ca23d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856384"
---
# <a name="example-how-to-analyze-videos-in-real-time"></a>Příklad: Analýza videí v reálném čase

Tato příručka ukazuje, jak provádět analýzu snímků z živého video streamu v téměř reálném čase. Takový systém funguje následovně:

- Pořídí snímky ze zdroje videa.
- Vybere snímky, které se mají analyzovat.
- Odešle tyto snímky do rozhraní API.
- Přijme jednotlivé výsledky analýzy vrácené z volání rozhraní API.

Tyto ukázky jsou napsané v jazyce C# a kód najdete na GitHubu tady: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) .

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

Tento kód spouští jednotlivé analýzy v samostatných úlohách, které můžou běžet na pozadí, zatímco se nadále zachytávají nové snímky. Pomocí této metody se vyhnete zablokování hlavního vlákna při čekání na vrácení volání rozhraní API, ale ztratili jsme některé záruky, které poskytuje jednoduchá verze. Paralelní může probíhat více volání rozhraní API a výsledky se můžou vrátit v nesprávném pořadí. Tento přístup může navíc znamenat, že několik vláken použije funkci ConsumeResult() souběžně, což může být nebezpečné, pokud tato funkce není bezpečná pro přístup z více vláken. Tento jednoduchý kód navíc nesleduje vytvářené úlohy, takže výjimky bez povšimnutí zmizí. Posledním krokem je proto přidat vlákno "příjemce", které bude sledovat úlohy analýzy, vyvolat výjimky, ukončit dlouhotrvající úlohy a zajistit, aby výsledky byly spotřebovány ve správném pořadí.

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

### <a name="getting-started"></a>začínáme

Pokud chcete svou aplikaci začít používat co nejrychleji, budete používat flexibilní implementaci systému popsanou výše. Chcete-li získat přístup k kódu, přejděte na [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis) .

Tato knihovna obsahuje třídu FrameGrabber, která implementuje výše popsaný systém producent-příjemce ke zpracování snímků videa z webkamery. Uživatel může zadat přesný tvar volání rozhraní API a třída použije události k tomu, aby volající kód věděl, kdy se získá nový rámec, nebo je k dispozici nový výsledek analýzy.

Některé možnosti ilustrují dvě ukázkové aplikace, které tuto knihovnu používají. První je jednoduchá Konzolová aplikace a zjednodušená verze je reprodukována níže. Přikládá snímky z výchozí webové kamery a odesílá je do služby obličeje pro rozpoznávání tváře.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/analyze.cs":::

Druhá ukázková aplikace je o něco zajímavější a umožňuje zvolit, které rozhraní API se má na snímky videa zavolat. Tato aplikace zobrazuje na levé straně náhled živého videa a na pravé straně ukazuje poslední výsledek rozhraní API překrývající odpovídající snímek.

Ve většině režimů bude mezi živým videem vlevo a vizualizovanou analýzou vpravo viditelné zpoždění. Toto zpoždění představuje dobu, jakou trvá volání rozhraní API. Jedinou výjimkou je režim "EmotionsWithClientFaceDetect", který provádí detekci tváře místně na klientském počítači pomocí OpenCV před odesláním jakýchkoli imagí Cognitive Services. Tímto způsobem můžeme vizualizovat zjištěnou plošku hned a následně aktualizovat emoce po návratu volání rozhraní API. Toto je příklad "hybridního" přístupu, kde může klient provádět určité jednoduché zpracování a rozhraní API služeb Cognitive Services může toto rozšíření rozšířit o pokročilejší analýzu v případě potřeby.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Integrace do základu kódu

Pokud chcete s touto ukázkou začít, postupujte takto:

1. Vytvořte [účet Azure](https://azure.microsoft.com/free/cognitive-services/). Pokud ho už máte, můžete přejít k dalšímu kroku.
2. Vytvořte prostředky pro Počítačové zpracování obrazu a tvář v Azure Portal, abyste získali svůj klíč a koncový bod. Při instalaci nezapomeňte vybrat bezplatnou úroveň (F0).
   - [Počítačové zpracování obrazu](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
   - [Tvář](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) Po nasazení prostředků klikněte na **Přejít k prostředku** a Shromážděte svůj klíč a koncový bod pro každý prostředek. 
3. Naklonujte úložiště GitHub [-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) GitHub.
4. Otevřete ukázku v aplikaci Visual Studio a sestavte a spusťte ukázkové aplikace:
    - V případě BasicConsoleSample je klíč obličeje pevně zakódovaný přímo v [BasicConsoleSample/program. cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - U aplikace LiveCameraSample se klíče zadávají do panelu s nastavením aplikace. Uloží se pro následné relace jako uživatelská data.
        

Až budete připraveni na integraci, **odkazujte na knihovnu VideoFrameAnalyzer ze svých vlastních projektů.** 

## <a name="summary"></a>Souhrn

V této příručce jste zjistili, jak spustit analýzu v reálném čase v reálném čase pomocí rozhraní API pro obličeje, Počítačové zpracování obrazu a emoce a jak začít používat náš vzorový kód.

V [úložišti GitHubu](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) můžete poskytnout zpětnou vazbu a návrhy nebo pro širší názory na rozhraní API na [webu UserVoice](https://cognitive.uservoice.com/).

## <a name="related-topics"></a>Související témata
- [Postup rozpoznání tváří v obrázku](HowtoDetectFacesinImage.md)
