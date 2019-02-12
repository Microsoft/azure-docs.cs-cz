---
title: Zjistit možné obsahu pro dospělé nebo pikantního obsahu pomocí Azure Media Content Moderator | Dokumentace Microsoftu
description: Moderování videa pomáhá detekovat potenciální obsahu pro dospělé nebo pikantního obsahu ve videích.
services: media-services
documentationcenter: ''
author: sanjeev3
manager: mikemcca
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/08/2019
ms.author: sajagtap
ms.openlocfilehash: aba7d2ff73fc1fdca6f57742582b38662177012d
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55995114"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Použití Azure Media Content Moderator rozpoznat možné obsahu pro dospělé nebo pikantního obsahu 

## <a name="overview"></a>Přehled
**Azure Media Content Moderator** procesor médií (PP) vám umožní použít moderování s podporou počítače pro svá videa. Můžete například zjistit možné obsahu pro dospělé nebo pikantního obsahu ve videích a zkontrolovat obsah s příznakem vaše týmy lidské moderování.

**Azure Media Content Moderator** sady Management Pack je aktuálně ve verzi Preview.

Tento článek obsahuje podrobnosti o **Azure Media Content Moderator** a ukazuje, jak používat ve službě Media Services SDK pro .NET.

## <a name="content-moderator-input-files"></a>Content Moderator vstupních souborů
Video soubory. V současné době jsou podporovány následující formáty: MP4, MOV a WMV.

## <a name="content-moderator-output-files"></a>Content Moderator výstupní soubory
Moderované výstup ve formátu JSON obsahuje automaticky rozpoznané snímky a klíčové snímky. Klíčové snímky jsou vráceny pomocí skóre spolehlivosti pro možný obsah pro dospělé nebo pikantního. Také zahrnují příznak logické hodnoty označující, zda se doporučuje kontrolu. Příznak zkontrolujte doporučení se přiřadí hodnoty na základě interní prahových hodnot pro dospělé nebo pikantního skóre.

## <a name="elements-of-the-output-json-file"></a>Prvky výstupního souboru JSON

Úloha vytvoří výstupní soubor JSON, který obsahuje metadata o zjištěných snímky a klíčové snímky a určuje, zda obsahují pro dospělé nebo pikantního obsahu.

Ve výstupu JSON obsahuje následující prvky:

### <a name="root-json-elements"></a>JSON kořenové prvky

| Element | Popis |
| --- | --- |
| version |Verze Content Moderatoru. |
| Časová osa |"Značky" za sekundu videa. |
| Posun |Posun času pro časová razítka. Ve verzi 1.0 rozhraní API pro Video bude tato hodnota vždy 0. Tato hodnota může v budoucnu změnit. |
| snímkovou |Počet snímků ve videu za sekundu. |
| Šířka |Šířka rámce výstup videa, v pixelech.|
| Výška |Výška snímek výstup videa, v pixelech.|
| totalDuration |Doba trvání vstupního videa v "impulzech." |
| [fragmenty](#fragments-json-elements) |Metadata je rozdělený do bloků dat až do různých segmentů zvaných fragmenty. Každý fragment je snímek automaticky detekované pomocí spuštění, dobu trvání, číslo intervalu a události. |

### <a name="fragments-json-elements"></a>Elementy fragmentů JSON

|Element|Popis|
|---|---|
| start |Čas zahájení první událost v "impulzech." |
| doba trvání |Délka fragment v "impulzech." |
| interval |Interval každý záznam událostí v rámci fragment v "impulzech." |
| [Události](#events-json-elements) |Každá událost představuje klipu a každý klip obsahuje klíčové snímky zjištěna a sledovány v rámci této dobu trvání. Je to pole událostí. Vnější pole představuje jeden časový interval. Vnitřní pole se skládá z 0 nebo více událostí, které se v tomto bodu v čase odehrály.|

### <a name="events-json-elements"></a>Elementy JSON událostí

|Element|Popis|
|---|---|
| reviewRecommended | `true` nebo `false` podle toho, jestli **adultScore** nebo **racyScore** překročí interní prahové hodnoty. |
| adultScore | Skóre spolehlivosti pro možný obsah pro dospělé, na škále od 0,00 a 0,99. |
| racyScore | Skóre spolehlivosti možné pikantní obsah, na škále od 0,00 a 0,99. |
| index | index rámce na škále od prvního rámce indexu na poslední index snímku. |
| časové razítko | Umístění tohoto rámce v "impulzech." |
| shotIndex | Index nadřazený snímek. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Rychlý start a ukázkový výstup moderování obsahu

### <a name="task-configuration-preset"></a>Konfigurace úlohy (nastavení)
Při vytváření úlohy s **Azure Media Content Moderator**, je nutné zadat nastavení konfigurace. Následující nastavení konfigurace je jen pro moderování obsahu.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>Ukázku kódu .NET

Následující vzorový kód .NET používá ke spuštění úlohy Content Moderator Media Services .NET SDK. Média trvá jako vstup, který obsahuje video a možné Moderovat služby Asset.
Najdete v článku [videa rychlý start Content Moderator](../../cognitive-services/Content-Moderator/video-moderation-api.md) pro úplný zdrojový kód a projekt aplikace Visual Studio.


```csharp
    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
    static void RunContentModeratorJob(IAsset asset)
    {
        // Grab the presets
        string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

        // grab instance of Azure Media Content Moderator MP
        IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

        // create Job with Content Moderator task
        IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
                asset.AssetFiles.First() + "_" + Guid.NewGuid()));

        ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                mp, configuration,
                TaskOptions.None);
        contentModeratorTask.InputAssets.Add(asset);
        contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
            AssetCreationOptions.None);

        job.Submit();


        // Create progress printing and querying tasks
        Task progressPrintTask = new Task(() =>
        {
            IJob jobQuery = null;
            do
            {
                var progressContext = _context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                    .First();
                    Console.WriteLine(string.Format("{0}\t{1}",
                    DateTime.Now,
                    jobQuery.State));
                    Thread.Sleep(10000);
             }
             while (jobQuery.State != JobState.Finished &&
             jobQuery.State != JobState.Error &&
             jobQuery.State != JobState.Canceled);
        });
        progressPrintTask.Start();

        Task progressJobTask = job.GetExecutionProgressTask(
        CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            ErrorDetail error = job.Tasks.First().ErrorDetails.First();
            Console.WriteLine(string.Format("Error: {0}. {1}",
            error.Code,
            error.Message));
        }

        DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
    }

For the full source code and the Visual Studio project, check out the [Content Moderator video quickstart](../../cognitive-services/Content-Moderator/video-moderation-api.md).

### JSON output

The following example of a Content Moderator JSON output was truncated.

> [!NOTE]
> Location of a keyframe in seconds = timestamp/timescale

    {
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]
    }
    ]
    }
```

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Azure Media Services Analytics Overview](media-services-analytics-overview.md)

[Azure Media Analytics demos](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Další postup

Další informace o Content Moderatoru [řešením pro video moderování a kontrola](../../cognitive-services/Content-Moderator/video-moderation-human-review.md).

Získejte úplný zdrojový kód a projekt sady Visual Studio z [moderování videa quickstart](../../cognitive-services/Content-Moderator/video-moderation-api.md). 

Zjistěte, jak generovat [video kontroly](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) moderované výstupu, a [střední záznamy o studiu](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) v rozhraní .NET.

Podívejte se na podrobné .NET [výukovém videu moderování a kontrola](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md). 
