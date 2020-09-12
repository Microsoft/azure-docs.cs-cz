---
title: Použití Azure Media Content Moderator k detekci možného obsahu pro dospělé a pikantní | Microsoft Docs
description: Procesor Azure Media Content Moderator Media pomáhá detekovat potenciální obsah pro dospělé a pikantní ve videích.
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
ms.date: 03/14/2019
ms.author: sajagtap
ms.custom: devx-track-csharp
ms.openlocfilehash: 395d2239970e7efbf6973d6262df0e049306584b
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266693"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Použití Azure Media Content Moderator k detekci možného obsahu pro dospělé a pikantní

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Procesor **Azure media Content moderator** Media bude vyřazený. Informace o datu vyřazení najdete v tématu [starší verze součástí](legacy-components.md) .

## <a name="overview"></a>Přehled
Procesor **Azure media Content moderator** Media procesor (MP) umožňuje používat pro vaše videa moderování s asistencí počítače. Ve videích například můžete chtít detekovat potenciálně nevhodný obsah nebo obsah pro dospělé a označený obsah nechat zkontrolovat týmy lidského moderování.

Sada **Azure Media Content moderator** MP je momentálně ve verzi Preview.

Tento článek obsahuje podrobné informace o  **Azure Media Content moderator** a ukazuje, jak ho používat s Media Services SDK pro .NET.

## <a name="content-moderator-input-files"></a>Content Moderator vstupní soubory
Videosoubory. V současné době jsou podporovány následující formáty: MP4, MOV a WMV.

## <a name="content-moderator-output-files"></a>Content Moderator výstupní soubory
Moderovaný výstup ve formátu JSON obsahuje automaticky rozpoznané snímky a klíčové snímky. Klíčové snímky jsou vráceny s hodnocením spolehlivosti pro možný obsah pro dospělé nebo pikantní. Obsahují také logický příznak označující, zda je doporučena revize. Příznak přezkoumání doporučení se přiřazuje hodnotám na základě vnitřních prahových hodnot pro dospělé a pikantní skóre.

## <a name="elements-of-the-output-json-file"></a>Prvky výstupního souboru JSON

Úloha vytvoří výstupní soubor JSON, který obsahuje metadata zjištěných snímků a klíčových snímků a zda obsahují obsah pro dospělé nebo pikantní.

Výstupní JSON obsahuje následující prvky:

### <a name="root-json-elements"></a>Kořenové elementy JSON

| Element | Popis |
| --- | --- |
| verze |Verze Content Moderator. |
| měřítk |"Takty" za sekundu videa. |
| posun |Posun času pro časová razítka. Ve verzi 1,0 rozhraní API pro video bude tato hodnota vždycky 0. Tato hodnota se může v budoucnu změnit. |
| snímků |Počet snímků ve videu za sekundu. |
| šířka |Šířka výstupního snímku videa (v pixelech)|
| výška |Výška výstupního snímku videa v pixelech|
| totalDuration |Doba trvání vstupního videa v části "osové". |
| [svalovin](#fragments-json-elements) |Metadata jsou rozdělená do různých segmentů s názvem fragmenty. Každý fragment je automaticky zjištěný snímek s počátečním, dobou trvání, číslem intervalu a událostmi. |

### <a name="fragments-json-elements"></a>Fragmenty – elementy JSON

|Element|Popis|
|---|---|
| start |Čas zahájení první události v "taktech". |
| doba trvání |Délka fragmentu v "taktech". |
| interval |Interval každé položky události v rámci fragmentu v části "osové". |
| [událost](#events-json-elements) |Každá událost představuje klip a každý klip obsahuje klíčové snímky zjištěné a sledované během této doby trvání. Jedná se o pole událostí. Vnější pole představuje jeden časový interval. Vnitřní pole se skládá z 0 nebo více událostí, které se v tomto bodu v čase odehrály.|

### <a name="events-json-elements"></a>Události – elementy JSON

|Element|Popis|
|---|---|
| reviewRecommended | `true` nebo `false` v závislosti na tom, zda **AdultScore** nebo **racyScore** překročí vnitřní prahové hodnoty. |
| adultScore | Hodnocení spolehlivosti pro možný obsah pro dospělé, na škále 0,00 až 0,99. |
| racyScore | Hodnocení spolehlivosti možného obsahu pikantní na škále 0,00 až 0,99. |
| index | index rámce na stupnici od prvního indexu snímku až po poslední index snímku. |
| časové razítko | Umístění rámce v "taktech". |
| shotIndex | Index nadřazeného snímku |


## <a name="content-moderation-quickstart-and-sample-output"></a>Rychlý Start a ukázkový výstup pro moderování obsahu

### <a name="task-configuration-preset"></a>Konfigurace úlohy (předvolba)
Při vytváření úlohy pomocí **Azure Media Content moderator**musíte zadat předvolby konfigurace. Následující předvolba konfigurace je jenom pro moderování obsahu.

```json
{
    "version":"2.0"
}
```

### <a name="net-code-sample"></a>Ukázka kódu .NET

Následující ukázka kódu .NET používá sadu Media Services .NET SDK ke spuštění úlohy Content Moderator. Přebírá Asset Media Services jako vstup, který obsahuje video, které se má moderováno.
Úplný zdrojový kód a projekt sady Visual Studio najdete v tématu [rychlý Start pro Content moderator video](../../cognitive-services/Content-Moderator/video-moderation-api.md) .


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
```

Úplný zdrojový kód a projekt sady Visual Studio najdete v části [rychlý Start pro Content moderator video](../../cognitive-services/Content-Moderator/video-moderation-api.md).

### <a name="json-output"></a>Výstup JSON

Následující příklad výstupu Content Moderator JSON byl zkrácen.

> [!NOTE]
> Umístění klíčového snímku v sekundách = časové razítko nebo časová osa

```json
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

## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Přehled analýzy Azure Media Services](media-services-analytics-overview.md)

[Ukázky Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak se vylepší [a Prohlédněte si řešení](../../cognitive-services/Content-Moderator/video-moderation-human-review.md)Content moderator 's

Získání úplného zdrojového kódu a projektu sady Visual Studio z rychlého startu pro zobrazení pro [moderování videa](../../cognitive-services/Content-Moderator/video-moderation-api.md) 

Naučte se generovat [recenze videí](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) z moderovaného výstupu a [mírného přepisu](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) v .NET.

Projděte si podrobného [kurzu pro moderování a kontrolu videa](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md).NET. 
