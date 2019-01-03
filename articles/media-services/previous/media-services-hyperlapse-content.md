---
title: Zpracování multimediálních souborů pomocí Azure Media Hyperlapse | Dokumentace Microsoftu
description: Azure Media Hyperlapse vytvoří smooth vypršelo čas videa z první, kdo nebo akce fotoaparátu obsahu. Toto téma ukazuje, jak používat Media Indexer.
services: media-services
documentationcenter: ''
author: asolanki
manager: johndeu
editor: ''
ms.assetid: 37d54db6-9cf3-4ae9-b3c6-0d29c744e965
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2018
ms.author: adsolank
ms.openlocfilehash: 3b361202d2d80f879ae6d23ec29782bc6f837f96
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632870"
---
# <a name="hyperlapse-media-files-with-azure-media-hyperlapse"></a>Zpracování multimediálních souborů pomocí Azure Media Hyperlapse

> [!NOTE]
> Tuto funkci verze preview služby Azure Media Services brzo skončí. Od 19. prosince 2018, Media Services se už provést změny nebo vylepšení Media Hyperlapse. 29. března 2019 je vyřazený a už nebudou dostupné.

Azure Media Hyperlapse je média procesoru (sady Management Pack), který vytvoří smooth vypršelo čas videa z první, kdo nebo akce fotoaparátu obsahu.  Na stejné úrovni založené na cloudu k [založené na telefonu Hyperlapse Mobile a Hyperlapse Pro klasické pracovní plochy Microsoft Research](https://aka.ms/hyperlapse), Microsoft Hyperlapse pro Azure Media Services využívá velkém měřítku zpracování médií Azure Media Services Platforma horizontálně škálovat a paralelní zpracování hromadně Hyperlapse zpracování.

> [!IMPORTANT]
> Microsoft Hyperlapse je navržena pro spolupráci na první, kdo obsahu s přesunutí fotoaparát. I když se můžete stále kamer i nadále fungovat, výkon a kvalita procesor médií Azure Media Hyperlapse nemůže být zaručena pro jiné typy obsahu.
> 
> 

Azure Media Hyperlapse úloha přijímá jako vstupní asset soubor MP4, MOV nebo WMV spolu s konfigurační soubor, který určuje, které snímky z videa musí být čas vypršelo a jaké rychlosti (například prvních 10 000 snímků na 2 x).  Výstup je stabilizovaná a čas vypršelo interpretace vstupu videa.

## <a name="hyperlapse-an-asset"></a>Hyperlapse prostředek
Nejprve budete muset nahrát požadované vstupní soubor do služby Azure Media Services.  Další informace o konceptech, které jsou spojené s ukládání a správě obsahu najdete v článku [správy obsahu článku](media-services-portal-vod-get-started.md).

### <a id="configuration"></a>Konfigurace nastavení pro Hyperlapse
Jakmile váš obsah v účtu Media Services, je potřeba vytvořit předvolbu vaší konfigurace.  Následující tabulka popisuje pole zadané uživatelem:

| Pole | Popis |
| --- | --- |
| StartFrame |Rámce, na kterém chcete spustit zpracování Microsoft Hyperlapse. |
| NumFrames |Počet snímků ke zpracování |
| Rychlost |Faktor s násobek zrychlení vstupního videa. |

Následuje příklad konfiguračního souboru splňující podmínky v XML a JSON:

**Přednastavení XML:**
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>
```

**Přednastavení JSON:**
```json
    {
        "Version":1.0,
        "Sources": [
            {
                "StartFrame":0,
                "NumFrames":2147483647
            }
        ],
        "Options": {
            "Speed":1,
            "Stabilize":false
        }
    }
```

### <a id="sample_code"></a> Technologii Microsoft Hyperlapse s AMS .NET SDK
Následující metoda nahraje soubor média jako prostředek a vytvoří úlohu s mediálním procesorem Azure Media Hyperlapse.

> [!NOTE]
> CloudMediaContext byste už měli mít v oboru s názvem "kontext" pro tento kód pro práci.  Další informace o tom, [správy obsahu článku](media-services-dotnet-get-started.md).
> 
> [!NOTE]
> Argument řetězce "hyperConfig" očekává se splňující podmínky konfigurace přednastavení ve formátu JSON nebo XML, jak je popsáno výše.
> 
> 

```csharp
        static bool RunHyperlapseJob(string input, string output, string hyperConfig)
        {
            // create asset with input file
            IAsset asset = context
            .Assets
            .CreateAssetAndUploadSingleFile(input, "My Hyperlapse Input", AssetCreationOptions.None);

            // grab instances of Azure Media Hyperlapse MP
            IMediaProcessor mp = context
            .MediaProcessors
            .GetLatestMediaProcessorByName("Azure Media Hyperlapse");

            // create Job with Hyperlapse task
            IJob job = context
            .Jobs
            .Create(String.Format("Hyperlapse {0}", input));

            if (String.IsNullOrEmpty(hyperConfig))
            {
            // config cannot be empty
            return false;
            }

            hyperConfig = File.ReadAllText(hyperConfig);

            ITask hyperlapseTask = job.Tasks.AddNew("Hyperlapse task",
            mp,
            hyperConfig,
            TaskOptions.None);
            hyperlapseTask.InputAssets.Add(asset);
            hyperlapseTask.OutputAssets.AddNew("Hyperlapse output",
            AssetCreationOptions.None);

            job.Submit();

            // Create progress printing and querying tasks
            Task progressPrintTask = new Task(() =>
            {

            IJob jobQuery = null;
            do
            {
                var progressContext = context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}\t{2}",
                DateTime.Now,
                jobQuery.State,
                jobQuery.Tasks[0].Progress));
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
                return false;                  
            }

        DownloadAsset(job.OutputMediaAssets.First(), output);
        return true;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }


    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }
```

### <a id="file_types"></a>Podporované typy souborů
* MP4
* MOV
* WMV

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Související odkazy
[Azure Media Services Analytics – přehled](media-services-analytics-overview.md)

[Ukázky Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

