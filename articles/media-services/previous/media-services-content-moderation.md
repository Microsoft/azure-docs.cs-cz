---
title: Použít Azure média obsahu moderátora ke zjištění možných obsah pro dospělé a zájem | Microsoft Docs
description: Video přerušování pomůže zjistit potenciální obsah pro dospělé a zájem v videa.
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
ms.date: 02/06/2018
ms.author: sajagtap
ms.openlocfilehash: e44308f38a138c0e186e41fc8310f8b480cd4e09
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788666"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Použít ke zjištění možných obsah pro dospělé a zájem moderátora obsah média Azure

## <a name="overview"></a>Přehled
**Azure média obsahu moderátora** procesor médií (PP) umožňuje používat s asistencí počítač přerušování pro videa. Můžete například zjistit možné obsah pro dospělé a zájem v videa a zkontrolujte označení obsah týmy lidského přerušování.

**Azure média obsahu moderátora** MP je aktuálně ve verzi Preview.

Tento článek obsahuje údaje o **Azure média obsahu moderátora** a ukazuje, jak pomocí sady Media Services SDK pro .NET.

## <a name="content-moderator-input-files"></a>Obsahu moderátora vstupní soubory
Video soubory. V současné době jsou podporovány následující formáty: MP4, MOV a WMV.

## <a name="content-moderator-output-files"></a>Obsahu moderátora výstupní soubory
Moderované výstup ve formátu JSON zahrnuje automatické rozpoznání snímky a klíčových snímků. Klíčové snímky jsou vráceny s jistotou skóre pro možné obsah pro dospělé nebo zájem. Také obsahují logický příznak indikující, zda se doporučuje kontrolu. Příznak zkontrolujte doporučení je přiřadit hodnoty na základě interní prahových hodnot pro skóre pro dospělé a zájem.

## <a name="elements-of-the-output-json-file"></a>Elementy výstupního souboru JSON

Úloha vytvoří výstupní soubor JSON, který obsahuje metadata o zjištěných snímky a klíčových snímků a jestli obsahovat obsah pro dospělé nebo zájem.

Výstup JSON obsahuje následující prvky:

### <a name="root-json-elements"></a>Elementy kořenové JSON

| Element | Popis |
| --- | --- |
| verze |Verze obsahu moderátora. |
| Časová osa |"Rysky" za sekundu videa. |
| Posun |Časový posun pro časová razítka. Ve verzi 1.0 rozhraní API, Video bude tato hodnota vždy 0. Tato hodnota může změnit v budoucnu. |
| kmitočet snímků |Počet snímků za sekundu videa. |
| Šířka |Šířka rámečku video výstup v pixelech.|
| Výška |Výška rámečku video výstup v pixelech.|
| Agent |Doba trvání vstupní video, v "rysky." |
| [fragmenty](#fragments-json-elements) |Metadata se blokové až do různých segmentů názvem fragmenty. Každý fragment je automatické rozpoznání snímek start, doba trvání, číslo intervalu a událostí. |

### <a name="fragments-json-elements"></a>Fragmenty elementy JSON

|Element|Popis|
|---|---|
| start |Čas zahájení první událost v "rysky." |
| Doba trvání |Délka fragment v "rysky." |
| interval |Interval každé události položky v rámci fragment v "rysky." |
| [Události](#events-json-elements) |Každá událost představuje klip a každý klip obsahuje klíčové snímky zjištěn a sledují v rámci této doby trvání. Je pole události. Vnější pole představuje jeden časový interval. Vnitřní pole se skládá z 0 nebo více událostí, které bylo provedeno v tomto bodě v čase.|

### <a name="events-json-elements"></a>Elementy JSON události

|Element|Popis|
|---|---|
| reviewRecommended | `true` nebo `false` podle toho, jestli **adultScore** nebo **racyScore** překročit prahovou hodnotu interní. |
| adultScore | Spolehlivosti skóre pro možné obsah pro dospělé, na škále od 0,00 a 0,99. |
| racyScore | Spolehlivosti skóre pro možné zájem obsah na škále od 0,00 a 0,99. |
| index | index rámečku na škále od první snímek indexu na poslední index rámečku. |
| časové razítko | Umístění rámečku v "rysky." |
| shotIndex | Index nadřazené snímek. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Obsahu přerušování rychlý start a ukázkový výstup

### <a name="task-configuration-preset"></a>Konfigurace úlohy (přednastavených)
Při vytváření úlohy s **Azure média obsahu moderátora**, je nutné zadat jedno z přednastavení konfigurace. Následující předvolba konfigurace je jen pro obsahu přerušování.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>Ukázka kódu rozhraní .NET

Následující ukázka kódu .NET používá ke spuštění úlohy obsahu moderátora sady Media Services .NET SDK. Na média trvá jako vstup, který obsahuje videa a být moderovaná služby Asset.
Najdete v článku [video rychlý start obsahu moderátora](../../cognitive-services/Content-Moderator/video-moderation-api.md) úplný zdrojový kód a projekt Visual Studio.


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
[Azure Media Services Analytics – přehled](media-services-analytics-overview.md)

[Ukázky služby Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Další postup

Další informace o obsahu moderátora [video přerušování a zkontrolujte řešení](../../cognitive-services/Content-Moderator/video-moderation-human-review.md).

Získat úplný zdrojový kód a projekt Visual Studio z [rychlý start video přerušování](../../cognitive-services/Content-Moderator/video-moderation-api.md). 

Zjistěte, jak vygenerovat [zkontroluje video](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) z moderované výstupu, a [střední přepisy](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) v rozhraní .NET.

Podívejte se na podrobné .NET [video přerušování a zkontrolujte kurzu](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md). 
